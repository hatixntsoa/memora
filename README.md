# Memora

**An intelligent assistant that answers from your organization's own knowledge.** Memora ingests internal documents, indexes them, and lets teams ask questions in natural language, with answers grounded in sources.

---

## Core purpose

Knowledge lives scattered across PDFs, Word files, wikis and notes. People waste time searching or asking colleagues. Memora uses **Retrieval-Augmented Generation (RAG)** so an AI assistant answers using *your* documents rather than guessing, and always shows where the answer came from.

## Key features

### Document ingestion
- Upload PDF, DOCX and text files
- Automatic text extraction, cleaning and chunking
- Source management: add, update, remove documents
- Metadata per document (title, source, date, tags)

### Retrieval (RAG)
- Semantic search using vector embeddings
- Top-k retrieval of the most relevant passages
- Optional filtering by source or tag

### Conversational assistant
- Chat interface with streaming answers
- **Citations**: each answer links to the passages it used
- Conversation memory within a session
- Clear fallback when the knowledge base has no answer, instead of inventing one

### Agents and tools
- Agent loop that can decide to search, read a document or combine results
- Extensible tool set in `src/lib/agents/tools.ts`
- Prompt templates kept in one place for easy tuning

## How it works

```
INGESTION
Document -> loader -> chunking -> embeddings -> vector store

QUERY
Question -> embedding -> retrieval (top-k) -> prompt with context -> LLM -> answer + citations
```

- `src/lib/loaders/` reads PDF, DOCX and text.
- `src/lib/rag/chunking.ts` splits content into overlapping chunks.
- `src/lib/rag/embeddings.ts` converts chunks and queries to vectors.
- `src/lib/rag/retriever.ts` finds the closest chunks.
- `src/lib/rag/pipeline.ts` orchestrates the full flow.
- `src/lib/agents/` holds the agent logic, tools and prompts.
- `src/lib/vectorstore/` abstracts the vector database.

## Tech stack

| Layer | Choice |
|---|---|
| Framework | Next.js (App Router) + TypeScript |
| Styling | Tailwind CSS |
| LLM | Provider-agnostic client in `src/lib/llm/client.ts` |
| Vector store | pgvector, Qdrant or Chroma (swappable) |
| Files | Local storage in `data/uploads` |

## Project structure

```
src/
  app/
    chat/         Chat interface
    documents/    Document library
    sources/      Source management
    settings/     Model and retrieval settings
    api/          chat, ingest, documents, search
  components/     ui, chat, documents
  lib/
    rag/          chunking, embeddings, retriever, pipeline
    agents/       agent, tools, prompts
    llm/          client
    vectorstore/  index
    loaders/      pdf, docx, text
  hooks/ types/ services/ utils/
data/uploads/     Uploaded source files
```

## API overview

| Route | Purpose |
|---|---|
| `POST /api/ingest` | Upload and index a document |
| `GET/DELETE /api/documents` | List and remove documents |
| `POST /api/search` | Semantic search over the knowledge base |
| `POST /api/chat` | Ask a question and get a grounded answer |

## Getting started

```bash
cp .env.example .env.local
# fill in your LLM and vector store settings

npm install
npm run dev
```

Open http://localhost:3000, upload a few documents in **Documents**, then ask questions in **Chat**.

## Environment variables

```
LLM_API_KEY=
LLM_MODEL=
EMBEDDING_MODEL=
VECTOR_STORE_URL=
UPLOAD_DIR=./data/uploads
```

## Quality and safety principles

- **Grounded answers only**: if the sources don't contain the answer, Memora says so.
- **Always cite**: every claim points back to a document passage.
- **Access control ready**: documents can be scoped per team or user.
- **Private by design**: your documents are never used to train models.

## Tuning tips

- Chunk size and overlap strongly affect answer quality; adjust them in `chunking.ts`.
- Increase top-k for broad questions, lower it for precise ones.
- Keep prompts in `prompts.ts` so they can be versioned and tested.

## Roadmap

- [ ] Connectors (Google Drive, Notion, SharePoint, Confluence)
- [ ] Per-team access permissions
- [ ] Reranking for better retrieval
- [ ] Answer feedback (helpful / not helpful)
- [ ] Evaluation set to measure answer quality

## License

Private project. All rights reserved.
