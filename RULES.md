# Scenario B rules

This project tests direct access to BookSQL's physical SQLite schema through a dedicated read-only MCP.

## Required data route

1. Use only MCP server `booksql_sqlite` for BookSQL data. Use `mcp_call` with tool `sqlite_schema` to inspect physical tables/columns and `sqlite_query` to execute SQL. Use `mcp_connect` only when tool discovery is needed.
2. Never use Nao's native `execute_sql`, Cube, another data MCP, or a different database. If `booksql_sqlite` is unavailable or a query fails, report the route failure; do not fall back.
3. Write one SQLite `SELECT` or `WITH` query per call. The server enforces read-only mode, a maximum of 1,000 returned rows, and a response-size cap. Add filters/aggregations to keep requests focused; report truncation rather than treating partial output as complete.
4. Use exact table and column names returned by `sqlite_schema`. Use SQLite syntax and functions; do not assume behavior from another SQL dialect or undeclared fields/relationships.
5. The physical schema is technical context only. Do not invent accounting policies, sign conventions, date rules, or formulas not stated by the question or benchmark; for relative dates, follow the shared temporal reference below.
6. Report the route and relevant filters/assumptions. Do not claim a query succeeded if the MCP returned an error or truncated result that is insufficient.

The only other permitted MCP is `decimal_calculator.calculate`, solely for arithmetic over numeric inputs already returned by `booksql_sqlite`. It must not perform data retrieval, filtering, aggregation, or business-rule selection.

## Required arithmetic: `decimal_calculator.calculate`

For each user-facing derived result (average, ratio, percentage, difference, or multi-step expression), retrieve the required inputs using `booksql_sqlite` and call `decimal_calculator.calculate`; do not perform final arithmetic mentally or substitute an unrelated route. Pass the exact values returned by SQLite, without currency symbols or thousands separators. Keep values unrounded through intermediate steps and round only the final display if the prompt explicitly requests it. If the calculator fails, state that the derived value could not be verified rather than silently calculating it yourself.

Keep database semantics separate from arithmetic: SQLite selects, filters, and aggregates the source rows; the calculator only evaluates a formula over the resulting values. Never send raw table rows to the calculator for database aggregation.

## Shared BookSQL benchmark temporal reference

For BookSQL benchmark questions, use `2022-06-01` as the fixed `as_of_date`, matching the date used by the official evaluator for its supported temporal replacements. This does not mean every date/time SQL expression is normalized. The anchor overrides the actual system/database clock only for interpreting temporal expressions in this benchmark.

Use this date only to interpret relative time wording or query predicates. It is not a data cutoff: do not add a blanket `transaction_date <= '2022-06-01'`, do not suppress later transactions, and do not add a date filter when the question has no temporal condition. Preserve the original questions, Gold SQL, and source data.

When forming a query for a relative period, resolve it to explicit absolute ISO dates from this anchor. Preserve the question's intended period and the Gold predicate's boundary semantics; distinguish a full calendar period (for example, “this month”) from a period-to-date (for example, “month to date”). For Cube, use absolute date-range endpoints rather than runtime-relative ranges. For SQL, do not rely on `CURRENT_DATE` or `now` at query time. If an evaluator must normalize unchanged Gold SQL, normalize a separate execution copy and use the same anchor for Gold and predictions. Temporal normalization must not alter text literals or other query semantics.

This is a date-only reference; it does not define a time of day or timezone for `CURRENT_TIMESTAMP`/`now`. Do not invent those values unless the benchmark protocol separately specifies them.

### Fiscal-year interpretation

For this BookSQL benchmark, the fiscal year runs from April 1 through March 31. With `as_of_date = 2022-06-01`, interpret “this fiscal year” and “this fiscal year to date” as 2022-04-01 through 2022-06-01, inclusive. “Last fiscal year” means 2021-04-01 through 2022-03-31, inclusive. Only an explicit request for the full FY 2022–23 uses 2022-04-01 through 2023-03-31.

In the validation split, 150 Gold queries worded “this fiscal year to date” instead cover the full FY through 2023-03-31. Preserve those questions and Gold SQL unchanged, but flag/report them separately as Gold/wording inconsistencies. Do not reproduce that full-year range when answering a genuine “to date” question or silently rewrite Gold.

## First-turn response requirement
Always answer the user's question in the current response and provide every requested field; if data is unavailable, state that explicitly without inventing values or deferring the answer to a follow-up.
