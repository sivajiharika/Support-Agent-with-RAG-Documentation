Support Agent with RAG Documentation

An AI-powered Support Agent that uses Retrieval-Augmented Generation (RAG) to answer user questions from project documentation.

The project uses n8n as the workflow orchestration layer. A web-based chat interface sends a user's question to an n8n webhook, n8n retrieves relevant information from the documentation/knowledge base, passes the retrieved context to an AI model, and returns the generated answer to the chatbot.

🚀 Project Overview

The goal of this project is to build a documentation-aware support chatbot that can provide answers based on a defined knowledge base instead of relying only on the AI model's general knowledge.

High-level flow

User
  │
  ▼
Web Chatbot
  │
  │  User question
  ▼
n8n Webhook
  │
  ▼
Question Processing
  │
  ▼
RAG Retrieval
  │
  │  Relevant documentation
  ▼
AI / LLM
  │
  │  Generated answer
  ▼
n8n Webhook Response
  │
  ▼
Web Chatbot
  │
  ▼
User

✨ Features

Web-based support chatbot

n8n webhook integration

Retrieval-Augmented Generation (RAG)

Documentation-based question answering

Context retrieval before generating an answer

AI-generated responses

Conversation/session support

Easy integration with an existing website

Centralized workflow management through n8n

Extensible documentation and knowledge sources

🏗️ Architecture

The system consists of three main parts.

1. Web Chatbot

The frontend provides a chat interface where users can enter a question, send it to the n8n webhook, and view the response returned by n8n.

Example request:

{
  "message": "What services does the company provide?",
  "query": "What services does the company provide?",
  "sessionId": "unique-session-id",
  "pageUrl": "https://example.com",
  "pageTitle": "Support Agent"
}

2. n8n Workflow

n8n acts as the orchestration layer:

Webhook
   ↓
Receive User Query
   ↓
Prepare / Normalize Query
   ↓
RAG Retriever
   ↓
Retrieve Relevant Documentation
   ↓
AI Agent / LLM
   ↓
Generate Answer
   ↓
Respond to Webhook

The exact nodes can be changed depending on the selected vector database, embedding model, and AI provider.

3. RAG Knowledge Base

The RAG layer contains the documentation used by the support agent. Documents are processed so that relevant sections can be retrieved when a user asks a question. The retrieved chunks are then supplied to the AI model as context.

🧠 How RAG Works

RAG stands for Retrieval-Augmented Generation.

Instead of sending only the user's question to the AI model, the workflow first searches the project's knowledge base.

User Question
     │
     ▼
Search Documentation
     │
     ▼
Relevant Documentation Chunks
     │
     ▼
AI Model + Retrieved Context
     │
     ▼
Grounded Support Response

This makes the support agent useful for project-specific documentation because the model can use information retrieved from the configured knowledge base.

🔄 Workflow

Step 1 — User asks a question

The user enters a question in the web chatbot.

Example:

What services does the company provide?

Step 2 — Chatbot calls the n8n webhook

The frontend sends the question using an HTTP POST request.

fetch(WEBHOOK_URL, {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    message: userMessage,
    query: userMessage
  })
});

Step 3 — n8n receives the request

The n8n Webhook node receives the user's query. The workflow extracts the message and prepares it for retrieval.

Step 4 — Retrieve documentation

The RAG component searches the configured knowledge base for relevant documentation. The retrieved chunks are provided as context for the AI model.

Step 5 — Generate the response

The AI model uses the user question, retrieved documentation, and system instructions to generate the support response.

A useful system instruction is:

You are a documentation-based support agent.

Answer the user's question using the retrieved documentation.

If the documentation does not contain enough information to answer the question,
say that the information is not available in the documentation rather than
inventing an answer.

Keep responses clear, concise, and helpful.

Step 6 — Return the response

n8n returns the generated response to the web chatbot.

Example:

{
  "reply": "Apex provides global sourcing and procurement, supplier verification, freight coordination, and cross-border regulatory and compliance support."
}

The chatbot displays the returned reply to the user.

📁 Suggested Repository Structure

support-agent-rag/
│
├── README.md
├── frontend/
│   └── index.html
├── n8n/
│   └── support-agent-workflow.json
├── documents/
│   └── README.md
└── screenshots/
    └── chatbot.png

The exact structure can be adjusted based on the final project implementation.

