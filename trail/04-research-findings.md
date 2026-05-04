# Research Findings

**Date:** 2026-05-04 12:15 MST

## 1. Code Graph Databases (most promising category)

### 1.1 Codebase-Memory
- SQLite + tree-sitter, 66 languages (PHP included)
- 14 MCP query tools built in
- Incremental via file-watching + content-hash re-indexing
- Open source
- **Best overall fit**: local, scriptable, PHP, incremental, MCP-native

### 1.2 CodeGraph
- SQLite + tree-sitter, PHP supported
- Scriptable CLI
- Simpler than Codebase-Memory, no incremental updates mentioned
- Open source (MIT)

### 1.3 CodeGraphContext
- MCP server + CLI, graph DB backend (LadybugDB, FalkorDB, Neo4j)
- Live file watching for incremental updates
- 15 languages (PHP TBD)
- Call chain tracing across files
- Open source

### 1.4 GitNexus
- Knowledge graph via LadybugDB
- Cross-file import/call resolution
- MCP-native, works with Claude Code
- Early stage
- Open source

### 1.5 Graphify
- Compresses codebase into queryable knowledge graph
- 71.5x token compression claimed
- Open source

## 2. PHP Static Analysis Tools

### 2.1 Psalm
- Has internal call graph (used by --find-dead-code, --taint-analysis)
- No direct "export call graph as JSON" — would need a custom plugin to extract
- Laravel plugin exists (psalm-plugin-laravel)
- Incremental file-level cache
- Development has slowed vs PHPStan

### 2.2 PHPStan
- No call graph export. Internal symbol table not exposed
- Best Laravel support via Larastan (facades, model attrs, relations)
- Incremental result cache (internal, not queryable)
- Very active (28k+ stars)

### 2.3 Phpactor
- OSS, builds symbol index, supports find-references and go-to-definition
- CLI-scriptable (`class-references`, `references` commands)
- Index stored in filesystem
- Laravel support limited without stubs
- Most promising for queryable PHP-specific index

### 2.4 php-parser (nikic)
- Full AST, but no type inference — can't resolve dynamic dispatch
- Laravel magic invisible without type layer
- Foundation for custom tooling, not a solution by itself

### 2.5 tree-sitter (PHP grammar)
- Fast, incremental, cross-language
- Syntax only — no type resolution, no name resolution
- Good for approximate symbol extraction, not semantic queries

## 3. SCIP / LSIF / Sourcegraph

- **Sourcegraph self-hosted**: was available free, increasingly restricted. SCIP is their index format.
- **SCIP PHP indexer**: no official PHP indexer found. Would need custom work.
- **LSIF PHP**: no mature PHP emitter found.
- **Standalone SCIP querying without Sourcegraph**: not well supported.
- **Verdict**: not viable for PHP without significant custom work.

## 4. Enterprise Code Intelligence (not viable)

- **Kythe** (Google): no PHP support, requires custom indexer
- **CodeQL** (GitHub): no PHP support, licensed for private repos
- **Glean** (Meta): PHP indexer exists internally but not in OSS release

## 5. Cross-Service Mapping

- **No off-the-shelf tool** for detecting PHP HTTP/queue/event calls across repos
- **Datadog Service Dependencies API** (already have): auto-discovers from APM traces, queryable, staleness built in (30-day aging). Fastest path to a service map.
- **Backstage** (Spotify): self-hosted service catalog, significant setup cost, language-agnostic
- **Custom script**: grep `Http::`, `Queue::push`, `event()` — 1-2 day effort

## 6. Code Annotation (weakest category)

- **Catseye**: VS Code extension, annotations move with code. Not scriptable. VS Code only.
- **No standalone CLI tool** for refactor-resilient code annotations exists.
- This is likely a "build it yourself" area.

## Summary Table

| Tool | Local | PHP | Scriptable | Incremental | Annotations | Multi-repo |
|------|-------|-----|------------|-------------|-------------|------------|
| Codebase-Memory | yes | yes | MCP+CLI | yes (file-watch) | no | ? |
| CodeGraphContext | yes | ? | MCP+CLI | yes (file-watch) | no | ? |
| CodeGraph | yes | yes | CLI | no? | no | ? |
| GitNexus | yes | yes | MCP | ? | no | ? |
| Phpactor | yes | yes | CLI | yes | no | no |
| Psalm (plugin) | yes | yes | custom | yes (file cache) | no | no |
| Datadog Svc Map | cloud* | n/a | API | auto (30d) | no | yes |

*Datadog data can be pulled locally via API

## Key Findings

1. **Codebase-Memory is the strongest candidate** — hits most criteria, MCP-native, PHP via tree-sitter
2. **Tree-sitter based tools can't resolve Laravel magic** — facades, observers, DI, dynamic dispatch all invisible
3. **The PHP semantic gap**: tools that understand PHP semantics (Psalm, PHPStan) don't export queryable indexes. Tools that export indexes (tree-sitter based) don't understand PHP semantics.
4. **Annotation tooling doesn't exist** in the form we need (scriptable, symbol-anchored, refactor-resilient)
5. **Cross-service mapping is best solved dynamically** via Datadog APM data we already have
6. **Staleness handling**: tree-sitter tools use file-watching + content hashing. Semantic tools use file-level caching. No tool does git-diff-aware selective re-indexing out of the box.

## Open Questions

1. Does Codebase-Memory's tree-sitter approach give enough depth for our needs, or do we need semantic resolution?
2. Can we layer Psalm/PHPStan type data on top of a tree-sitter index to fill the semantic gap?
3. Is Datadog Service Dependencies API sufficient for cross-service mapping, or do we need static analysis too?
4. Should annotation be a separate lightweight tool (SQLite + symbol keys) rather than integrated?
