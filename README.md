# how-i-build

Notes, methodologies, and architecture disciplines I use to develop software
projects. This is a working record of how I approach building things, shared
openly in case any of it is useful to others.

These are opinions formed in practice, not universal prescriptions. They evolve
as I learn. Take what fits your context and leave the rest.

## What's here

### Architecture disciplines

Reusable, framework-level disciplines I apply across projects. Each is written
to be agnostic and portable: it defines a structure and the reasoning behind it,
not a single project's implementation.

- **Backend** — a layered architecture for Python services, where business
  logic stays free of frameworks and the dependency rule is enforced by tooling
  rather than convention.
- **Frontend** — a feature-based architecture for React + TypeScript apps that
  talk to a separate API.
- **Streamlit clients** — a deliberately thin discipline for Streamlit apps used
  as a frontend to an existing backend.

## Principles that run through all of it

- Choose the minimal tool, then add discipline on top, rather than adopting
  heavy frameworks for capability you won't use.
- A rule that nothing checks erodes. Prefer disciplines enforced by tooling over
  conventions held by goodwill.
- Keep documents agnostic and reusable. Project-specific detail lives with the
  project, not in the methodology.
- Separation of concerns and modularity are the goals; the structures here are
  specific, enforceable ways of reaching them.

## How to use this

Each discipline is a standalone document. Read the one relevant to what you're
building. They are written to be referenced directly (for example, from a
project's own constitution or contributing guide) and applied as-is or adapted.

## Status

This is a living repository. Documents are added and revised as the methodology
develops. Some disciplines are mature; others are early or still being worked
out.
