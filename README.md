# Code Intelligence Research

Research into tools and techniques that give AI agents fast, cheap, accurate code knowledge across multiple repositories.

## Problem

AI agents re-discover codebase context every session — grepping for symbols, reading files, tracing call chains — all at token and latency cost. Cross-service flows are invisible, non-obvious code paths (events, DI, queues, observers) are missed by static analysis, and human knowledge about code has no durable anchor point.

## Goal

Find or build a solution where an agent can:

- Look up any symbol's callers/callees across repos in <1s and <100 tokens
- Trace requests end-to-end through queues, events, and service boundaries
- Resolve indirect code paths (DI, observers, dynamic dispatch, framework magic)
- Anchor human annotations to code symbols that survive refactors
- Detect anomalies: dead code, circular dependencies, cross-service mismatches
- Stay current automatically as code changes

## Repo Structure

```
tools/              # Individual tool evaluations (see TEMPLATE.md)
comparison/         # Cross-tool comparison matrix
trail/              # Original research discussion notes (temporary)
```

## Approach

Survey existing tools, evaluate each against a standard template, then compare to identify gaps and determine what needs to be built custom.
