# Frontend Architecture

A reusable discipline for React frontends that talk to a separate API. This
document defines the architecture; the project constitution references it and
makes it binding. Its test is that the same structure appears in every project.

## Scope and stack

These projects are single-page React applications that call a separate backend
over HTTP. They are not server-rendered and do not use a full-stack
meta-framework.

- **React + TypeScript + Vite.** TypeScript is mandatory, in strict mode. Vite
  is the build tool. No Next.js, no Remix, no meta-framework.
- **TanStack Query** for all server state.
- **React Router** for client-side routing.
- A single HTTP client module for all backend calls.

## The two rules

**1. Organize by feature, not by type.** Everything belonging to one feature
lives in that feature's folder. Code is grouped by what part of the product it
serves, not by what technical kind of file it is.

**2. Logic does not live in components.** Components render. Data fetching and
mutation live in hooks built on TanStack Query. Pure calculation lives in
framework-free functions. A component that fetches, polls, or computes inline is
a violation.

Every other rule follows from these two.

## Folder structure

```
src/
    app/             entry, router, providers, top-level shell
    features/        one folder per feature, self-contained
        <feature>/
            components/   UI specific to this feature
            hooks/        TanStack Query hooks for this feature
            api/          endpoint functions for this feature
            logic/        pure framework-free functions
            types.ts      types for this feature
    shared/          things used by more than one feature
        components/   reusable UI primitives (Button, Input, Modal)
        hooks/        reusable hooks
        lib/          the HTTP client, pure shared utilities
        types/        shared types
```

The rule for placement: if a thing belongs to one feature, it lives in that
feature's folder. The moment a second feature needs it, it moves up to
`shared/`. Nothing feature-specific lives in `shared/`; nothing shared lives
inside a feature.

This is the single most important convention for cross-project consistency.
"Which folder does this go in" must always have one obvious answer, because that
is what keeps file placement identical across projects and lets a behavior be
found by opening one folder.

## The layers inside a feature

A feature folder has the same internal shape every time, and the same dependency
direction as a backend service: inner layers know nothing of outer ones.

### logic/ — pure functions

Framework-free TypeScript. Takes input, returns output. Imports no React, no
TanStack Query, no HTTP client. This is where calculation and transformation
live: formatting, derivation, validation rules, anything that is "given this
data, compute that."

If a function does not need React to exist, it belongs here, not inlined in a
component. The audit-era habit of declaring `formatSeconds` or
`computeRepresentativeLevel` inside a screen file is exactly what this forbids.

### api/ — endpoint functions

Thin functions that call the shared HTTP client and return typed data. One
function per endpoint. No React. No caching logic (that is the hook's job). This
is the feature's I/O surface.

### hooks/ — server state

TanStack Query hooks (`useQuery`, `useMutation`) that wrap the api functions and
expose data, loading, and error state to components. All fetching, caching,
polling, invalidation, and optimistic update logic lives here and only here.

This is where the audit's hand-rolled patterns are replaced. Polling becomes a
`refetchInterval`, not a manual `setInterval`. Optimistic updates and
revert-on-failure use TanStack Query's mutation lifecycle, not bespoke state.

### components/ — UI

React components. They call hooks to get data and call mutation functions to
change it. They contain no fetching and no calculation. A component reads from
hooks, renders, and emits events. Nothing else.

## Server state vs client state

This distinction is a hard rule, because misusing it is the most common way this
architecture degrades.

- **Server state** is data that lives on the backend: anything fetched over
  HTTP. It is owned by TanStack Query. Never copy it into `useState`.
- **Client state** is UI state that never leaves the browser: which modal is
  open, sort order, selection, form drafts, toggles. It is owned by React
  (`useState`, `useReducer`, or Context). Never put it in the query cache.

TanStack Query owns server state. React owns client state. Code that blurs this
(caching UI state in queries, or mirroring fetched data into local state) is a
violation even though it runs fine.

## Talking to the backend

All HTTP goes through one client module in `shared/lib`. One configured instance
with a base URL, auth-token attachment, and a single place that normalizes error
shapes so every callsite sees the same error structure.

Feature `api/` functions call this client. Components and hooks never call
`fetch` or the client directly; they go through their feature's api functions,
wrapped by their feature's hooks. There is exactly one definition of how the app
talks to the backend, and it lives in one file.

Endpoints the browser fetches natively (file downloads, media elements,
server-sent events) will bypass the client by necessity. Keep those few cases
isolated and documented rather than spread around.

## TypeScript

TypeScript is mandatory and strict (`strict: true`). It is the frontend's
mechanical comprehension aid, the equivalent of an enforced contract: it makes
code self-documenting and catches API-shape drift at build time rather than by
hand.

- Every feature defines its data types in `types.ts`.
- API functions return typed data, not `any`.
- Shared types used across features live in `shared/types`.
- `any` is a smell to be justified, not a default.

## State management

No global state library by default. React's own tools plus TanStack Query cover
the cases:

- Server state: TanStack Query.
- Local UI state: `useState` / `useReducer` in the component.
- Cross-feature client state (auth, theme, feature flags): React Context, one
  provider per concern, named consistently.

A dedicated client-state library (Zustand and similar) is added only when a real
need appears, never by default. Most projects will not need one.

## What enforcement can and cannot do

TypeScript's compiler and ESLint enforce types and the React Hooks rules
mechanically. They do not enforce the feature-folder placement or the
"logic-not-in-components" rule. Those stay review questions and are the things to
check in review: is this in the right feature folder, and is there fetching or
calculation hiding inside a component that belongs in a hook or in logic.

A consistent ESLint config and `tsconfig` should ship with every project so the
mechanical parts never drift. The structural parts are held by this document and
by review.

## Summary of decisions

- React + TypeScript (strict) + Vite. No meta-framework.
- Feature-based folders. Shared area for cross-feature code only.
- Components render; hooks fetch; logic computes; api calls the client.
- TanStack Query owns server state; React owns client state.
- One HTTP client module for the whole app.
- No client-state library unless a real need appears.
