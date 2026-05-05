# AST (Abstract Syntax Tree)

## What it is

A tree representation of source code where every node is a meaningful syntactic unit — a function declaration, a method call, a variable assignment. Unlike raw text, an AST strips whitespace and formatting and keeps only structure.

## How it works

A parser reads source text and applies grammar rules to produce a tree. In PHP, `$user->getName()` becomes a `MethodCallExpression` node with a `Variable` child (`$user`) and an `Identifier` child (`getName`). Each node knows its type, its position in the file, and its children.

```
MethodCallExpression
├── object: Variable ($user)
└── method: Identifier (getName)
```

Most languages have at least one parser that emits an AST. PHP has `nikic/php-parser`; JavaScript has Babel and Acorn; tree-sitter (see [`tree-sitter.md`](tree-sitter.md)) provides ASTs for 100+ languages via a unified API.

## Problems it solves

Lets you ask structural questions: "find all calls to `DB::table()`," "what methods does this class expose," "where is this variable defined." Text search cannot answer these reliably — a comment or string could contain `DB::table(` and fool it. ASTs let you distinguish between a call site, a comment, and a string literal.

## Limitations

Static structure only. An AST tells you the shape of code, not what it does at runtime. It has no awareness of types that resolve dynamically, inherited methods, or which branch of an `if` actually executes. It is also single-file — the AST for `UserRepository.php` knows nothing about `User.php`.

## When to choose it

Any time you need to understand code structure without executing it. It is the foundation layer for nearly everything in code intelligence — symbol extraction, call graph construction, syntax highlighting, refactoring tools.
