HTTP Web Search Briefing Bot

A minimal HTTP-based tool server and CLI client that performs web search, fetches readable web pages, summarizes them using a local LLM, and saves a Markdown briefing with citations.

This project is designed as a Daily Challenge to practice:

clean HTTP API design

client/server separation

tool-style orchestration

reproducible local AI workflows

✨ Features

HTTP-only tool server with Bearer authentication

Free web search API (configurable)

Local LLM summarization over HTTP (Ollama or LM Studio)

Inline citations with numbered sources

Tiny CLI client (brief) that runs the full flow end-to-end

Deterministic, reproducible setup

🧱 Architecture
CLI (brief)
   |
   | HTTP
   v
Tool Server (/tools/*)
   |
   |--> Web Search API (free tier)
   |
   |--> Local LLM (Ollama or LM Studio over HTTP)
   |
   |--> Disk (Markdown output)

📦 Project Structure
.
├── server.py          # FastAPI HTTP tool server
├── client.py          # CLI client (brief)
├── requirements.txt   # Pinned dependencies
├── README.md
└── samples/
    └── brief_YYYY-MM-DD.md

🔐 Authentication

All endpoints require:

Authorization: Bearer <MCP_HTTP_TOKEN>


Set it via environment variable:

export MCP_HTTP_TOKEN=dev-token

🚀 Setup
1️⃣ Prerequisites

Python 3.10+

A free web search API key (Tavily / Google CSE / Brave / Serper)

A local LLM running over HTTP:

Ollama (recommended)

or LM Studio (OpenAI-compatible mode)

2️⃣ Install dependencies
pip install -r requirements.txt


requirements.txt

fastapi==0.110.0
uvicorn==0.29.0
requests==2.31.0
python-dotenv==1.0.1
readability-lxml==0.8.1
beautifulsoup4==4.12.3

3️⃣ Run a local LLM
Option A: Ollama
ollama run llama3


API will be available at:

http://localhost:11434/api/chat

Option B: LM Studio

Open Developer → Local Server

Start server

Example base URL:

http://localhost:1234/v1

4️⃣ Run the server
uvicorn server:app --port 8000

🧰 Tool API
GET /tools

List available tools and input schemas.

curl -H "Authorization: Bearer dev-token" \
http://localhost:8000/tools

POST /tools/search_web

Search the web.

curl -X POST http://localhost:8000/tools/search_web \
-H "Authorization: Bearer dev-token" \
-H "Content-Type: application/json" \
-d '{
  "query": "AI agents tool calling",
  "k": 5
}'


Response

[
  {
    "title": "Tool Calling in LLM Agents",
    "url": "https://example.com",
    "snippet": "Agents increasingly rely on tools...",
    "source": "web"
  }
]

POST /tools/fetch_readable

Fetch main readable content from a URL.

curl -X POST http://localhost:8000/tools/fetch_readable \
-H "Authorization: Bearer dev-token" \
-H "Content-Type: application/json" \
-d '{
  "url": "https://example.com/article"
}'


Response

{
  "url": "https://example.com/article",
  "title": "Example Article",
  "text": "Main readable article text..."
}

POST /tools/summarize_with_citations

Summarize documents using a local LLM.

curl -X POST http://localhost:8000/tools/summarize_with_citations \
-H "Authorization: Bearer dev-token" \
-H "Content-Type: application/json" \
-d '{
  "topic": "AI agents",
  "docs": [
    {
      "title": "Example",
      "url": "https://example.com",
      "text": "Long article text..."
    }
  ]
}'


Response

{
  "bullets": [
    "AI agents increasingly use tool-calling for planning [1]",
    "Local LLMs enable private agent workflows [1]",
    "Deterministic graphs improve reliability [1]",
    "Grounded retrieval reduces hallucinations [1]",
    "Automation tools speed agent integration [1]"
  ],
  "sources": [
    { "i": 1, "title": "Example", "url": "https://example.com" }
  ]
}

POST /tools/save_markdown

Save the briefing to disk.

curl -X POST http://localhost:8000/tools/save_markdown \
-H "Authorization: Bearer dev-token" \
-H "Content-Type: application/json" \
-d '{
  "filename": "brief_2025-11-19.md",
  "content": "# Brief\n\n- Example bullet [1]\n\n## Sources\n[1] Example — https://example.com"
}'


Response

{ "path": "samples/brief_2025-11-19.md" }

🖥️ CLI Client
Usage
python client.py brief "AI agents and tool calling"

What it does

/tools/search_web

/tools/fetch_readable (top 3 results)

/tools/summarize_with_citations

/tools/save_markdown

📄 Sample Output

samples/brief_YYYY-MM-DD.md

# Brief

- AI agents increasingly use tool-calling for planning and execution [1]
- Local LLMs enable private, low-cost agent workflows [2]
- Frameworks like LangGraph add deterministic control [3]
- Retrieval grounding reduces hallucinations [1]
- Visual orchestrators like n8n speed integrations [3]

## Sources
[1] Example — https://example.com
[2] Example — https://example.com
[3] Example — https://example.com

🛠 Troubleshooting

401 Unauthorized → check Authorization header and MCP_HTTP_TOKEN

Search API errors → verify key and quota

LLM JSON issues → reduce temperature or retry with stricter prompt

Empty readable text → try another domain or increase search k

📚 References

Ollama API — https://ollama.com

LM Studio API — https://lmstudio.ai/docs

Tavily Search — https://docs.tavily.com

Google Programmable Search — https://developers.google.com/custom-search