⚙️ n8n Setup

1. Create an n8n workflow

Create a new workflow in n8n.

2. Add a Webhook node

Configure the webhook to receive POST requests. The frontend should call the generated webhook URL.

Example:

POST /webhook/support-agent

3. Process the incoming message

Read the user message from the webhook payload, for example:

{{$json.message}}

4. Connect the RAG pipeline

Configure the retrieval component to search the documentation knowledge base. Pass the retrieved content to the AI Agent/LLM as context.

5. Generate the response

Configure the AI model to answer using the retrieved documentation.

6. Return the response

Configure the n8n workflow to return a JSON response such as:

{
  "reply": "Your generated support response"
}

🔐 Environment Variables and Secrets

Do not commit API keys, passwords, tokens, or other secrets to GitHub.

Use environment variables or the credential management provided by n8n.

Examples of values that should remain private:

AI API keys
Vector database credentials
Webhook authentication tokens
Database passwords
OAuth credentials

🌐 CORS

Because the chatbot runs in a browser and communicates with an n8n webhook, the webhook endpoint must allow requests from the frontend's origin.

During local development, avoid opening the HTML directly with file:///. Instead, serve the frontend through a local HTTP server:

python -m http.server 8000

Then open:

http://localhost:8000/

Configure the webhook/server CORS policy according to the origin from which the frontend is served.

🧪 Testing

Test 1 — Webhook

Send a test request directly to the n8n webhook:

{
  "message": "What services are available?"
}

Verify that n8n receives the request.

Test 2 — RAG retrieval

Ask a question whose answer exists in the documentation and verify that relevant documentation is retrieved.

Test 3 — AI response

Verify that the AI generates an answer using the retrieved context.

Test 4 — Frontend

Verify the complete flow:

User question
      ↓
Frontend
      ↓
n8n webhook
      ↓
RAG retrieval
      ↓
AI model
      ↓
n8n response
      ↓
Frontend

Test 5 — Unknown questions

Ask a question that is not covered by the documentation. The agent should avoid inventing information and should indicate that the documentation does not provide enough information.

🛠️ Troubleshooting

Chatbot says it cannot connect to the webhook

Check:

Webhook URL

n8n workflow status

Network connectivity

CORS configuration

Browser developer console

Browser Network tab

HTTP response status

n8n execution logs

Webhook receives the message but chatbot shows no answer

Check the response returned by n8n. The frontend expects a response similar to:

{
  "reply": "Your answer here"
}

If the workflow returns another field name, update the frontend response parser accordingly.

RAG returns irrelevant information

Check document quality, document chunking, embedding configuration, vector search configuration, retrieval parameters, metadata filtering, and the number of retrieved documents.

AI invents answers

Strengthen the system prompt so the AI must use retrieved documentation and explicitly state when the required information is unavailable.

📌 Example Questions

Depending on the documentation loaded into the RAG knowledge base, users can ask:

What services does the company provide?

Where does the company operate?

What products are supported?

How does the company handle logistics?

What documentation is available for this process?

The chatbot should answer using the relevant retrieved documentation.

🔮 Future Improvements

Streaming AI responses

Conversation history

User authentication

Source/document citations in responses

Feedback buttons

Conversation logging

Multiple knowledge bases

Document upload and automatic indexing

Metadata-based retrieval

Role-based support agents

Human-agent handoff

Analytics and monitoring

Improved error handling

Webhook authentication

Production deployment

📊 Project Goal

This project demonstrates how n8n + RAG + an AI model + a web chatbot can be combined to create a documentation-aware support agent.

The key principle is:

Retrieve relevant documentation first, then generate the answer using that context.

This allows the support agent to work with project-specific information while keeping the workflow configurable and maintainable through n8n.

🤝 Contributing

Contributions are welcome.

Fork the repository.

Create a feature branch:

git checkout -b feature/new-feature

Make your changes.

Test the n8n workflow and frontend.

Commit your changes:

git commit -m "Add new support agent feature"

Push the branch:

git push origin feature/new-feature

Open a Pull Request.

📄 License

Add the project's chosen license here, for example:

MIT License

👨‍💻 Project

Support Agent with RAG Documentation

Built using:

n8n — workflow automation and orchestration

RAG — documentation retrieval

AI/LLM — response generation

Web Chatbot — user interface

Webhook — 
