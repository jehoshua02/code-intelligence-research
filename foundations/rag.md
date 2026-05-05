# RAG (Retrieval-Augmented Generation)

## What it is

A pattern for giving an LLM relevant context from an external knowledge base at query time, rather than relying solely on training data. The LLM reads relevant snippets on demand instead of memorizing your codebase.

## How it works

Two phases:

**Indexing** (done once, or on change):
1. Chunk your code at meaningful boundaries (per function, per method)
2. Embed each chunk with an embedding model
3. Store vectors + metadata + original text in a vector database

**Retrieval** (done per query):
1. Embed the user's question
2. Fetch top-K most similar chunks from the vector database
3. Inject those chunks into the LLM prompt as context
4. LLM answers using those chunks

Variants add re-ranking (a second model scores retrieved chunks for relevance), hybrid search (vector + keyword), and multi-hop retrieval (use the LLM's first answer to drive a second retrieval).

## Problems it solves

LLMs have stale training data and finite context windows. RAG lets a model answer "how does authentication work in this Laravel app?" using current, actual code without fine-tuning or fitting the whole repo into one prompt.

## Limitations

Quality depends entirely on retrieval quality. Wrong chunks → confident wrong answers. Chunking strategy, embedding model, and retrieval tuning are all failure points. Adds latency for the retrieval step (typically 100–500ms).

## When to choose it

The default architecture for any LLM feature that needs to ground answers in a specific codebase. Nearly always the right starting point before considering fine-tuning, which is expensive and goes stale as the codebase changes.
