# Research Template

**Date:** 2026-05-04 12:40 MST

## File Organization

Each deep-dive gets its own file: `dd-NN-<tool-slug>.md` (dd = deep-dive).

```
dd-01-codebase-memory.md
dd-02-codemcp.md
dd-03-cie.md
...
```

After all deep-dives, a comparison file: `07-comparison-matrix.md`.

## Template

```markdown
# Deep Dive: <Tool Name>

**Date:** <YYYY-MM-DD HH:MM MST>
**Repo:** <GitHub URL>
**License:** <license>
**Stars / Last Commit:** <stars> / <date>

## 1. What It Does

<2-3 sentences. What problem does it solve, and how.>

## 2. Architecture

- **Index format:** <SQLite / graph DB / in-memory / etc>
- **Parser:** <tree-sitter / AST / SCIP / LSP / etc>
- **Storage:** <where the index lives, how big>
- **Query interface:** <MCP / CLI / HTTP / library>

## 3. PHP / Laravel Support

- PHP parsing: <yes/no/partial — specifics>
- Laravel magic: <facades, observers, DI, events, queue — what's resolved?>
- Multi-repo: <yes/no — how?>

## 4. Indexing & Staleness

- Initial index time: <estimate or benchmark>
- Incremental update: <how it works>
- Stale entry detection: <how / whether>
- Git-aware: <yes/no>

## 5. Query Capabilities

List what you can ask it:
- [ ] Symbol lookup (class, method, function)
- [ ] Callers of X
- [ ] Callees of X
- [ ] References to X
- [ ] Cross-file dependency graph
- [ ] Call chain tracing (A → B → C)
- [ ] Impact analysis (what breaks if I change X)
- [ ] Dead code detection
- [ ] Route → controller mapping
- [ ] Other: <specifics>

## 6. Limitations

<Honest list of what it can't do or does poorly.>

## 7. Integration Effort

- Setup: <trivial / moderate / heavy>
- Dependencies: <what it needs installed>
- Multi-repo config: <how>

## 8. Verdict

<2-3 sentences. Is this worth trying? What role could it play in a layered solution?>
```
