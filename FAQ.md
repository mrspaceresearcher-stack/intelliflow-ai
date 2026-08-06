# FAQ.md

## What is IntelliFlow AI?

IntelliFlow AI is an AI-powered workflow platform that transforms enterprise or customer support data into intelligent actions. It analyzes tickets, detects anomalies, makes decisions, and generates reports automatically.

## How does it work?

IntelliFlow AI follows a multi-step workflow:

1.  It collects customer support records from Snowflake, CSV uploads, or sample data.
2.  The Ticket Analyzer reads each record and extracts category, sentiment, and urgency.
3.  The Risk Detector flags unusual patterns and scores ticket risk.
4.  The Decision Agent chooses the next action based on the risk level.
5.  A business report and AI insights are generated at the end.

## Does it use Snowflake?

Yes. The project is built around Snowflake-backed data, and the workflow is executed through Snowflake CoCo CLI. If live Snowflake access is unavailable, the app can fall back to sample data.

## How do I run the project?

Typical local setup is:

```bash
npm install
npm run dev
```
