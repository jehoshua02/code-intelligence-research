# tree-sitter

## What it is

A C library (with bindings for most languages) that parses source code into a syntax tree, incrementally, in milliseconds. It is the de facto parser used by Neovim, GitHub Copilot, and most modern code intelligence tools.

## How it works

tree-sitter uses a generated parser per language (e.g., `tree-sitter-php`) based on a grammar file written in JavaScript. The grammar describes the language's syntax; tree-sitter compiles it into an efficient C parser. Parsing is error-tolerant — it produces a usable tree even for incomplete or broken code. Its standout feature: when you edit one line, it re-parses only the affected subtree, not the whole file.

You query the tree using tree-sitter's pattern language, similar to CSS selectors for code:

```scheme
(method_call_expression
  object: (variable_name) @object
  name: (name) @method)
```

This matches every method call in a PHP file and captures the object and method name.

## Problems it solves

Writing a correct, fast parser per language from scratch is a multi-month project. tree-sitter gives you one for PHP, JavaScript, Python, Go, Ruby, and 100+ others, all with the same query API. Grammars are maintained by the community and are generally production-quality for major languages.

## Limitations

Produces a syntax tree, not a full semantic one. It knows `foo()` is a function call but not what `foo` resolves to. No cross-file awareness. Grammar quality varies — PHP support is solid, some niche languages less so.

## When to choose it

Default choice for syntactic parsing. Use it when you need structure extraction, syntax highlighting, or code navigation without needing type resolution. If you need semantic answers (which class does this method belong to?), pair it with an LSP server or a SCIP index.
