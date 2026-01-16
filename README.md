# Retrieval-Augmented-Generation-API
Production-ready RAG API built with FastAPI, ChromaDB, and Ollama. It performs semantic search over embedded documents, retrieves relevant context, and generates grounded answers using a local LLM. Includes Swagger docs, REST endpoints, and a modular backend design suitable for real-world AI services.

Features

⚡ FastAPI backend with automatic Swagger documentation

🧠 Local LLM inference using Ollama (e.g., TinyLlama)

🔎 Vector search using ChromaDB

📚 Document-based question answering (RAG)

🔁 Hot reload for fast development

🧪 Tested with curl and Swagger UI

🏗️ Production-style project structure

🛠 Tech Stack

Python 3.12+

FastAPI – Web framework

Uvicorn – ASGI server

ChromaDB – Vector database

Ollama – Local LLM runner

TinyLlama (or similar model)

📦 Installation
1️⃣ Clone the repo
git clone https://github.com/yourusername/nextwork-rag-api.git
cd nextwork-rag-api
2️⃣ Create a virtual environment
py -m venv venv
3️⃣ Activate the environment

Windows:

.\venv\Scripts\Activate.ps1
4️⃣ Install dependencies
python -m pip install --upgrade pip
pip install fastapi uvicorn chromadb requests
🧠 Install and Run Ollama

Download Ollama: https://ollama.com

Pull a model:

ollama pull tinyllama

Start Ollama (it runs on port 11434 by default)

▶️ Running the API
python -m uvicorn app:app --reload

Once running, visit:

Swagger UI: http://127.0.0.1:8000/docs

Redoc: http://127.0.0.1:8000/redoc

📡 API Endpoints
🔹 Health Check
GET /health
🔹 Add Documents
POST /add
🔹 Query the RAG System
POST /query
🧪 Example Usage
Using curl
curl -X POST http://127.0.0.1:8000/query \
  -H "Content-Type: application/json" \
  -d '{"question": "What is RAG?"}'
📁 Project Structure
nextwork-rag-api/
│
├── app.py
├── venv/
├── requirements.txt
└── README.md
📈 What I Learned

How RAG systems work end-to-end

How to build APIs using FastAPI

How vector databases enable semantic search

How to integrate local LLMs

How real-world AI backends are structured

🚀 Future Improvements

PDF & file uploads

JWT authentication

Streaming responses

Frontend UI

Cloud deployment

Multi-model support
