# Scenario B rules

This project tests direct access to BookSQL's physical SQLite schema through a dedicated read-only MCP.

## Required data route

1. Use only MCP server `booksql_sqlite` for BookSQL data. Use `mcp_call` with tool `sqlite_schema` to inspect physical tables/columns and `sqlite_query` to execute SQL. Use `mcp_connect` only when tool discovery is needed.
2. Never use Nao's native `execute_sql`, Cube, another data MCP, or a different database. If `booksql_sqlite` is unavailable or a query fails, report the route failure; do not fall back.
3. Write one SQLite `SELECT` or `WITH` query per call. The server enforces read-only mode, a maximum of 1,000 returned rows, and a response-size cap. Add filters/aggregations to keep requests focused; report truncation rather than treating partial output as complete.
4. Use exact table and column names returned by `sqlite_schema`. Use SQLite syntax and functions; do not assume PostgreSQL-specific behavior or undeclared fields/relationships.
5. The physical schema is technical context only. Do not invent accounting policies, sign conventions, date rules, or formulas not stated by the question or benchmark.
6. Report the route and relevant filters/assumptions. Do not claim a query succeeded if the MCP returned an error or truncated result that is insufficient.

The only other permitted MCP is `decimal_calculator.calculate`, solely for arithmetic over numeric inputs already returned by `booksql_sqlite`. It must not perform data retrieval, filtering, aggregation, or business-rule selection.

## Required arithmetic: `decimal_calculator.calculate`

For each user-facing derived result (average, ratio, percentage, difference, or multi-step expression), retrieve the required inputs using `booksql_sqlite` and call `decimal_calculator.calculate`; do not perform final arithmetic mentally or substitute an unrelated route. Pass the exact values returned by SQLite, without currency symbols or thousands separators. Keep values unrounded through intermediate steps and round only the final display if the prompt explicitly requests it. If the calculator fails, state that the derived value could not be verified rather than silently calculating it yourself.

Keep database semantics separate from arithmetic: SQLite selects, filters, and aggregates the source rows; the calculator only evaluates a formula over the resulting values. Never send raw table rows to the calculator for database aggregation.

## First-turn response requirement
Always answer the user's question in the current response and provide every requested field; if data is unavailable, state that explicitly without inventing values or deferring the answer to a follow-up.
