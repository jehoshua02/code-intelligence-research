# Codebase Knowledge Discussion

**Date:** 2026-05-04 12:00 MST
**Ticket:** none
**Status:** active

## Context

Exploring strategies to give an AI agent cheap, fast, accurate lookup across multiple repos — full class/method/caller/callee knowledge. Goals:

- **Complete symbol graph** — every class, method, caller, callee across all repos
- **Cross-repo flow tracing** — endpoint calls between services, shared DB tables, queue consumers
- **Non-obvious path traversal** — Laravel observers, dynamic class resolution, service providers, route-to-controller-to-Datadog APM mappings
- **Problem detection** — race conditions, cross-service bugs, broken assumptions between code paths
- **Annotation layer** — ability to store and recall human notes attached to specific code locations
- **Speed + cost** — minimize tokens and latency; agent should answer codebase questions without deep re-reading every session
