# IntelliFlow AI — Intelligent Workflow Automation Copilot

IntelliFlow is an AI-powered workflow automation dashboard that helps businesses monitor, analyze, and automate their operations. It features an intelligent AI Copilot powered by **Groq (Llama 3)** to provide real-time insights and data analysis.

## 🚀 Features
- **Intelligent AI Copilot**: Context-aware chat powered by Groq Llama 3.
- **Secure Architecture**: API keys are handled securely via a Node.js backend proxy.
- **Live Dashboard**: Monitor tickets, anomalies, and workflow executions.
- **Data Analysis**: Upload CSV/Excel files for instant AI-driven insights.
- **Hinglish Support**: Friendly AI that understands both English and Hindi.

## 🛡️ Security
This project follows industry best practices for security:
- **Environment Variables**: API keys are stored in `.env` files and never pushed to the repository.
- **Backend Proxy**: The frontend never talks to the AI provider directly, preventing API key exposure.

## 🛠️ Setup Instructions
1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-username/intelliflow-ai.git
   cd intelliflow-ai
   ```
2. **Install dependencies**:
   ```bash
   npm install
   ```
3. **Configure Environment Variables**:
   - Create a `.env` file in the root directory.
   - Add your Groq API key:
     ```env
     GROQ_API_KEY=your_gsk_key_here
     ```
4. **Start the server**:
   ```bash
   node server.js
   ```
5. **Open the App**:
   Open `index.html` in your browser.

## 🏆 Hackathon Submission
This project was built for [Hackathon Name]. It demonstrates the power of combining Snowflake-style data analytics with high-speed LLMs like Llama 3 via Groq.
