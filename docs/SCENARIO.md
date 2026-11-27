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

## Fiscal-year convention

BookSQL fiscal years run from April 1 through March 31, as implied by the authors' [DIN-SQL example](https://github.com/Exploration-Lab/BookSQL/blob/main/GPT/DIN-SQL.py#L1584). With the benchmark reference date 2022-06-01, “this fiscal year” and “this fiscal year to date” mean 2022-04-01 through 2022-06-01; “last fiscal year” means 2021-04-01 through 2022-03-31. A full FY 2022–23, when explicitly requested, means 2022-04-01 through 2023-03-31.

The validation split has 150 Gold queries worded “this fiscal year to date” whose SQL instead covers the full FY through 2023-03-31. Keep questions and official Gold unchanged; flag these cases separately as Gold/wording inconsistencies.
