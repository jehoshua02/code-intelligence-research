# Vector Database

## What it is

A database purpose-built for storing embeddings and running approximate nearest-neighbor (ANN) queries — "find the 10 vectors most similar to this one." Standard SQL databases cannot do this efficiently.

## How it works

At write time, vectors are indexed using structures like HNSW (Hierarchical Navigable Small Worlds) — a layered graph where each node links to nearby neighbors. This allows the search to skip most of the space and find approximate matches in milliseconds. At query time, you embed your query and the DB returns the closest stored vectors.

Common options:

- `pgvector` — Postgres extension; easiest if you're already on Postgres
- `Qdrant` — purpose-built, fast, rich filtering
- `Chroma` — lightweight, good for prototyping
- `Pinecone` — managed, scales easily

All support storing metadata (file path, language, repo) alongside vectors, so you can filter before the vector search: "find similar code, but only in PHP files in this repo."

## Problems it solves

A brute-force cosine similarity scan over 1M vectors takes seconds in SQL. Vector databases make this fast — typically under 50ms. They also handle the operational concerns: persistence, updates, and concurrent queries.

## Limitations

ANN means approximate — results may miss a small number of true matches. Not a replacement for exact search. Storage scales with vector dimensionality and corpus size (a 3072-dim vector at float32 is ~12KB each). Most lack the rich query language of relational or graph databases.

## When to choose it

When you have embeddings and need to query them at scale. `pgvector` is the pragmatic first choice for a new project; purpose-built databases (Qdrant, Pinecone) offer better performance and filtering at higher scale.
