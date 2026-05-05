# SCIP (Sourcegraph Code Intelligence Protocol)

## What it is

A protobuf-based file format for storing a precomputed index of code symbols and their relationships. Think of it as a snapshot of everything an LSP server knows, serialized to disk and queryable without a live server.

## How it works

An indexer (e.g., `scip-php`) runs over your codebase and emits a `.scip` binary file. That file maps every symbol — every class, method, variable — to a canonical name using a structured scheme:

```
php-demo 1.0.0 App/UserRepository#find().
```

Each symbol entry includes: its definition location, its documentation, and every reference to it across the repo. Sourcegraph ingests `.scip` files to power "find all references" and "go to definition" across millions of files without running per-repo language servers.

## Problems it solves

LSP is interactive and stateful; SCIP is a batch artifact you build once and query many times. If you want to know "every call site of `UserRepository::find()` across 50 repositories," SCIP lets you do that efficiently. It is also language-agnostic — a polyglot monorepo can combine PHP, TypeScript, and Go SCIP indexes into one queryable surface.

## Limitations

You must run an indexer per language per repo, and indexer quality varies. SCIP is a storage format, not a query engine — you need tooling on top (e.g., Sourcegraph, or a custom importer into a graph database) to answer questions. The ecosystem is still largely Sourcegraph-centric.

## When to choose it

Cross-repo, cross-language code intelligence at scale. When you want durable, queryable symbol data that doesn't require live server processes. A natural complement to a graph database — import SCIP edges as graph relationships.
