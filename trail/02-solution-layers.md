# Solution Layers (Initial Framework)

**Date:** 2026-05-04 12:05 MST

Three layers identified before diving into specifics:

## 1. Offline Index (the foundation)

Persistent symbol graph — classes, methods, call edges, routes, observer registrations, service bindings. Populated by static analysis. Rebuilt on commit or nightly. Makes lookups cheap: agent queries the index instead of reading files.

## 2. Semantic Annotation Layer (the human knowledge)

Human notes anchored to code symbols or file ranges. "This observer silently triggers a queue job that hits the bank service" — things static analysis can't infer. Keyed to symbols, not file paths, so they survive refactors.

## 3. Agent Retrieval Protocol (the glue)

MCP server or similar interface the agent calls instead of grep/Read. Structured endpoints: `callers_of()`, `route_to_controller()`, `cross_repo_calls()`, `notes_for()`. Returns lean, pre-digested answers. Agent never touches raw files for lookup.

## Key Tradeoff

Build-time investment vs. query-time cost. Rich offline index = expensive to build, cheap per query. Thin index + on-demand analysis = easy to set up, expensive per question.
