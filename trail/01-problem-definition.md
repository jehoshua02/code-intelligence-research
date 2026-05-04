# Problem Definition

**Date:** 2026-05-04 12:05 MST

## The Problem

AI agents working on codebases are slow and expensive because they re-discover context every session. They read files, grep for symbols, trace call chains — all at token cost and latency cost. This compounds across multiple repos where cross-service flows are invisible without deep investigation.

### Specific pain points

1. **Symbol lookup is expensive** — finding every caller/callee of a method requires grep + file reads + deduplication, every time
2. **Cross-repo flows are invisible** — service A calls service B's endpoint, but the agent has no way to know this without reading both codebases
3. **Non-obvious paths are missed** — Laravel observers, dynamic class resolution, service container bindings, event listeners, queue jobs — static grep doesn't find these
4. **Route-to-runtime mapping is lost** — connecting a route name to its controller to its Datadog APM trace to its Rollbar errors requires human knowledge
5. **Context resets every session** — the agent forgets what it learned about code paths, gotchas, and relationships
6. **Human knowledge has no anchor point** — notes about code ("this is fragile because...") live in KB files disconnected from the code they describe; they rot as code changes
7. **Problem detection requires full-graph understanding** — race conditions, cross-service bugs, and broken assumptions can't be spotted without seeing the complete picture

### What "solved" looks like

- Agent can answer "who calls X?" across all repos in <1 second, <100 tokens
- Agent can trace a request from HTTP endpoint through queues/events/service calls to final side effects
- Human notes are anchored to code symbols (not file paths) and survive refactors
- Agent can detect anomalies: dead code, circular dependencies, mismatched assumptions between services
- Maintenance cost is low — index updates automatically on code changes

## Next Step

Survey existing tools and products that solve parts of this problem before designing anything custom.
