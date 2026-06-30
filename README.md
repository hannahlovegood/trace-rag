# 溯源 · TraceRAG

> A single-file, zero-backend document Q&A tool that traces every answer back to its source. Upload a PDF, ask a question, and get answers with clickable citations to the exact passages they came from.

**Live demo:** https://hannahlovegood.github.io/trace-rag/

A lightweight, from-scratch reimplementation of the core RAG (Retrieval-Augmented Generation) experience found in tools like [Open WebUI](https://github.com/open-webui/open-webui) — distilled into **one self-contained HTML file** with no build step and no server.

## Features

- **Runs entirely in the browser.** PDF parsing (pdf.js), chunking, and keyword retrieval all happen client-side, so it deploys as a plain static page.
- **Two tiers, graceful by design:**
  - *No API key* → **BM25 keyword retrieval** (handles both Chinese and English, with stop-word filtering). Visitors can try it instantly.
  - *With an API key* → **semantic vector retrieval** + an LLM that generates a natural-language answer with `[1] [2]` inline citations.
- **Every answer is traceable.** Retrieved passages are shown as numbered cards with page number and relevance score; click a citation to jump straight to the source text.
- **Bring your own key (BYOK).** Works with any OpenAI-compatible API (DashScope / Qwen, OpenAI, or a custom endpoint). Your key is stored only in your browser and sent directly to the provider — never to any intermediary server.

## How it works

The RAG pipeline, in four steps:

1. **Parse & chunk** — `pdf.js` extracts text in the browser; it is split into ~600-character chunks (with overlap), each tagged by page number.
2. **Index** — with a key, every chunk is embedded into a vector; without one, a BM25 index is built (CJK bigrams + Latin tokens).
3. **Retrieve top-K** — the question is matched against chunks by cosine similarity (or BM25) to surface the most relevant passages — the "sources."
4. **Generate with citations** — the sources are fed to the model, which is told to answer *only* from them and cite `[1] [2]`. If the answer isn't in the document, it says so instead of guessing.

## Usage

Open the live demo (or `index.html` via a local server), drag in a PDF, and ask away — the no-key demo mode works immediately. To enable model-generated answers, click **设置 / API Key**, pick a provider, paste a key, and hit **测试连接**.

> Tip: when testing with a key locally, serve the file (e.g. `python3 -m http.server`) rather than opening it as `file://` — some providers reject the `null` origin via CORS.

## Tech

Vanilla JS · [pdf.js](https://mozilla.github.io/pdf.js/) · BM25 · cosine similarity · OpenAI-compatible chat & embeddings APIs. No framework, no build, one file.

## License

MIT
