# Deployment

## Deployment Overview

This project is an AI-powered workflow application that analyzes customer support data, detects anomalies, and generates actions and reports. The system is designed to work with Snowflake-backed data, with workflow execution through Snowflake CoCo CLI, and can also fall back to sample or generated data when live access is unavailable.

The deployment should support both the frontend dashboard and the API/data bridge used for Grok AI integration and Snowflake access.

## Prerequisites

Before deploying, make sure you have:

- Node.js and npm installed.
- Access to the project repository.
- A Grok AI API key.
- Snowflake access, if you plan to connect live data.
- Deployment credentials for your chosen hosting platform.

## Local Setup

1. Clone the repository.
2. Install dependencies.
3. Configure environment variables.
4. Run the app locally.
5. Verify that the dashboard loads and the workflow can read ticket data.

Example:
```bash
npm install
npm run dev
```

Only store secrets in environment variables, never in frontend code.

## Build Process

The project should be built for production using the standard build command for the framework in use.

Example:
```bash
npm run build
```
After build, verify:
- Static assets are generated correctly.
- API calls point to the correct production endpoints.
- Snowflake or fallback data flow still works.
- Grok AI requests are authenticated properly.

## Production Deployment

For production, deploy the frontend and backend separately if needed.
- Frontend: host the dashboard on a static or app platform.
- Backend/API: host the Grok AI and Snowflake bridge service securely.
- Ensure environment variables are set in the production environment.
- Confirm that the workflow can still run when live Snowflake access is limited.
- If Snowflake credits are exhausted, the app should not crash; it should switch to sample/generated data and continue displaying the workflow results.

## Netlify/Vercel Deployment

### Netlify
- Connect the repository to Netlify.
- Set the build command.
- Set the publish directory.
- Add environment variables in the Netlify dashboard.
- Deploy and test API connectivity.

### Vercel
- Import the repository into Vercel.
- Set framework defaults if detected automatically.
- Add environment variables in Project Settings.
- Deploy the frontend.
- Verify the API bridge and Grok AI connection.

If the frontend is fully static, use environment variables carefully because public client-side variables may be exposed. Keep secret keys on the server side only.

## API Configuration

The app uses Grok AI API for reasoning and response generation, and a Snowflake-backed data source for customer support data. The project evidence also shows a backend bridge endpoint used to fetch ticket data from a Replit-hosted service.

Recommended API setup:
- Store `GROK_API_KEY` on the backend only.
- Use `BACKEND_API_URL` for the Snowflake/data bridge.
- Add authorization headers when required.
- Handle fallback when the data bridge or Snowflake query fails.

Example request:
```http
GET /api/data/tickets
Authorization: Bearer <token>
```

## Troubleshooting

### Snowflake connection fails
- Check Snowflake credentials.
- Confirm warehouse, database, and schema values.
- Verify that the account still has available credit or active access.
- Use fallback sample data if live access is unavailable.

### Grok API request fails
- Confirm the API key is valid.
- Check request headers.
- Verify the model name and endpoint format.
- Review rate limiting or quota issues.

### Frontend does not load data
- Confirm the backend API URL is correct.
- Check network errors in browser dev tools.
- Ensure CORS is configured properly.
- Confirm fallback data is enabled.

### Deployment builds but app is blank
- Check console errors.
- Confirm environment variables are set.
- Verify the correct build output folder is being deployed.
- Make sure the API endpoints are reachable from production.

## Maintenance

Keep the deployment healthy by doing the following:
- Rotate API keys regularly.
- Monitor Snowflake usage and credit balance.
- Review backend logs for failed workflow runs.
- Update fallback data when the schema changes.
- Retest API integration after every deployment.

The workflow is modular, so Ticket Analyzer, Risk Detector, and Decision Agent logic can be updated independently without rebuilding the entire system.
