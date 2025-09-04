# BlogAgentic — LangGraph Blog Generator API

A FastAPI service that uses **LangGraph** + **LangChain** and **Groq LLMs** to generate structured blog posts from a topic and optionally translate them into multiple languages using a routing graph. Built for programmatic use, with **LangGraph CLI server** for local graph serving, **LangGraph Studio** for interactive dev, and **LangSmith** for tracing/experiments.

---

## ✨ Features
- **Topic → Blog**: Generate a blog (title + content) from a topic.
- **Multilingual**: Translate via graph routing (e.g., Hindi, French).
- **Graph‑first**: `StateGraph` with modular nodes (generate, translate, route).
- **FastAPI REST**: Clean JSON I/O (`/generate_blog`, `/generate_blog_language`).
- **LangGraph CLI Server**: Serve compiled graphs locally for testing/integration.
- **LangGraph Studio**: Visualize, step through, and iterate on graph runs.
- **LangSmith Integration**: Tracing, logs, metrics, and experiment tracking.

---

## 🧱 Architecture
- **Entry point**: `app.py` (FastAPI)
- **Graph**: `src/graphs/graph_builder.py` (topic & language graphs)
- **Node logic**: `src/nodes/blog_node.py` (generate & translate)
- **State**: `src/states/blogstate.py` (typed state + Pydantic model `Blog`)
- **LLM**: `src/llms/groqllm.py` (ChatGroq via `GROQ_API_KEY`)
- **CLI/Studio config**: `langgraph.json`
- **Env**: `.env` for keys (never commit real keys)

---

## 📂 Project Layout
```
BlogAgentic/
├── app.py
├── requirements.txt
├── pyproject.toml
├── langgraph.json
├── request.json
├── .env
└── src/
    ├── graphs/graph_builder.py
    ├── nodes/blog_node.py
    ├── states/blogstate.py
    └── llms/groqllm.py
```

---

## 🔐 Environment
Set via `.env` or your shell:
```bash
export GROQ_API_KEY="your_groq_api_key"
# Optional LangSmith/Studio telemetry
export LANGCHAIN_API_KEY="your_langsmith_key"
export LANGCHAIN_PROJECT="BlogAgentic"
```
> 🔒 **Security**: Rotate any real keys in `.env` before sharing.

---

## ▶️ Run the FastAPI Server
Create a venv and install deps:
```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```
Start:
```bash
uvicorn app:app --host 0.0.0.0 --port 8000 --reload
```
Open **Swagger UI**: http://localhost:8000/docs

---

## 🧪 REST Endpoints
### `POST /generate_blog`
**Body:**
```json
{ "topic": "Agentic AI" }
```
**Response (shape):**
```json
{ "data": { "blog": { "title": "...", "content": "..." } } }
```

### `POST /generate_blog_language`
**Body:**
```json
{ "topic": "Agentic AI", "language": "french" }
```
**Response:** translated blog.

See **request.json** for examples.

---

## 🧰 LangGraph CLI — Local Graph Server
This repo includes **langgraph.json** so you can serve the compiled graph without FastAPI:

1) Install CLI:
```bash
pip install "langchain-cli[inmem]"
```
2) Ensure env is set (e.g., `GROQ_API_KEY`, optional `LANGCHAIN_API_KEY`).
3) From the project root, run:
```bash
langgraph server serve --config ./langgraph.json --env ./.env
```
The CLI will expose endpoints for your graphs (e.g., `blogs:graph`). Check the terminal output for the local base URL and paths (often `/graphs/<name>/invoke`).

**cURL example** (graph that generates/translate blog):
```bash
curl -X POST "http://localhost:8123/graphs/blogs/graph/invoke"   -H "Content-Type: application/json"   -d '{"topic":"Agentic AI","current_language":"french"}'
```

> Tip: Use `langgraph server call --graph blogs/graph --input @request.json` to send inputs from a file.

---

## 🧪 LangGraph Studio — Visual Dev & Debugging
Run the Studio to **inspect**, **step through**, and **visualize** state transitions:
```bash
langgraph studio --config ./langgraph.json --env ./.env
```
- See live node traces, inputs/outputs, and execution flow.
- Tweak prompts/nodes and re-run quickly.
- Great for validating routing and translation behavior before deploying.

---

## 📈 LangSmith — Tracing & Experiments
Enable tracing for both FastAPI and CLI/Studio runs by setting:
```bash
export LANGCHAIN_API_KEY="your_langsmith_key"
export LANGCHAIN_PROJECT="BlogAgentic"
```
Then explore runs, prompt versions, and latency metrics in **LangSmith** to compare model variants or prompt edits.

---

## 🛠 Troubleshooting
- **401/403**: Check `GROQ_API_KEY`.
- **Graph errors**: Verify versions match `requirements.txt`; re‑install inside a clean venv.
- **CLI not found**: `pip install "langchain-cli[inmem]"`.
- **Studio/CLI env**: Pass `--env ./.env` or export in your shell.

---

## 📄 License
MIT
