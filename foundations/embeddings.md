# Embeddings

## What it is

A numeric vector (array of floats) that represents the semantic meaning of a piece of text or code. Semantically similar content produces vectors that are geometrically close in high-dimensional space. The key property: similarity in meaning maps to proximity in vector space.

## How it works

A neural model reads a chunk of text and outputs a vector of 768–3072 numbers. The model is trained so that semantically related content produces similar vectors, even when the words differ:

- `"find user by email"` → `[0.12, -0.87, 0.34, ...]`
- `UserRepository::findByEmail($email)` → `[0.13, -0.85, 0.31, ...]`

These vectors would be very close (high cosine similarity) despite sharing no words. Common models: `text-embedding-3-small` (OpenAI), `nomic-embed-code` (open-weight, code-optimized).

## Problems it solves

Keyword search requires you to know the exact terms used in code. Embeddings let you search by concept: "where does this app handle password resets?" finds `initiateCredentialRecovery()` even though it shares no words with the query. This is the foundation of semantic search in code intelligence.

## Limitations

Vectors are opaque — you cannot inspect them to understand what they encode. Quality depends heavily on the model and the chunking strategy (how you split code into pieces). Embedding a 1,000-line file as one chunk loses local detail; embedding each line loses context. Typically you chunk at function or class-method granularity.

## When to choose it

When users will search with natural language, or when you need fuzzy concept-level matching. Always combine with exact/structural search — pure embedding search misses precise queries like "show me all calls to `DB::beginTransaction()`."
