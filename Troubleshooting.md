# Troubleshooting

## Installation Issues

### Problem
Dependencies are not installing, or the project is failing to start.

### Solutions
-   Ensure Node.js and npm are correctly installed on your system.
-   Navigate to the project directory and run `npm install`.
-   If the `package-lock.json` or `yarn.lock` file is corrupted, delete `node_modules` and the lock file, then reinstall dependencies. For example:
    ```bash
    rm -rf node_modules package-lock.json
    npm install
    ```
-   Verify that you are using the correct Node.js version as specified in the project requirements.
-   Clear the package manager cache and attempt installation again.

## Known Limitations

### Snowflake Live Data Access

**Description:** The project is functioning correctly across all major features, including AI processing, workflow execution, reporting, and deployment. However, live Snowflake integration is currently unavailable due to exhausted trial credits. Consequently, the demo is operating on fallback API responses in place of real-time Snowflake data.

**Impact:** While the core functionality and AI capabilities remain fully operational, the system will not fetch live data directly from Snowflake until the credit quota is renewed or an alternative live data source is configured. The application is designed to gracefully handle this by utilizing sample or current API responses to ensure continuous demonstration and functionality.

**Resolution:** To restore live Snowflake integration, ensure that the associated Snowflake account has sufficient credits or an active subscription. Alternatively, configure the application to connect to a different live data source if available.
