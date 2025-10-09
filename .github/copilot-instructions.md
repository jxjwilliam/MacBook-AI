<!-- .github/copilot-instructions.md - guidance for AI coding agents in this workspace -->
# Copilot / AI agent quick guide for this repository

This repository collects notes, examples and local tooling for LLM, RAG, and multi-agent experiments. Keep instructions short and actionable. When in doubt, reference the `LLM/` docs and `mcp/` metadata.

Key places to read first
- `LLM/` — primary conceptual docs (LangChain, RAG, multi-agents). Use these for architecture and data-flow patterns.
- `mcp/` — MCP metadata and shortcuts (contains `copilot.json`, `cursor.json`) that show how local MCP servers/tools are run.
- `TodoList.md` — high-level project tasks, quick tips (contains notes about favicons, ffmpeg, etc.).

Big-picture architecture notes
- This workspace is documentation-first. There are many Markdown guides that describe typical LLM pipelines: ingestion -> splitter -> embeddings -> vector store -> retriever -> LLM generation (see `LLM/rag.md` and `LLM/langchain.md`).
- Common components to assume: Document loaders (PDF/CSV/HTML), text splitters (RecursiveCharacterTextSplitter), embeddings (OpenAI/HuggingFace), vector stores (FAISS/Chroma/Elastic), and LangChain-style chains/agents.

Project-specific conventions
- Docs are authoritative: prefer updating `LLM/*.md` when behaviour changes rather than scattering implementation notes elsewhere.
- Examples and snippets in `LLM/langchain.md` are Python-first (LangChain). If you add code, follow Python idioms used there (PromptTemplate, LLMChain, Retriever patterns).
- `mcp/copilot.json` shows MCP servers (Stripe/vercel) and environment keys; do NOT hardcode secrets — follow the pattern of placeholders.

Developer workflows (what bots should suggest)
- How to run or test: there is no single package manager or top-level build. For Python examples, assume a venv and `pip install -r requirements.txt` in the `mp-docs/img-docs/` folder when relevant.
- Deployment hints: frontends can be deployed to Vercel; Python backends (FastAPI examples mentioned in docs) can be deployed to Render or fly.io — reference in `LLM/*` where these are suggested.

Concrete, discoverable examples to reference
- RAG pipeline: follow `LLM/rag.md` — loader -> splitter -> embeddings -> FAISS/Chroma -> retriever -> LLM. Cite code snippets from `LLM/langchain.md` (PromptTemplate, LLMChain).
- Multi-agent orchestration: `LLM/multi-agents.md` describes orchestrator-led patterns (WebSurfer, FileSurfer, Coder). Use AutoGen/Autogen-like patterns when suggesting multi-agent code.

What to avoid or flag
- Don't invent runtime scripts or package manifests. If recommending commands, verify the file (e.g., `requirements.txt`) exists in the target folder first.
- Secrets: never insert real secrets; use placeholders like `STRIPE_SECRET_KEY` (see `mcp/copilot.json`).

If you change docs or add examples
- Update the corresponding `LLM/*.md` file and add a short example. Keep examples self-contained and copy-pastable (small functions or one-file demos).

When asking the user for clarification
- Use file references (path + short excerpt) and ask targeted questions: "Do you want a runnable Python RAG example using FAISS or Chroma?" rather than generic clarifications.

Further reading and follow-ups
- Start with `LLM/langchain.md`, `LLM/rag.md`, `LLM/multi-agents.md`, then `mcp/copilot.json`.

— End of file
