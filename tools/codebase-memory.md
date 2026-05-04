# Codebase-Memory

**Repo:** https://github.com/DeusData/codebase-memory-mcp
**License:** MIT
**Activity:** Active -- last push 2026-05-04, 484 commits, 31 releases, 2.1K stars, 237 forks, 110 open issues. Created 2026-02-24. Latest release v0.6.0 (2026-04-06).
**Pricing:** Open source, self-hosted. Single static binary. No API keys or cloud dependency.

## 1. What It Does

Parses codebases using tree-sitter into a persistent knowledge graph stored in SQLite, then exposes 14 structural query tools via MCP. Replaces repeated file-reading and grep-searching by LLM agents with pre-materialized structural relationships -- functions, classes, call chains, HTTP routes, cross-service links. Achieves 83% answer quality vs 92% for file-exploration agents, at 10x fewer tokens and 2.1x fewer tool calls.

## 2. Architecture

- **Index format:** SQLite with WAL mode (ACID-safe, persistent). Property-graph model with typed nodes (13 labels) and edges (16 relationship types).
- **Parser:** Vendored tree-sitter grammars (66 languages compiled into binary). LSP-style hybrid type resolution for Go, C, and C++ (more planned). 6-strategy cascading call resolver with confidence scoring (0.30-0.95).
- **Storage:** `~/.cache/codebase-memory-mcp/` -- one SQLite file per project. Size not explicitly documented but Linux kernel produces 2.1M nodes + 4.9M edges. RAM-first pipeline during indexing: LZ4 compression, in-memory SQLite, single flush at end.
- **Query interface:** MCP (JSON-RPC 2.0 over stdio) with 14 typed tools. Also usable via CLI mode. Optional 3D graph visualization UI on localhost:9749.

### 2.1. Indexing Pipeline (6 phases, single transaction)

1. **Structure:** File discovery; Project, Package, Folder, File nodes + containment edges.
2. **Extraction:** Parallel definition extraction via pthreads worker pool; Function, Method, Class, Interface, Enum, Type nodes; decorators; FunctionRegistry.
3. **Resolution:** Parallel CALLS, IMPORTS, USAGES, USES_TYPE, IMPLEMENTS, INHERITS, DECORATES edges via 6-strategy cascading resolver.
4. **Enrichment:** TESTS edges, HTTP route matching, config linking, git co-change (FILE_CHANGES_WITH) edges.
5. **Flush:** Bulk INSERT into SQLite with deferred index creation.
6. **Post-index:** Louvain community detection, XXH3 file hashes.

### 2.2. Graph Data Model

**Node labels (13):** Project, Package, Folder, File, Module, Class, Function, Method, Interface, Enum, Type, Route, Resource.

**Edge types (16):** CONTAINS_PACKAGE, CONTAINS_FOLDER, CONTAINS_FILE, DEFINES, DEFINES_METHOD, IMPORTS, CALLS, HTTP_CALLS, ASYNC_CALLS, IMPLEMENTS, HANDLES, USAGE, CONFIGURES, WRITES, MEMBER_OF, TESTS, USES_TYPE, FILE_CHANGES_WITH.

## 3. Language Support

- **66 languages** via vendored tree-sitter grammars compiled into the binary.
- **Excellent tier (>=90% score):** Lua, Kotlin, C++, Perl, Objective-C, Groovy, C, Bash, Zig, Swift, CSS, YAML, TOML, HTML, SCSS, HCL, Dockerfile.
- **Good tier (75-89%):** Python, TypeScript, TSX, Go, Rust, Java, R, Dart, JavaScript, Erlang, Elixir, Scala, Ruby, PHP, C#, SQL.
- **Functional tier (<75%):** OCaml, Haskell.
- **Also supported:** Clojure, F#, Julia, Vim Script, Nix, Common Lisp, Elm, Fortran, CUDA, COBOL, Verilog, Emacs Lisp, MATLAB, Lean 4, Vue, Svelte, JSON, XML, Markdown, Makefile, CMake, Protobuf, GraphQL, and more.
- **Dynamic/framework-aware features:** LSP-style hybrid type resolution for Go, C, C++. 6 framework-specific HTTP extractors (Python, Go, Java/Spring, Kotlin/Ktor, Express.js, Laravel). Decorator extraction. Route-to-handler linking. No DI resolution, no event/queue dispatch beyond HTTP.
- **Multi-language:** Yes -- indexes all languages in one graph. Cross-service HTTP linking works across language boundaries.

## 4. Indexing and Staleness

- **Initial index time:** Linux kernel (28M LOC, 75K files): ~3 min full / 1m12s fast. Django: ~6s. Average repo: seconds to sub-second.
- **Incremental update:** XXH3 content-hash per file. Background file watcher with adaptive polling detects changes. Only changed files re-parsed. Incremental re-index ~1.2s (vs ~6s fresh for Django-size). No-op: <1ms.
- **Stale entry detection:** Hash comparison on watcher tick. Changed files have their nodes/edges deleted and re-parsed.
- **Git-aware:** Yes. `detect_changes` maps git diff to affected symbols with blast radius and risk classification. Respects `.gitignore` hierarchy. `FILE_CHANGES_WITH` edges from git co-change analysis. Auto-sync via git polling.

