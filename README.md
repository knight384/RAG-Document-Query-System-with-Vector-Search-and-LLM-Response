# RAG-Document-Query-System-with-Vector-Search-and-LLM-Response

📌 Overview

This project implements a Retrieval-Augmented Generation (RAG) system in n8n that allows:

📥 Document ingestion (PDF/Text via webhook)

✂️ Automatic document chunking

🧠 Embedding generation using OpenAI

🗄️ In-memory vector storage

🔎 Semantic search (Top-K retrieval)

🤖 Context-aware LLM responses

📦 Structured JSON output with confidence scoring

The system ensures the LLM answers only from provided document context, reducing hallucinations.

🏗 Architecture

The workflow supports two modes:

1️⃣ Document Ingestion Mode

If no query is provided in the webhook body:

Loads document

Splits into chunks (500 tokens, 100 overlap)

Generates embeddings

Stores them in vector memory

Returns success response

2️⃣ Query Mode

If query exists in request body:

Generates query embedding

Retrieves top 5 relevant chunks

Sends context to GPT model

Returns structured answer JSON

🔄 Workflow Nodes Breakdown
1️⃣ Webhook Trigger

Node: Receive Query or Document

Method: POST

Path: /rag-query

Determines whether request is ingestion or query.

2️⃣ Conditional Routing

Node: Route: Ingest or Query

If body.query exists → Query flow

Else → Ingestion flow

📥 Ingestion Pipeline
• Load Documents

Uses binary document loader.

• Split into Chunks

Chunk Size: 500 tokens

Overlap: 100 tokens

• Generate Embeddings

Uses OpenAI embeddings.

• Store Embeddings

Stored in:

In-Memory Vector Store
Memory Key: vector_store_key
• Response

Returns:

{
  "status": "success",
  "message": "Documents ingested and embeddings stored successfully"
}
🔎 Query Pipeline
• Query Embeddings

Embeds incoming question.

• Retrieve Context

Vector search

Top K = 5

Uses same memory key

• LLM Agent

Model: gpt-4o-mini
Temperature: 0.2

System Prompt Restriction:

Answer ONLY from provided context. If insufficient, say so explicitly.

📦 Structured Output

The system enforces JSON output schema:

{
  "answer": "string",
  "sources": ["string"],
  "confidence": "high | medium | low"
}

This guarantees:

Deterministic response format

Clear source attribution

Confidence scoring

🛠 Technical Stack

Automation Platform: n8n

LLM: OpenAI GPT-4o-mini

Embeddings: OpenAI

Vector Store: In-memory (LangChain)

Architecture Pattern: RAG (Retrieval-Augmented Generation)

🚀 API Usage
🔹 Ingest Documents

POST /rag-query

Attach document as binary data.

Response:

{
  "status": "success",
  "message": "Documents ingested and embeddings stored successfully"
}
🔹 Query Documents

POST /rag-query

{
  "query": "What is the main topic of the document?"
}

Response:

{
  "answer": "The document explains...",
  "sources": ["chunk_3", "chunk_7"],
  "confidence": "high"
}
🔐 Anti-Hallucination Strategy

Strict system prompt instructions

Low temperature (0.2)

Context-only answering

Structured output parser

Limited topK retrieval

⚠ Limitations

Uses in-memory vector store (data lost on restart)

Not persistent across sessions

Not optimized for very large document sets

No authentication on webhook (add in production)

🔮 Recommended Improvements

For production use:

Replace in-memory vector store with:

Pinecone

Weaviate

Supabase Vector

PostgreSQL + pgvector

Add authentication to webhook

Add logging node

Add persistent database

Add rate limiting

Add document metadata tracking
