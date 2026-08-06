# Architecture

## System Overview

IntelliFlow – AI Workflow Automation Agent is an enterprise customer support workflow dashboard built to analyze support tickets, detect anomalies, and generate contextual actions and reports. The system is designed around a multi-step workflow that loads support data, applies AI-style analysis, detects risk patterns, and produces business insights for support teams.

## Frontend

The frontend is built using HTML, CSS, and JavaScript. It contains multiple sections such as Overview, Data Sources, Agent Skills, Workflow Builder, Executions, Anomalies, Insights, Reports, Capabilities, and Settings. The UI also includes a chat-style copilot panel, workflow execution visualization, and report views for support operations.

## Backend/API

During development, the project was successfully integrated with Snowflake, but the current demo uses API responses because the Snowflake trial/credit limit has expired. The application also includes a live FastAPI-based backend connection that can fetch customer support tickets from an external service when available, and it falls back to sample/generated data if the live fetch fails.

## AI Workflow

The AI workflow follows modular agent skills instead of a single prompt-based flow. The main steps are Ticket Analyzer, Anomaly Risk Detector, and Decision Action Agent, which together transform raw customer support tickets into structured findings, risk scores, and recommended actions. The workflow execution is shown visually in the interface and can run end-to-end as a simulated or live process.

## Snowflake Integration

The project is connected to the Snowflake customer support dataset, specifically the `INTELLIFLOWDB.PUBLIC.CUSTOMERSUPPORTDATA` table. The site mentions that real category distribution was pulled from Snowflake and re-verified via CoCo CLI analysis, showing that the data model is based on real support-ticket patterns rather than random content.

## Data Flow

The data flow begins with customer support records being loaded from Snowflake or from the API fallback. The records are then processed by the Ticket Analyzer, passed to the Anomaly Risk Detector for scoring, and then sent to the Decision Action Agent for escalation, monitoring, or normal processing. Finally, the system generates execution logs, insights, and a business report for the user.

## API Communication

The interface communicates with an external FastAPI service when live data is available, and it can also trigger a local CoCo CLI bridge for workflow execution. If the live API or bridge is unavailable, the UI shows simulated or fallback data so the dashboard remains usable during demo mode.

## Security

The project includes a security policy for reporting vulnerabilities by email, and the workflow logic is designed to avoid unsafe silent changes by relying on controlled execution steps and clear branching. The interface also emphasizes explainable actions, which makes support decisions easier to review and audit.

## Future Architecture

Future improvements may include a more permanent live Snowflake connection, stronger automatic retry handling, self-learning optimization, and deeper cross-session memory for agent decisions. The current design already supports a roadmap for richer automation while keeping the workflow modular and explainable.
