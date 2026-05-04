# <Tool Name>

**Repo:** <GitHub URL or homepage>
**License:** <license>
**Activity:** <active / maintenance / abandoned — last commit date>
**Pricing:** <open source / freemium / commercial / self-hosted>

## 1. What It Does

<2-3 sentences. What problem does it solve, and how.>

## 2. Architecture

- **Index format:** <SQLite / graph DB / in-memory / etc.>
- **Parser:** <tree-sitter / AST / SCIP / LSP / etc.>
- **Storage:** <where the index lives, approximate size>
- **Query interface:** <MCP / CLI / HTTP / library>

## 3. Language Support

- Languages supported and depth of support
- Dynamic/framework-aware features (e.g., DI resolution, route mapping, decorator handling)
- Multi-language project support

## 4. Indexing and Staleness

- Initial index time (estimate or benchmark)
- Incremental update mechanism
- Stale entry detection
- Git-aware (yes/no)

## 5. Query Capabilities

| Capability | Supported | Notes |
|---|---|---|
| Symbol lookup (class, method, function) | | |
| Callers of X | | |
| Callees of X | | |
| References to X | | |
| Cross-file dependency graph | | |
| Call chain tracing (A -> B -> C) | | |
| Impact analysis (what breaks if I change X) | | |
| Dead code detection | | |
| Route -> controller mapping | | |

## 6. Indirect Path Resolution

How well does it trace paths that aren't in the syntax tree?

- **Native support:** which dynamic patterns does it resolve? (event dispatch, queue handlers, DI containers, middleware pipelines, dynamic dispatch, framework magic)
- **Extensibility:** can you add custom rules, plugins, or config hints to teach it new patterns?
- **LLM-augmented:** could an agent layer fill the gaps by combining the tool's static graph with framework knowledge?

## 7. Annotations

- Can you attach notes or metadata to code symbols?
- Format (comments, sidecar files, database entries, tags)
- Queryable (can you search/filter by annotation)?
- Shareable (team-visible or local-only)?

## 8. LLM Integration

How does it surface context to an LLM?

- Interface: MCP server / RAG / context window stuffing / API
- What context is provided (full files, snippets, graph structure, summaries)
- Token efficiency (does it minimize what's sent, or dump everything?)

## 9. Performance and Scale

- Index size on disk for a large codebase
- Memory usage during indexing / querying
- Query latency

## 10. Limitations

<Honest list of what it can't do or does poorly.>

## 11. Integration Effort

- **Setup:** <trivial / moderate / heavy>
- **Dependencies:** <what it needs installed>
- **Multi-repo:** <how it handles multiple repos, cross-repo queries>
- **Configuration:** <how much tuning before it's useful>

## 12. Hands-On Notes

<Observations from running the tool against a real codebase. Benchmarks, surprises, friction. Leave blank until tested.>

## 13. Verdict

<2-3 sentences. Is this worth trying? What role could it play in a layered solution?>
