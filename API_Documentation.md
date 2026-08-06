# API Documentation

## API Overview

This project leverages the Grok AI API for intelligent workflow reasoning and response generation. The core customer support dataset was originally sourced from Snowflake and accessed via the Snowflake CoCo CLI. However, due to the exhaustion of live credits, the direct Snowflake access path is currently inactive. The application is designed with a fallback mechanism to utilize alternate or sample data sources when direct Snowflake access is unavailable.

The overall workflow is meticulously designed for comprehensive support-ticket analysis, robust anomaly detection, and intelligent decision action generation, all powered by AI-assisted processing.

## Base URLs

-   **Grok AI API Base URL:** `https://api.x.ai/v1`
-   **Project Backend / Data Bridge URL:** `https://snowflake-ticket-fetch--mrspaceresearch.replit.app/api/data/tickets`

## Endpoints

The IntelliFlow AI system interacts with the following key API endpoints:

| Endpoint                     | Method | Description                                     |
| :--------------------------- | :----- | :---------------------------------------------- |
| `/chat/completions`          | `POST` | Generates AI responses and structured analysis. |
| `/api/data/tickets`          | `GET`  | Fetches support ticket data.                    |

## Request & Response Examples

### Grok AI Chat Completion Request

**Request:**
```http
POST /v1/chat/completions
Authorization: Bearer <grok_api_key>
Content-Type: application/json

{
  "model": "grok-2-latest",
  "messages": [
    {
      "role": "system",
      "content": "You are an AI assistant for customer support ticket analysis."
    },
    {
      "role": "user",
      "content": "Summarize the ticket and suggest the next action."
    }
  ],
  "temperature": 0.2
}
```

**Response:**
```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "This ticket is high priority and should be escalated to support."
      }
    }
  ]
}
```

### Snowflake-backed Ticket Fetch

**Request:**
```http
GET /api/data/tickets
```

**Response:**
```json
[
  {
    "ticket_id": "TKT-1001",
    "customer_name": "Aarav Sharma",
    "category": "Refund",
    "priority": "High",
    "status": "Open"
  }
]
```

## Authentication

The Grok AI API utilizes **Bearer token authentication**. API keys must be included in the `Authorization` header of each request:

`Authorization: Bearer <grok_api_key>`

If the application employs a backend bridge for Snowflake data, additional authentication may be required for that service, depending on its specific deployment configuration.

## Error Codes

*(Specific error codes were not provided in the project evidence. Implementations should refer to the Grok AI API documentation for detailed error handling.)*

## Rate Limits

Exact rate limits for both the Grok AI API and the project's backend were not explicitly defined in the provided project evidence. For documentation purposes, it is important to note that the effective rate limit will depend on the deployed Grok AI plan and any separate limits imposed by the backend service or Snowflake bridge.

## Snowflake/API Integration Flow

The integration flow for customer support records is as follows:

1.  **Data Sourcing:** Customer support records are initially sourced from Snowflake via the Snowflake CoCo CLI or through a dedicated backend bridge.
2.  **Fallback Mechanism:** In instances where live Snowflake access fails (e.g., due to exhausted credits), the system gracefully falls back to sample or generated data, ensuring the interface remains functional and usable.
3.  **AI Processing:** The Grok AI API processes the ingested ticket text, performing summarization of issues and assisting in the generation of recommendations.
4.  **Workflow Application:** The processed data then undergoes a series of workflow steps, including ticket analysis, anomaly detection, and the application of decision actions.
5.  **Output Generation:** Finally, the system generates comprehensive insights and integrates them into the report/dashboard output for user review.