## 5. Query Capabilities

| Capability | Supported | Notes |
|---|---|---|
| Symbol lookup (class, method, function) | Yes | `search_graph` with label, name regex, file pattern, degree filters. <10ms. |
| Callers of X | Yes | `trace_call_path` direction=callers, depth 1-5. ~0.3ms. |
| Callees of X | Yes | `trace_call_path` direction=callees, depth 1-5. ~0.3ms. |
| References to X | Partial | USAGE edges + `search_code` for text grep. Not LSP-precise references. |
| Cross-file dependency graph | Yes | IMPORTS edges, CALLS edges, full graph traversal via Cypher. |
| Call chain tracing (A -> B -> C) | Yes | `trace_call_path` with configurable depth. BFS traversal. |
| Impact analysis (what breaks if I change X) | Yes | `detect_changes` maps git diff to blast radius with risk classification. |
| Dead code detection | Yes | Functions with zero callers, excluding entry points/handlers/main(). ~150ms. |
| Route -> controller mapping | Yes | Route nodes + HANDLES edges. 6 framework extractors. Confidence scored. |
| Architecture overview | Yes | `get_architecture`: languages, packages, routes, hotspots, clusters, ADRs. |
| Cypher-like queries | Yes | `query_graph` supports MATCH, WHERE, RETURN, ORDER BY, LIMIT, variable-length paths, recursive CTEs. Read-only. No WITH/COLLECT/OPTIONAL MATCH. |
| Community detection | Yes | Louvain algorithm. MEMBER_OF edges to cluster nodes. |
| Cross-service HTTP linking | Yes | HTTP_CALLS and ASYNC_CALLS edges with confidence scoring. 6 framework extractors. |
| Code snippet retrieval | Yes | `get_code_snippet` by qualified name. Reads actual source from disk. |
| Text search | Yes | `search_code` with BM25 full-text search (v0.6.0). |
| Semantic search | Yes | `semantic_query` via Nomic embeddings (v0.6.0). |
| Near-clone detection | Yes | MinHash fingerprinting, SIMILAR_TO edges (v0.6.0). |

## 6. Indirect Path Resolution

### 6.1. Native Support

