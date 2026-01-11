# HTTP Web Search Briefing Bot

## Setup
```bash
pip install fastapi uvicorn requests
export MCP_HTTP_TOKEN=devtoken123
export TAVILY_API_KEY=YOUR_KEY
ollama run llama3

Run server

```bash
uvicorn server:app --reload

Run client
```bash
python client.py "Your topic here"

---

# 4️⃣ `curl_examples.md`

```md
## List tools
curl -H "Authorization: Bearer devtoken123" http://127.0.0.1:8000/tools

## Search
curl -X POST http://127.0.0.1:8000/tools/search_web \
 -H "Authorization: Bearer devtoken123" \
 -d '{"query":"AI agents","k":5}'
