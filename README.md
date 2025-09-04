# BlogAgentic — LangGraph Blog Generator API

A FastAPI service that uses **LangGraph** + **LangChain** and **Groq LLMs** to generate structured blog posts from a topic and optionally translate them into multiple languages using a routing graph. Designed for programmatic use and easy deployment.

---

## ✨ Features
- **Topic → Blog**: Generate a blog (title + content) from a prompt topic.
- **Multilingual**: Route to language-specific translation (e.g., Hindi, French) via graph edges.
- **Graph-first design**: `StateGraph` with nodes for blog creation and translation.
- **JSON I/O**: Clean REST endpoints for automation and integration.
- **LangSmith-ready**: Environment hooks for tracing/observability.

---

## 🧱 Architecture
- **Entry point**: `app.py` (FastAPI server)
- **Graph**: `src/graphs/graph_builder.py` (builds topic or language graphs)
- **Node logic**: `src/nodes/blog_node.py` (generate + translate)
- **State**: `src/states/blogstate.py` (typed state; `Blog` Pydantic model)
- **LLM**: `src/llms/groqllm.py` (ChatGroq using `GROQ_API_KEY`)
- **Config**: `langgraph.json` and `.env` for local studio/dev tooling

---

## 📂 Project Layout
```
BlogAgentic/
├── app.py
├── requirements.txt
├── pyproject.toml
├── langgraph.json
├── request.json
├── .env                 # API keys (do NOT commit real keys)
└── src/
    ├── graphs/graph_builder.py
    ├── nodes/blog_node.py
    ├── states/blogstate.py
    └── llms/groqllm.py
```

---

## 🔐 Environment
Set via `.env` or shell:
```bash
export GROQ_API_KEY="your_groq_api_key"
# Optional LangSmith/Chain telemetry
export LANGCHAIN_API_KEY="your_langsmith_key"
export LANGCHAIN_PROJECT="BlogAgentic"
```
> ⚠️ Rotate any real keys found in `.env` before publishing.

---

## ▶️ Run Locally
Create a venv and install:
```bash
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```
Start the API:
```bash
uvicorn app:app --host 0.0.0.0 --port 8000 --reload
```
Open http://localhost:8000/docs for Swagger UI.

---

## 🧪 API Endpoints
### `POST /generate_blog`
**Body:**
```json
{ "topic": "Agentic AI" }
```
**Response:**
```json
{ "data": { "blog": { "title": "...", "content": "..." } } }
```

### `POST /generate_blog_language`
**Body:**
```json
{ "topic": "Agentic AI", "language": "french" }
```
**Response:** translated blog.

> See `request.json` for example payloads.

---

## 🧠 How It Works
- `GraphBuilder` wires a `StateGraph(BlogState)`.
- `BlogNode` calls the LLM to **generate** the blog and (optionally) **translate** it.
- The **router** directs to the right translation path based on `current_language`.

---

## 🛠 Troubleshooting
- *401/403*: Check `GROQ_API_KEY`.
- *Validation errors*: Ensure JSON body matches examples.
- *No output/graph errors*: Verify LangGraph/LangChain versions from `requirements.txt`.
- *Security*: Never commit real API keys. Use `.env.example` for public repos.

---

## 📄 License
MIT (update as needed).
