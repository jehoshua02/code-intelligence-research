# LSP (Language Server Protocol)

## What it is

A JSON-RPC protocol that separates language intelligence from the editor. A language server (e.g., `phpactor`, `intelephense`) runs as a background process; the editor sends it requests and gets back semantic answers. Standardized by Microsoft in 2016.

## How it works

The editor opens a file, sends a `textDocument/didOpen` notification, then queries:

- `textDocument/hover` — "what is this symbol?"
- `textDocument/definition` — "where is this defined?"
- `textDocument/references` — "where is this used?"
- `textDocument/completion` — "what can I type here?"

The server maintains an in-memory index of the project and responds. Communication is over stdin/stdout or a socket, using JSON-RPC messages.

In PHP, when your cursor is on `$repo->find($id)`, an LSP server resolves that `find` is `UserRepository::find(int $id): ?User` and returns that information to the editor.

## Problems it solves

Before LSP, every editor needed a custom plugin per language — an N×M integration problem. Now one server works in VS Code, Neovim, Emacs, and any LSP-compliant editor. For code intelligence systems, LSP servers are a shortcut to semantic answers without reimplementing type inference.

## Limitations

Designed for interactive, per-file queries — not bulk analysis. Calling it in a loop over 10,000 files is slow and fragile. It also requires a running server process and a workspace context, making it awkward to embed in batch pipelines.

## When to choose it

When you need semantic answers (type resolution, cross-file references) and your use case is editor-adjacent or interactive. For bulk indexing, use a dedicated indexer (e.g., a SCIP indexer) that can produce a durable artifact without a live server.
