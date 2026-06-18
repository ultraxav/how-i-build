# Layered Architecture

A reusable discipline for backend Python services. This document defines the
architecture; the project constitution references it and makes it binding.

## The one rule

**Dependencies point inward only. Domain imports nothing from the outside
world; everything points toward it.**

Every other rule in this document follows from that one. If a change satisfies
the dependency rule, it is structurally sound. If it violates the rule, it is
wrong regardless of how convenient it is.

## The four layers

Code is organized by layer, not by topic. A module's folder is determined by
what kind of work it does, not by what subject it concerns.

```
src/<package>/
    domain/          pure computation, no I/O
    application/     orchestration, no I/O
    adapters/        all I/O
    api/             HTTP delivery, thin
```

### domain

The rules and calculations that are true regardless of how the service runs.
Given an input, it returns a derived fact. It does not know where data came
from or where it is going.

- Pure functions and pure data types.
- No network, no disk, no subprocess, no database.
- May use computation libraries (numpy, math, data-modeling libraries) and
  standard library. May use Pydantic for data types.
- May not import any I/O or delivery framework, and may not import from any
  other layer.

A function that computes an answer from its inputs belongs here.

### application

The logic of what happens in what order to fulfill a request. It coordinates
domain calculations and the I/O performed by adapters, but performs no I/O
itself.

- Orchestration: the sequence of steps, the recipe.
- Depends on domain. Receives I/O capabilities through ports (interfaces it
  defines), never by importing adapters directly.
- May not import any I/O or delivery framework.

A function that says "first this, then that, then save" belongs here.

### adapters

Everything that touches the outside world. Databases, HTTP clients,
subprocesses, filesystem, third-party APIs, ML model loading, auth providers.

- All I/O lives here and only here.
- Implements the ports that application defines.
- May import application and domain.

### api

The delivery edge. Receives a request, hands it to application, serializes the
response. Thin by design.

- HTTP framework lives here (and in adapters where infrastructure requires it).
- Parses and validates incoming requests, wires adapters to ports at startup,
  injects them into application.
- Contains no business logic. If a router is doing orchestration, that logic
  belongs in application.

## Two kinds of business logic

Business logic spans two layers, and conflating them is the most common way
this architecture is misapplied.

- **Computational logic** (the calculations) lives in **domain**.
- **Orchestration logic** (the sequence and coordination) lives in
  **application**.

The test: does this code *compute an answer* or *coordinate a sequence*?
Computing is domain. Coordinating is application. A module being "about" a
subject does not make it domain; only being pure computation does.

## Dependency inversion

The obvious tension: application needs data, loading data is I/O, and
application may not do I/O. The resolution is the port.

The inner layer defines an interface describing what it needs. The outer layer
implements it. The inner layer depends on the abstraction it owns, not on the
concrete outer thing.

```python
# application/ports.py — inner layer owns the interface
class DataSource(Protocol):
    def load(self, key: str) -> Record: ...

# application/use_case.py — depends only on the port
def run(key: str, source: DataSource) -> Result:
    record = source.load(key)
    return compute(record)          # domain, pure

# adapters/concrete_source.py — outer layer implements the port
class ConcreteSource:
    def load(self, key: str) -> Record:
        ...                          # real I/O
```

The dependency arrow points inward even though data flows outward at runtime.
application defines the port; adapters implement it; application never names
the concrete adapter. Construction happens at the api edge and is injected in.

This is what makes adapters swappable: the real adapter, a fake for tests, or a
different technology, all without touching domain or application.

## What "forbidden" means

The fence around domain and application forbids I/O and delivery frameworks
specifically, not libraries in general. A library is forbidden in the inner
layers if it talks to the outside world or binds the code to a delivery or
storage technology.

Forbidden in domain and application: web frameworks, ORMs and database drivers,
HTTP clients, cache and queue clients, cloud and auth SDKs, ML-model runtimes,
anything wrapping a subprocess.

Allowed in the inner layers: computation and data-modeling libraries that do no
I/O.

Note: a computation library is only allowed in domain when used for its pure
functions. If such a library also offers I/O entry points (for example, file
loaders), those entry points are adapter-layer concerns even though the library
itself is permitted.

## Enforcement

This discipline is enforced by tooling, not by convention. A rule that nothing
checks erodes over time.

[import-linter](https://import-linter.readthedocs.io) expresses the rule as
contracts that fail the build when an arrow points the wrong way. The folder
names above are the package paths the contracts bind to; folders and contracts
are the same decision expressed twice and must match exactly.

```ini
[importlinter]
root_package = <package>

[importlinter:contract:layered-architecture]
name = Dependencies point inward only
type = layers
layers =
    api
    application
    domain
containers =
    <package>

[importlinter:contract:domain-is-pure]
name = Domain imports no I/O frameworks
type = forbidden
source_modules =
    <package>.domain
forbidden_modules =
    # web, orm, http, cache, cloud, ml-runtime frameworks

[importlinter:contract:application-is-pure]
name = Application does no direct I/O
type = forbidden
source_modules =
    <package>.application
forbidden_modules =
    # same list as domain
```

`lint-imports` runs in CI and exits non-zero on any violation. That non-zero
exit is what makes the discipline enforced rather than aspirational.

## What enforcement cannot do

The linter checks import direction. It cannot judge whether a module's contents
belong in the layer it sits in. A thin passthrough that hides orchestration in
an adapter, or a calculation misfiled in application, passes the linter and
still violates the spirit of the architecture. The import direction is
mechanical; the contents stay a review question.
