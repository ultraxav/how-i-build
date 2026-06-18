# Streamlit Client Architecture

A reusable discipline for Streamlit apps that act as a frontend to a separate
API. This document defines the architecture; the project constitution references
it and makes it binding.

This is deliberately short. A Streamlit app of this kind is a thin view: the
real architecture lives in the backend it talks to. The only job here is to keep
the view thin and the boundary clean.

## The one rule

**The Streamlit script presents. It does not compute, process, or hold business
logic. All of that lives behind the API.**

Streamlit calls the backend over HTTP, receives data, and displays it. Every
real decision has already happened behind the API. If a calculation or
transformation is needed, it belongs in the backend and is exposed as an
endpoint, not performed in the Streamlit script.

This is the same instinct as a pure domain layer, with almost nothing to enforce
locally, because the logic is not in this project at all. It is across the
boundary, in the backend, governed by the backend's own discipline.

## One place defines API calls

All calls to the backend go through a single client module: base URL, auth,
and a single place that normalizes error shapes. The Streamlit pages call this
module. They never issue raw HTTP calls scattered through the UI.

This is the one structural rule worth holding, because Streamlit's rerun model
tempts you to drop an inline request wherever data is needed. Resist that. There
is exactly one definition of how the app talks to the backend, and it lives in
one file.

## Thin pages, framework-free helpers

- Page scripts call the API module, format results, and render widgets.
- Any non-trivial display helper that does not need Streamlit (formatting,
  shaping data for a table) lives in a plain module that does not import
  Streamlit, so it stays testable and out of the rerun path.
- No business logic in either place. Display formatting only.

## Use Streamlit's own structure

Do not invent a folder architecture. Streamlit has a built-in multipage
convention; use it rather than fighting it.

```
app.py            entry / home page
pages/            one file per page (Streamlit multipage convention)
lib/
    api.py        the single API client module
    format.py     framework-free display helpers (optional)
```

Add to this only when the app genuinely grows. The framework is chosen for
speed; over-structuring it discards the reason it was chosen.

## Session state holds UI state only

Streamlit's session state holds UI state: selection, form drafts, which tab is
open, pagination position. It is not a data store. Real data comes fresh from
the API; it is not parked in session state and treated as a source of truth.

Server state lives behind the API. Session state owns only what never leaves the
browser session.

## What this document does not need

Because the logic lives in the backend, this discipline deliberately omits most
of what a full architecture would specify: no domain layer (it is in the
backend), no data-fetching library (a plain HTTP client suffices), no
enforcement tooling (the app is small and the one boundary is held by review,
not a linter). Adding any of that would be structure the situation does not
call for.

## Summary of decisions

- Streamlit is a thin view; all logic lives behind the API.
- One client module defines every backend call.
- Pages render; framework-free helpers format; neither holds business logic.
- Use Streamlit's native multipage structure; do not over-organize.
- Session state holds UI state only; real data comes from the API.
