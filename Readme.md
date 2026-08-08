# IntelliFlow – AI Workflow Automation Agent

IntelliFlow AI is an enterprise customer support intelligent workflow automation agent created for the Snowflake CoCoCli Hackathon.

## Team
- **Team Name:** project mogembo
- **Creator:** Kush Kumar Yadav

## Hackathon
- Snowflake CoCoCli Hackathon

## Live Demo
- [https://initialflowagent.netlify.app/](https://initialflowagent.netlify.app/)

## GitHub Repository
- [https://github.com/mrspaceresearcher-stack/intelliflow-ai](https://github.com/mrspaceresearcher-stack/intelliflow-ai)

## Project Theme
Enterprise Customer Support Intelligent Workflow Automation Agent

## About the Project
IntelliFlow AI is a workflow-based customer support platform designed to enhance the efficiency of support teams by analyzing support tickets, detecting anomalies, and generating contextual actions and reports. This system is built to manage large volumes of tickets through automated reasoning and decision branching, thereby streamlining customer support operations.

## Website Features
The IntelliFlow AI platform offers a comprehensive suite of features to facilitate intelligent workflow automation:

| Feature                       | Description                                                               |
| :---------------------------- | :------------------------------------------------------------------------ |
| Overview Dashboard            | Provides a centralized view for monitoring support workflows.             |
| Data Sources                  | Allows integration with Snowflake tables and CSV upload for data ingestion. |
| Agent Skills                  | Modular AI processing units for specialized tasks.                        |
| Workflow Builder              | Enables the creation and customization of automation logic.               |
| Executions History            | Tracks and logs all completed workflow runs.                              |
| Anomaly Detection             | Identifies high-risk tickets requiring immediate attention.               |
| Insights and Reports          | Generates business analysis and performance reports.                      |
| Capabilities and Settings     | Provides controls for system configuration and management.                |

## Workflow Process
The core workflow within IntelliFlow AI follows a structured, automated process:

1.  **Load Customer Support Data:** Ingests customer support tickets from various sources.
2.  **Analyze Ticket Content and Structure:** Processes the textual and structural elements of each ticket.
3.  **Detect Anomalies and High-Risk Cases:** Identifies unusual patterns or critical issues within the tickets.
4.  **Apply Decision Logic for Escalation or Monitoring:** Determines appropriate actions based on detected anomalies and predefined rules.
5.  **Generate Contextual Actions and Reports:** Creates actionable recommendations and comprehensive reports for support teams.

## Core Agent Skills
IntelliFlow AI leverages specialized AI agents to perform its core functions:

-   **Ticket Analyzer:** Processes and understands the content of support tickets.
-   **Anomaly Risk Detector:** Identifies and scores the risk level of anomalous tickets.
-   **Decision Action Agent:** Recommends and executes appropriate actions based on analysis.

## Snowflake Integration
The application was successfully integrated with Snowflake during its development phase, utilizing the Snowflake customer support dataset to process enterprise support records. The current demo, however, relies on API responses due to the expiration of the Snowflake trial/credit limit.

## Snowflake Connection Proof

This section documents the working end-to-end connection between the frontend and a live Snowflake account, established via Snowflake's SQL API (`/api/v2/statements`), authenticated with a Programmatic Access Token (PAT). No intermediate backend server is required — the browser calls Snowflake directly.

### 1. Account Identity (verified via SQL)

```sql
SELECT CURRENT_ACCOUNT() AS account, CURRENT_REGION() AS region,
       CURRENT_USER() AS username, CURRENT_WAREHOUSE() AS warehouse;
```

| Field | Value |
|---|---|
| Account (locator) | `TX71804` |
| Region | `AWS_AP_SOUTHEAST_7` |
| Username | `MRSPACE` |
| Warehouse | `COMPUTE_WH` |

```sql
SELECT CURRENT_ORGANIZATION_NAME() AS org_name, CURRENT_ACCOUNT_NAME() AS account_name;
```

| Field | Value |
|---|---|
| Org Name | `QNHCMMP` |
| Account Name | `FI07235` |
| Account identifier used in connection | `QNHCMMP-FI07235` |

### 2. Database Objects Created

```sql
CREATE DATABASE IF NOT EXISTS ANALYTICS_DB;
CREATE SCHEMA IF NOT EXISTS ANALYTICS_DB.PUBLIC;
CREATE TABLE IF NOT EXISTS ANALYTICS_DB.PUBLIC.AUTOMATION_LOGS (
  source_name STRING,
  summary STRING,
  created_at TIMESTAMP_NTZ DEFAULT CURRENT_TIMESTAMP()
);
```

Table structure confirmed via `DESCRIBE TABLE`:

| Column | Type | Nullable |
|---|---|---|
| `LOG_ID` | `NUMBER(38,0)` (identity, auto-increment) | N |
| `SOURCE_NAME` | `VARCHAR(16777216)` | Y |
| `SUMMARY` | `VARCHAR(16777216)` | Y |
| `CREATED_AT` | `TIMESTAMP_NTZ(9)` | Y |

### 3. Live Data Insert (used to verify the connection is real-time, not cached)

```sql
INSERT INTO ANALYTICS_DB.PUBLIC.AUTOMATION_LOGS (source_name, summary) VALUES
('ORDER', 'Customer asking about order status'),
('REFUND', 'Customer requesting refund for damaged item'),
('ACCOUNT', 'Customer unable to login to account');
```

Result: `number of rows inserted: 3`

### 4. Frontend Connection Code (`index.html`)

```javascript
const SNOWFLAKE_ACCOUNT   = "QNHCMMP-FI07235";
const SNOWFLAKE_WAREHOUSE = "COMPUTE_WH";
const SNOWFLAKE_DATABASE  = "ANALYTICS_DB";
const SNOWFLAKE_SCHEMA    = "PUBLIC";
const SNOWFLAKE_ROLE      = "ACCOUNTADMIN";
const REAL_API_URL = `https://${SNOWFLAKE_ACCOUNT}.snowflakecomputing.com/api/v2/statements`;
const SNOWFLAKE_QUERY = "SELECT SOURCE_NAME, SUMMARY, CREATED_AT FROM AUTOMATION_LOGS ORDER BY CREATED_AT DESC LIMIT 500";

async function fetchRealTickets(fallbackCount){
  try{
    const res = await fetch(REAL_API_URL, {
      method: "POST",
      headers: {
        "Authorization": "Bearer " + SNOWFLAKE_TOKEN, // PAT, redacted here
        "Content-Type": "application/json",
        "Accept": "application/json",
        "X-Snowflake-Authorization-Token-Type": "PROGRAMMATIC_ACCESS_TOKEN"
      },
      body: JSON.stringify({
        statement: SNOWFLAKE_QUERY,
        warehouse: SNOWFLAKE_WAREHOUSE,
        database: SNOWFLAKE_DATABASE,
        schema: SNOWFLAKE_SCHEMA,
        role: SNOWFLAKE_ROLE,
        timeout: 30
      })
    });
    if(!res.ok) throw new Error("Snowflake API returned "+res.status);
    const json = await res.json();
    const rows = json && json.data ? json.data : [];
    if(!Array.isArray(rows) || rows.length===0) throw new Error("Empty response from Snowflake");
    STATE.dataSource = "live";
    return rows.map((r,i)=>mapRealRowToTicket(r,i));
  }catch(e){
    console.warn("Live Snowflake fetch failed, falling back to sample data:", e);
    STATE.dataSource = "sample";
    return generateTickets(fallbackCount);
  }
}
```

> **Note on the token:** the actual PAT (`SNOWFLAKE_TOKEN`) used to authenticate is intentionally **not included** in this document or committed to source control — it is a live credential and including it here would let anyone read/write the connected Snowflake account. Only structural/config values (account id, warehouse, database, schema) are shown.

### 5. Result Observed in the Live App

After the insert above, the dashboard's **Tickets Loaded** metric (tagged `LIVE`) reflected the current live row count from `AUTOMATION_LOGS` rather than a hardcoded number, and a new execution entry (`WF-001`) appeared with the current date/timestamp and a `Records` count matching the table's live row count at query time — confirming the value was read from Snowflake at request time, not from a static/sample dataset.

| Component | Value |
|---|---|
| Snowflake Account | `QNHCMMP-FI07235` |
| Database | `ANALYTICS_DB` |
| Schema | `PUBLIC` |
| Table | `AUTOMATION_LOGS` |
| Warehouse | `COMPUTE_WH` |
| Auth method | Bearer token (Programmatic Access Token) |
| Connection type | Direct browser → Snowflake SQL API (`/api/v2/statements`), no middle backend |

## Use Case
IntelliFlow AI is particularly beneficial for enterprise customer support teams seeking to achieve faster ticket triage, accurate risk detection, and automated response recommendations, ultimately leading to improved operational efficiency and customer satisfaction.

## Tech/Implementation Highlights
-   Workflow-based dashboard for intuitive operation.
-   Advanced customer support analytics for data-driven insights.
-   Conditional automation for flexible and intelligent workflows.
-   Explainable anomaly scoring to provide transparency in risk detection.
-   Comprehensive report generation and execution tracking for accountability and performance monitoring.

## Architecture Overview

IntelliFlow AI is built around a robust, multi-step workflow. The frontend is developed using HTML, CSS, and JavaScript, providing an interactive user interface with a chat-style copilot panel and workflow visualization. The backend is a FastAPI-based service that integrates with external services and includes a fallback mechanism to sample data when live data sources are unavailable. The AI workflow is driven by modular agent skills, including the Ticket Analyzer, Anomaly Risk Detector, and Decision Action Agent, which collaboratively process tickets, assess risks, and recommend actions.

## API Integration

The project uses the Grok AI API for intelligent workflow reasoning and response generation. While originally integrated with Snowflake for data sourcing, the current demo uses API responses. Key endpoints include `/chat/completions` (POST) for AI responses and `/api/data/tickets` (GET) for fetching ticket data, secured by Bearer token authentication.

## Deployment

Deployment involves both the frontend dashboard and the API/data bridge. Prerequisites include Node.js, npm, a Grok AI API key, and deployment credentials. The build process uses `npm run build`, and production deployment typically separates the frontend and backend. The system is designed for resilience, gracefully falling back to sample data if live Snowflake access is limited.

## Contributing

Contributions are welcome! You can contribute by reporting bugs, suggesting features, improving documentation, fixing issues, or submitting pull requests. Please refer to the [Contributing Guidelines](./contributing_guidelines.md) for detailed instructions.

## Code of Conduct

We are committed to providing a welcoming, respectful, and inclusive environment. All participants are expected to treat others with respect and professionalism. Harassment or discriminatory behavior will not be tolerated. Please refer to the [Code of Conduct](./code_of_conduct.md) for more details and reporting procedures.

## Security Policy

Security updates are applied to the latest available version. If you discover a security vulnerability, please report it immediately to `mrspaceresearcher@gmail.com`. We advocate for responsible disclosure. For more information, see the [Security Policy](./security_policy.md).

## Changelog

All notable changes to this project are documented in the [Changelog](./changelog.md). Recent updates include:
-   **v1.4.0:** Bug fixes and performance improvements.
-   **v1.3.0:** Dashboard improvements.
-   **v1.2.0:** Snowflake integration added.
-   **v1.1.0:** Added AI Workflow Builder.
-   **v1.0.0:** Initial release.

## Roadmap

The project roadmap outlines the evolution towards a more intelligent, connected, and enterprise-ready product. Future releases will focus on AI improvements, stronger live Snowflake integration, multi-agent workflows, and advanced notifications. See the [Roadmap](./roadmap.md) for planned features.

## Support

For support, please contact `mrspaceresearcher@gmail.com`. Details on bug reporting, feature requests, and response times are available in the [Support Document](./SUPPORT.md).

## Frequently Asked Questions (FAQ)

For answers to common questions about IntelliFlow AI, its functionality, and technical aspects, please refer to the [FAQ Document](./FAQ.md).

## .gitignore Configuration

The `.gitignore` file specifies patterns for files and directories that should be ignored by Git, such as environment variables, `node_modules/`, build outputs, log files, and editor-specific configurations. This ensures a clean repository and protects sensitive information. For the full configuration, refer to the [gitignore_config.md](./gitignore_config.md) file.

## Copyright

Copyright (c) 2026 Kush Kumar Yadav. All rights reserved. This software and its source code are the property of Kush Kumar Yadav. No unauthorized use, copying, modification, distribution, publication, sublicense, or sale is permitted without prior written permission from the owner. For permissions, contact `mrspaceresearcher@gmail.com`.
