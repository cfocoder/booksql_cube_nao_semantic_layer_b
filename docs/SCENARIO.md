# Scenario B deployment contract

## Required Nao project

```text
Project: booksql-condition-b
Repository: https://github.com/cfocoder/booksql_cube_nao_semantic_layer_b.git
Branch: main
```

## Required MCP/environment

```text
MCP server: booksql_sqlite
Tools: sqlite_schema, sqlite_query
Project variable: BOOKSQL_SQLITE_MCP_URL
Example value: http://<MACMINI_TAILSCALE_IP>:8788/mcp
```

Set `BOOKSQL_SQLITE_MCP_URL` in the B project's Nao environment settings to the current MacMini Tailscale endpoint. Do not put the address or secrets in source if the endpoint changes; the SQLite MCP itself has no database credential and is restricted to the tailnet interface plus a read-only file mount.

## Boundary

- Direct physical SQLite access is allowed only through `booksql_sqlite`.
- Cube/`cube_semantic` is absent from B.
- `decimal_calculator` is arithmetic-only and is not a data route.
- SQL must be SQLite `SELECT`/`WITH`; the MCP rejects writes and multiple statements and caps rows.
- The Nao project uses only `BOOKSQL_SQLITE_MCP_URL`; database access is through `booksql_sqlite`, not a direct connection.
