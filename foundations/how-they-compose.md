# How They Compose

These technologies are not alternatives — they layer. Here is how they fit together in a code intelligence system.

## The Stack

```
Source Code
    │
    ▼
tree-sitter ─────────────── parses each file into ──────────► AST
                                                               │
                                                  extract symbols, calls,
                                                  definitions, references
                                                               │
                          ┌────────────────────────────────────┤
                          │                                    │
                          ▼                                    ▼
                       ripgrep                         Graph Database
                  (fast text fallback             (nodes: files, classes,
                   for queries the                 methods; edges: CALLS,
                   index can't handle)             EXTENDS, IMPORTS, etc.)
                                                               │
                                                               │ can also emit
                                                               ▼
                                                          SCIP index
                                                     (cross-repo symbol
                                                      artifact, importable
                                                      into other tooling)
                                                               │
Source Code (chunked per function/method)                      │
    │                                                          │
    ▼                                                          │
Embedding model                                                │
    │                                                          │
    ▼                                                          │
Vector Database ◄──────────── metadata from graph/SCIP ────────┘
(semantic search over code chunks)
    │
    ▼
RAG pipeline
    ├── User query arrives
    ├── Embed query
    ├── Retrieve top-K chunks from vector DB
    ├── Optionally: expand context via graph traversal
    │   ("also fetch all callers of this method")
    ├── Inject into LLM prompt
    └── LLM answers grounded in actual code
```

**LSP** sits alongside this pipeline as an interactive escape hatch: when a query needs precise semantic resolution (exact type of a variable, authoritative "go to definition"), the system can delegate to a running language server rather than relying on the static index.

## Data Flow by Layer

| Layer | Technology | Input | Output |
|-------|-----------|-------|--------|
| Parse | tree-sitter | Source file | AST nodes |
| Structure | AST | AST nodes | Symbols, call edges |
| Graph | Graph DB | Symbols + edges | Traversable call graph |
| Cross-repo | SCIP | Symbols + edges | Portable index artifact |
| Semantic | Embeddings | Code chunks | Float vectors |
| Search | Vector DB | Query vector | Top-K similar chunks |
| Answer | RAG + LLM | Retrieved chunks | Natural language answer |
| Fallback | ripgrep | Regex pattern | Matching text lines |
| Interactive | LSP | Cursor position | Semantic hover/definition |

## Practical Starting Point

For a new code intelligence system, add layers in this order:

1. **tree-sitter** — extract structure from files
2. **Graph database** — store the call/dependency graph
3. **Embeddings + pgvector** — enable semantic search
4. **RAG** — wire it to an LLM
5. **ripgrep** — fast fallback for exact-match queries
6. **SCIP / LSP** — optional upgrades when cross-repo scale or semantic precision becomes a bottleneck

Each layer adds capability without replacing the ones below it. The graph answers structural questions; the vector database answers semantic ones; the LLM synthesizes both into human-readable answers.
