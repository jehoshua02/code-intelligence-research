# Master Tool List

**Date:** 2026-05-04 12:30 MST

Filtered to: local, open source, active, PHP support (confirmed or likely via tree-sitter). Sorted by relevance to our use case.

## Tier 1: Code Graph + MCP (best fit)

| # | Tool | Index Method | Query | Incremental | Notes |
|---|------|-------------|-------|-------------|-------|
| 1 | Codebase-Memory | tree-sitter + SQLite knowledge graph | MCP (14 tools) | file-watch + content-hash | 66 langs, indexes Linux kernel in 3min, sub-ms queries, arXiv paper |
| 2 | CodeMCP | SCIP + LSP + Git + tree-sitter fallback | MCP + CLI + HTTP | via SCIP/LSP | Orchestrates multiple backends, PHP listed explicitly |
| 3 | CIE (Code Intelligence Engine) | tree-sitter + CozoDB (Datalog) + optional Ollama embeddings | MCP (25 tools) | unknown | Call graph tracing, dependency analysis. PHP support unclear (Go/Python/JS/TS listed) |
| 4 | code-graph-mcp (entrepeneur4lyf) | ast-grep + graph | MCP | file watcher | 25+ languages, call graph + references |
| 5 | CodeGraph (colbymchenry) | tree-sitter + SQLite | MCP + CLI | file watcher | Simple, PHP confirmed |
| 6 | SocratiCode | AST chunking + Qdrant vectors + BM25 | MCP | unknown | Hybrid search, 18 langs, handles 40M+ LOC |
| 7 | CodeBadger (Joern) | Code Property Graph (AST+CFG+PDG) | MCP | unknown | Deepest analysis: taint tracking, data flow. PHP supported. Docker. |
| 8 | jCodeMunch | tree-sitter + BM25 + optional embeddings | MCP | unknown | Call graphs, blast-radius, dead code detection |
| 9 | Graphify | tree-sitter + NetworkX + Leiden clustering | Claude Code skill | unknown | 25 langs, 71.5x token compression |
| 10 | GitNexus | LadybugDB graph | MCP | unknown | Cross-file import/call resolution, early stage |

## Tier 2: Queryable Indexers (good building blocks)

| # | Tool | Index Method | Query | Incremental | Notes |
|---|------|-------------|-------|-------------|-------|
| 11 | scip-php | SCIP format | SCIP tooling | full reindex | Only SCIP PHP indexer. Reads composer.json. Compiler-accurate. |
| 12 | Phpactor | Own indexer | CLI | yes | Find-references, go-to-def. PHP-native. Laravel limited without stubs. |
| 13 | Probe | ripgrep + tree-sitter | CLI + MCP | no persistent index | Returns complete functions/classes. SIMD-accelerated. |
| 14 | ast-grep | tree-sitter pattern matching | CLI + MCP | no persistent index | Structural search. Very fast (Rust). |
| 15 | CodeGraphContext | LadybugDB/FalkorDB/Neo4j | MCP + CLI | file watching | 15 langs, call chain tracing |
| 16 | Codemogger | tree-sitter + local embeddings (MiniLM) + SQLite | MCP + CLI | unknown | No API keys needed |
| 17 | CocoIndex | tree-sitter + embeddings | MCP + API | delta re-indexing | 20+ langs, real-time incremental |
| 18 | CodeDB | tree-sitter + trigram + in-memory | MCP (16 tools) | unknown | Pure Zig, single binary, sub-ms queries |
| 19 | Codeix | tree-sitter + SQLite FTS5 | MCP | unknown | Rust, outputs JSONL index files |
| 20 | RepoMapper MCP | tree-sitter + PageRank | MCP | unknown | Aider's approach as standalone MCP |

## Tier 3: Search Engines (text search, no graph)

| # | Tool | Index Method | Query | Incremental | Notes |
|---|------|-------------|-------|-------------|-------|
| 21 | Zoekt | trigram posting lists | HTTP API | yes | Proven at Google/Sourcegraph scale |
| 22 | OpenGrok | Lucene | REST API | yes | Enterprise-proven, Oracle |
| 23 | Hound | trigram | JSON API | repo watching | Etsy, simple |
| 24 | GNU Global (gtags) | ctags + own parser | CLI | single-file update | Mature, cross-ref |
| 25 | Universal Ctags | tag parsing | tag files | full reindex | 100+ langs, foundation tool |

## Tier 4: PHP-Specific Analysis

| # | Tool | Capability | Notes |
|---|------|-----------|-------|
| 26 | Psalm | Internal call graph, taint analysis | No export, needs plugin to extract |
| 27 | PHPStan + Larastan | Best Laravel type resolution | No queryable index export |
| 28 | Exakat | PHP graph DB analysis | PHP 5.2-8, queryable reports |
| 29 | LaravelBrain | Laravel route→controller→service→model graph | Interactive visualization, Laravel-specific |
| 30 | Rector | AST refactoring | Deep PHP structure understanding |
| 31 | nikic/php-parser | Full PHP AST | Foundation library, no type inference |

## Tier 5: IDE/Framework Context (reference only)

| # | Tool | Approach | Standalone? |
|---|------|---------|------------|
| 32 | Aider RepoMap | tree-sitter + PageRank | built into aider |
| 33 | Continue.dev | tree-sitter + LanceDB vectors + SQLite FTS | yes (configurable) |
| 34 | Tabby | RAG + repo indexing | yes (self-hosted AI assistant) |
| 35 | Cline | ripgrep + fzf + tree-sitter | no persistent index |
| 36 | Repomix | tree-sitter → static file dump | no query capability |

## Architectural Patterns Observed

1. **tree-sitter + graph DB** — parse AST into queryable graph. Call graphs, impact analysis. (Codebase-Memory, CIE, code-graph-mcp)
2. **tree-sitter + PageRank** — rank symbols by importance. Token-efficient. (Aider, RepoMapper)
3. **tree-sitter + embeddings + vector DB** — semantic natural-language queries. (SocratiCode, Codemogger, CocoIndex)
4. **Hybrid BM25 + vector** — keyword precision + semantic recall. (SocratiCode, code-graph-mcp)
5. **Code Property Graph** — AST + CFG + PDG merged. Deepest analysis. (Joern/CodeBadger)
6. **SCIP/LSP orchestration** — leverages language servers for precise resolution. (CodeMCP, scip-php)
7. **Trigram indexing** — fast regex at scale. (Zoekt, CodeDB)

## Deep-Dive Queue (start from top)

1. Codebase-Memory
2. CodeMCP
3. CIE
4. CodeBadger (Joern)
5. scip-php
6. code-graph-mcp
7. SocratiCode
8. LaravelBrain
9. Phpactor
10. Exakat
