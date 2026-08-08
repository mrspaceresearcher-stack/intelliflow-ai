# Snowflake Connection — Proof of Successful Setup

**Website:** IntelliFlow AI (Intelligent Workflow Automation Agent)
**Date verified:** August 8, 2026
**Connection method:** Direct browser → Snowflake SQL API (`/api/v2/statements`)

---

## Summary

The website's front-end (`index.html`) was connected directly to Snowflake using the
Snowflake SQL API, authenticated with a Programmatic Access Token (PAT). No backend
server is required — the browser calls Snowflake's REST API directly and reads live
rows from the `ANALYTICS_DB.PUBLIC.AUTOMATION_LOGS` table.

Below is step-by-step evidence, in order, showing the account was identified,
data was inserted into the live table, and the website subsequently displayed that
live data with a "LIVE" status badge.

---

## 1. Snowflake Account Identified

Query run in a Snowflake worksheet to retrieve account credentials needed for the connection:

```sql
SELECT CURRENT_ACCOUNT() AS account, CURRENT_REGION() AS region,
       CURRENT_USER() AS username, CURRENT_WAREHOUSE() AS warehouse;
```

**Result:**
- Account: `TX71804`
- Region: `AWS_AP_SOUTHEAST_7`
- Username: `MRSPACE`
- Warehouse: `COMPUTE_WH`

![Account details query result](screenshots/01-account-details-query.jpg)

---

## 2. Organization & Account Name (final connection identifier)

```sql
SELECT CURRENT_ORGANIZATION_NAME() AS org_name, CURRENT_ACCOUNT_NAME() AS account_name;
```

**Result:**
- Org Name: `QNHCMMP`
- Account Name: `FI07235`
- Final account identifier used in the connection URL: `QNHCMMP-FI07235`

![Org and account name query result](screenshots/02-org-account-name.jpg)

---

## 3. Live Test Data Inserted into Snowflake

To verify the connection reads real, current data (not cached/sample data), 3 new
rows were inserted directly into the table the website queries:

```sql
INSERT INTO ANALYTICS_DB.PUBLIC.AUTOMATION_LOGS (source_name, summary) VALUES
('ORDER', 'Customer asking about order status'),
('REFUND', 'Customer requesting refund for damaged item'),
('ACCOUNT', 'Customer unable to login to account');
```

**Result:** `number of rows inserted: 3` ✅

![Snowflake insert confirms 3 rows inserted](screenshots/03-data-inserted-snowflake.jpg)

---

## 4. Website Dashboard Showing Live Connected Data

Immediately after inserting the rows above, the website's dashboard was opened.
It shows:

- **"Tickets Loaded: 9"** with a green **"LIVE"** badge — confirming the count
  reflects the actual current row count in Snowflake (not a hardcoded sample number).
- A new execution record **`WF-001`**, timestamped **`08/08/2026, 09:49:49`**
  (today's date/time) with **Records: 9** — matching the live table state.
- Older sample rows (`EXE-001`–`EXE-003`, dated 2024) are clearly differentiated
  as historical/sample data, confirming the new row is genuinely live.

![Website dashboard showing LIVE tickets loaded from Snowflake](screenshots/04-live-dashboard-connected.jpg)

---

## Conclusion

✅ **Connection confirmed working.** The website successfully authenticates to
Snowflake via the SQL API and retrieves live data from the
`ANALYTICS_DB.PUBLIC.AUTOMATION_LOGS` table — verified by inserting new rows in
Snowflake and observing the website's dashboard update to reflect the new, correct
row count in real time.

| Component | Value |
|---|---|
| Snowflake Account | `QNHCMMP-FI07235` |
| Database | `ANALYTICS_DB` |
| Schema | `PUBLIC` |
| Table | `AUTOMATION_LOGS` |
| Warehouse | `COMPUTE_WH` |
| Connection type | Direct browser → Snowflake SQL API (Bearer token / PAT) |