- **HTTP route/call-site matching:** Yes, with confidence scoring (0.0-1.0). 6 framework-specific extractors: Python, Go, Java/Spring, Kotlin/Ktor, Express.js, Laravel.
- **Cross-service HTTP links:** Yes. REST endpoints as first-class graph entities. Works across language boundaries.
- **Call resolution:** 6-strategy cascading resolver: import map (0.95), import map suffix (0.85), same module (0.90), unique name (0.75), suffix match (0.55), fuzzy (0.30-0.40).
- **Type-inferred method calls:** LSP-style hybrid resolution for Go, C, C++ (scope chain, field/method lookup, base-class traversal, return-type propagation).
- **gRPC:** In progress (issue #293-294 indicate active work on cross-repo gRPC matching, with phantom route bugs being fixed).
- **NOT resolved:** Event dispatch, queue handlers, DI containers, middleware pipelines, dynamic dispatch, reflection, runtime behavior, decorator-based routing beyond the 6 supported frameworks.

### 6.2. Extensibility

- **Custom file extensions:** `.codebase-memory.json` with `extra_extensions` mapping.
- **Custom ignore patterns:** `.cbmignore` files (gitignore syntax).
- **Plugin/rule system for new patterns:** Not documented. The resolver strategies are hardcoded in C. Adding new framework extractors requires modifying source code.
- **Runtime trace ingestion:** `ingest_traces` tool can validate/augment HTTP_CALLS edges with real runtime data -- a bridge between static and dynamic analysis.

### 6.3. LLM-Augmented

The tool is designed for exactly this pattern. An LLM agent could:
- Use the static graph for structural queries (callers, callees, routes).
- Fill gaps by reading source code via `get_code_snippet` or `search_code` for dynamic patterns.
- Use `detect_changes` + domain knowledge to reason about event/queue/DI paths.
- The 83% vs 92% quality gap likely comes from these dynamic patterns -- an agent combining both approaches could close it.

## 7. Annotations

- **ADRs (Architecture Decision Records):** `manage_adr` tool for CRUD operations. Persist across sessions. Queryable via graph.
- **Decorator/attribute metadata:** Extracted from source and stored as node properties.
- **Confidence scores:** Attached to HTTP_CALLS and ASYNC_CALLS edges.
- **Community membership:** MEMBER_OF edges from Louvain detection.
- **Custom annotations on symbols:** Not supported. No mechanism to attach arbitrary notes or metadata to code symbols.
- **Format:** Database entries in SQLite.
- **Queryable:** Yes, via Cypher and search_graph.
- **Shareable:** Local-only (SQLite file). Could theoretically share the cache directory but no built-in team sharing mechanism.

## 8. LLM Integration

- **Interface:** MCP server (JSON-RPC 2.0 over stdio). Supports 11 coding agents: Claude Code, Codex CLI, Gemini CLI, Zed, OpenCode, Antigravity, Aider, KiloCode, VS Code, OpenClaw, Kiro.
- **What context is provided:** Structured JSON responses -- symbol names, qualified paths, call chains, graph structure, code snippets on demand. Not full files by default.
- **Token efficiency:** 99.2% reduction in benchmarks. 5 structural queries: ~3,400 tokens vs ~412,000 via file-by-file exploration. Pattern search ~200 tokens vs ~45,000. Call chain trace ~800 vs ~120,000.
- **Agent instructions:** Auto-installs "Skills" (4 for Claude Code) that guide the agent to prefer graph queries over file exploration. Advisory hooks (PreToolUse) remind agents to use graph tools.
- **CLI mode:** All 14 tools callable from command line for scripting/pipeline integration.

## 9. Performance and Scale

| Metric | Value | Notes |
|---|---|---|
| Linux kernel full index | ~3 min | 28M LOC, 75K files -> 2.1M nodes, 4.9M edges |
| Linux kernel fast index | 1m 12s | 1.88M nodes |
| Django full index | ~6s | 49K nodes, 196K edges |
| Incremental re-index | ~1.2s | Content-hash based, changed files only |
| No-op re-index | <1ms | Hash match, nothing to do |
| Cypher query | <1ms | Relationship traversal via recursive CTEs |
| Call path trace (depth=5) | ~0.3ms | BFS traversal |
| Name search (regex) | <10ms | SQL LIKE with pre-filtering |
| Dead code detection | ~150ms | Full graph scan with degree filtering |
| Index size on disk | Unconfirmed | Not explicitly documented; single SQLite file per project |
| Memory during indexing | Unconfirmed | RAM-first pipeline; per-worker buffers; memory released after flush |
| Memory during querying | Unconfirmed | SQLite WAL mode; likely minimal |

## 10. Limitations

1. **Static analysis only.** No runtime behavior, reflection, dynamic dispatch, or metaprogramming. Macros (C/C++) lack AST representation in tree-sitter. Paper explicitly states: "the knowledge graph captures static structure only."
2. **Quality gap.** 83% answer quality vs 92% for file-exploration agents. The explorer retains advantage for full source context (16/31 languages) and exhaustive call-site grep (10/31) -- queries requiring line-level code the graph intentionally does not store.
3. **Limited Cypher.** No WITH, COLLECT, OPTIONAL MATCH, mutations. Query ceiling of 100K rows.
4. **Framework coverage.** HTTP extractors only for 6 frameworks (Python, Go, Java/Spring, Kotlin/Ktor, Express.js, Laravel). No event dispatch, queue handlers, DI containers, middleware pipelines.
5. **No custom resolver plugins.** Adding new framework extractors requires modifying C source. No plugin API.
6. **Stability in progress.** Active bugs: SIGBUS on macOS arm64 with concurrent sessions (#314), C++ crash (#312), TypeScript path alias resolution broken in monorepos (#308), Cypher evaluator bugs (#305), search_graph multi-minute latency on large codebases (#301-302).
7. **Young project.** Created Feb 2026, first stable release Mar 2026. Rapidly iterating (31 releases in ~2 months). API surface may be unstable.
8. **No team sharing.** Local SQLite files only. No built-in mechanism for shared indexes or collaborative annotations.
9. **Type inference limited.** LSP-style resolution only for Go, C, C++. Dynamically-typed languages (Python, Ruby, JS) rely on import-map and name-matching heuristics.
10. **Phantom edges.** gRPC extractor produces false positive routes from non-gRPC variable names (#294).

## 11. Integration Effort

- **Setup:** Trivial. One-line install script. Single static binary, zero runtime dependencies. Auto-configures 11 coding agents.
- **Dependencies:** Build from source requires C compiler + C++ compiler + zlib. Pre-built binaries need nothing.
- **Multi-repo:** Each repo indexed separately into its own SQLite DB. Cross-project HTTP edges in progress (#295). No unified cross-repo graph yet (per current issues).
- **Configuration:** Minimal. Works out of the box. Optional: `.codebase-memory.json` for custom file extensions, `.cbmignore` for ignore patterns, env vars for cache dir and diagnostics. `config set/get/reset` for auto-indexing behavior.

## 12. Hands-On Notes

*(Not tested yet.)*

## 13. Verdict

Strong candidate for the "structural query" layer in a composite code intelligence stack. The token efficiency gains (99%+ reduction) and sub-millisecond query times are compelling for LLM agent workflows. The 83% quality score is the honest trade-off -- it trades exhaustive source-level accuracy for massive efficiency, which an agent can compensate for by falling back to file reads when the graph answer is insufficient. The project is very young (3 months old) with active stability bugs, but the architecture is sound (pure C, single binary, SQLite, tree-sitter) and development velocity is high. Worth evaluating hands-on, particularly for: call chain tracing, impact analysis, architecture overview, and as a first-pass filter before expensive file reads. The lack of DI/event/queue resolution is a known gap that an LLM agent layer could bridge using the graph as scaffolding plus targeted source reads.
