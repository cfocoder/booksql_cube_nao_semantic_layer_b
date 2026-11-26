# Scenario B — Direct read-only SQLite with physical schema context

This repository is the Nao Multi-project context for BookSQL scenario **B**.

## Boundary

- Direct data access only through the private `booksql_sqlite` MCP, backed by the physical `accounting.sqlite` file.
- `sqlite_schema` exposes table/column metadata; `sqlite_query` runs bounded `SELECT`/`WITH` statements on the read-only connection.
- No Nao-native database connection and no Cube MCP; this preserves the B-versus-C route distinction.
- `decimal_calculator.calculate` is allowed only for arithmetic over values already retrieved from SQLite; it is not a data-access route.
- No extra business-policy rules are available in B.

## Project mapping

- Nao project: `booksql-condition-b`
- Repository: `cfocoder/booksql_cube_nao_semantic_layer_b`
- Expected route: `booksql_sqlite` → read-only SQLite MCP → `accounting.sqlite`
