# Research Plan

**Date:** 2026-05-04 12:10 MST

## Scope

Local/self-hosted code intelligence tools that can be hydrated and queried via script. No cloud SaaS. No paid hosting.

## Search Categories

1. **Code intelligence platforms** — Sourcegraph (self-hosted), SCIP, LSIF-based tools, language server index formats
2. **Static analysis / call graph generators** — phpstan, psalm, tree-sitter, php-parser AST tools, cross-language graph builders
3. **Code graph databases** — tools that store symbol graphs in queryable form (SQLite, graph DBs, custom formats)
4. **Cross-repo / cross-service mapping** — tools that understand service topology, API contracts, endpoint-to-endpoint calls
5. **Code annotation systems** — tools for anchoring notes to symbols that survive refactors

## Evaluation Criteria

For each tool found, answer:

1. **Local/self-hosted?** — must be yes
2. **Scriptable hydration?** — can a script build/rebuild the index?
3. **Scriptable query?** — CLI, API, or library to query programmatically?
4. **Multi-repo?** — handles multiple repos in one index?
5. **Multi-language?** — at minimum PHP + JS/TS; bonus for others
6. **Non-obvious paths?** — observers, dynamic dispatch, DI, event listeners, queue jobs
7. **Staleness handling?** — how does it detect and recover from stale entries when code changes?
   - Full rebuild cost (time)?
   - Incremental update support?
   - Git-diff-aware updates?
   - Stale entry detection/cleanup?
8. **Human annotation support?** — can notes be attached to symbols?
9. **Open source?** — license, community activity
10. **PHP/Laravel specific support?** — how well does it handle Laravel's magic?

## Search Queries

- "self-hosted code intelligence call graph"
- "SCIP index PHP"
- "LSIF PHP language server index"
- "sourcegraph self-hosted code graph"
- "phpstan call graph export"
- "tree-sitter cross-repo symbol index"
- "code knowledge graph open source"
- "static analysis cross-service dependency mapping"
- "incremental code index update git"
- "code annotation anchored to symbols open source"

## Output

Summary table of tools found, scored against evaluation criteria. Identify top 2-3 candidates worth deeper investigation.
