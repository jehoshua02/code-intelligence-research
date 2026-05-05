# ripgrep

## What it is

A command-line text search tool written in Rust. Searches file contents with regular expressions, respects `.gitignore`, and is typically 10–100x faster than `grep` on large codebases.

## How it works

Uses finite automata (via the Rust `regex` crate) to match patterns without backtracking — the key reason it avoids the pathological slowness that regex backtracking can cause. Reads files in parallel using multiple threads. Automatically skips binary files, `.git` directories, and anything in `.gitignore`.

```bash
rg 'DB::table\(' --type php
rg 'class\s+\w+\s+extends\s+Model' --type php -l
```

There is also a Rust library (`grep` crate) used by tools like VS Code's search panel to embed ripgrep's engine directly.

## Problems it solves

Sometimes you just need to find text fast. Before committing to an AST parse or semantic index, a regex search can answer "does this codebase use `Redis::connection()`?" in 200ms across 500,000 lines. It is also the right tool when you do not control the file format — searching logs, config files, migration scripts, and so on.

## Limitations

Text only — no understanding of code structure. `DB::table(` in a comment or string is a false positive. Cannot answer "is this the same `find()` I defined in `UserRepository`?" That requires AST parsing or semantic indexing.

## When to choose it

Fast initial discovery, grepping logs, finding configuration values, or as a fallback when your structured index does not have an answer. In code intelligence pipelines, often used as a first-pass filter before heavier analysis, or as a safety net for queries the index cannot handle.
