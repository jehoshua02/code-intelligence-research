# Graph Database

## What it is

A database where entities (nodes) and relationships (edges) are first-class citizens. Optimized for traversing connections, not scanning rows. The question "which services are transitively affected if I change this method?" is a native operation.

## How it works

Data is stored as nodes with labels and properties, connected by typed, directed edges. A code intelligence schema might look like:

```
(UserRepository)-[:DEFINES]->(find)
(AuthController)-[:CALLS]->(find)
(AuthController)-[:EXTENDS]->(BaseController)
(BaseController)-[:IMPORTS]->(AuthService)
```

Queries are written in a path-based language (Cypher for Neo4j):

```cypher
MATCH (m:Method {name: 'find'})<-[:CALLS*1..5]-(caller)
RETURN caller
```

This returns every caller up to 5 hops away — a query that would require 5 self-joins in SQL. The database stores adjacency lists natively, so traversal is fast regardless of depth.

## Problems it solves

Call graphs, dependency trees, and inheritance hierarchies are fundamentally relational. Relational databases model these awkwardly — each hop is a JOIN. Graph databases make dependency analysis, impact analysis, and "what breaks if I change X?" queries natural and fast.

## Limitations

Not the right tool for tabular data, bulk analytics, or full-text search. Operational complexity is higher than Postgres. Query languages (Cypher, Gremlin) have a learning curve. Most graph databases do not natively support vector search, so you'll need a polyglot architecture.

## When to choose it

When your domain is fundamentally relational — call graphs, dependency trees, inheritance hierarchies, module coupling analysis. Combine with a vector database for semantic search and a relational store for flat metadata.
