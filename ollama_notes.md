<div align="center">

# Local LLM Development with Ollama & DeepSeek R1

### A Complete Knowledge Base for Building Private, Powerful AI Applications

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Ollama](https://img.shields.io/badge/Ollama-000000?style=for-the-badge&logo=ollama&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-1C3C3C?style=for-the-badge&logo=langchain&logoColor=white)
![ChromaDB](https://img.shields.io/badge/ChromaDB-FF6B6B?style=for-the-badge)
![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white)
![DeepSeek](https://img.shields.io/badge/DeepSeek_R1-4A90D9?style=for-the-badge)
![ElevenLabs](https://img.shields.io/badge/ElevenLabs-000000?style=for-the-badge)

> **Everything you need to know to build local LLM applications — from zero to production RAG systems, voice assistants, and agentic tools.**

</div>

---

## Table of Contents

- [What is Ollama?](#what-is-ollama)
- [Why Run LLMs Locally?](#why-run-llms-locally)
- [Understanding Model Parameters](#understanding-model-parameters)
- [Ollama Setup & Commands](#ollama-setup--commands)
- [The Ollama REST API](#the-ollama-rest-api)
- [Using Ollama with Python](#using-ollama-with-python)
- [Custom Model Creation with Modelfiles](#custom-model-creation-with-modelfiles)
- [RAG Systems — The Full Picture](#rag-systems--the-full-picture)
- [Vector Stores & Embeddings Deep Dive](#vector-stores--embeddings-deep-dive)
- [Building PDF RAG with LangChain](#building-pdf-rag-with-langchain)
- [Function Calling (Tool Use)](#function-calling-tool-use)
- [Voice RAG System](#voice-rag-system)
- [DeepSeek R1 — Reasoning Models](#deepseek-r1--reasoning-models)
- [Model Variants Explained](#model-variants-explained)
- [Open Source vs Closed Source LLMs](#open-source-vs-closed-source-llms)
- [Applications You Can Build](#applications-you-can-build)
- [Full Tech Stack Reference](#full-tech-stack-reference)

---

## What is Ollama?

**Ollama** is an open-source tool that dramatically simplifies the process of running large language models (LLMs) locally on your machine.

Instead of dealing with complex cloud APIs, authentication, rate limits, or data privacy concerns — Ollama handles everything in the background through a clean CLI interface and a local REST API.

```
                         ┌─────────────────────────────┐
                         │           OLLAMA            │
                         │                             │
   ┌──────────┐          │  ┌─────────┐  ┌──────────┐  │            ┌─────────────┐
   │   User   │◀───────▶│ │   CLI   │  │ REST API │   │◀────────▶│  Your App   │
   └──────────┘          │  └─────────┘  └──────────┘  │            └─────────────┘
                         │         │          │        │
                         │         ▼          ▼        │
                         │   ┌─────────────────────┐   │
                         │   │   Model Runner      │   │
                         │   │  (GPU/CPU managed)  │   │
                         │   └──────────┬──────────┘   │
                         │              │              │
                         └──────────────│──────────────┘
                                        │
                         ┌──────────────▼───────────────┐
                         │         LOCAL MODELS         │
                         │  llama3.2 | llava | mistral  │
                         │  codellama | deepseek-r1 ... │
                         └──────────────────────────────┘
```

**What Ollama gives you:**
- A unified interface to download, run, and manage LLM models
- A local API server (runs on `localhost:11434`) compatible with REST and the OpenAI SDK
- Model management — pull, list, delete, and create custom models
- Performance optimization — handles hardware acceleration automatically
- Extensibility — plug into Python, LangChain, LM Studio, Msty, and more

**Core Idea:** With Ollama, you can run models like `llama3.2`, `deepseek-r1`, `llava`, `mistral`, and `codellama` on your own hardware — privately, for free, with no internet required after setup.

---

## Why Run LLMs Locally?

Running models on the cloud is convenient, but comes with real trade-offs. Here's the full comparison:

| Problem with Cloud APIs | How Local LLMs Solve It |
|------------------------|------------------------|
| **Privacy** — Prompts and documents leave your machine | Runs entirely on-device — zero data exfiltration |
| **Cost** — API calls add up fast at scale | Free after initial hardware investment |
| **Latency** — Round-trip network delay on every request | Sub-second inference, no network dependency |
| **Setup friction** — Auth, billing, quotas, rate limits | One Ollama install and you're running |
| **Customization** — Limited control over model behavior | Full Modelfile control over every parameter |
| **Vendor Lock-in** — Tied to one provider's pricing | Open-source models, fully portable |

```
  Cloud LLM Flow:                        Local LLM Flow:
  ─────────────────────────────────      ───────────────────────────
  Your App                               Your App
    │                                      │
    │  (prompt travels over internet)       │  (stays on your machine)
    ▼                                      ▼
  OpenAI / Anthropic Servers            Ollama (localhost:11434)
    │  (your data is on their servers)     │
    │                                      ▼
    ▼                                    Local Model
  Response back over internet           Response instantly
    │                                      │
    ▼                                      ▼
  Your App                               Your App
  [slow] [costly] [data exposed]         [fast] [free] [private]
```

---

## Understanding Model Parameters

Before you can pick the right model, you need to understand what these numbers actually mean.

### Parameters (e.g., 3.2B)

Parameters are the internal **weights and biases** that a neural network learns during training. They determine how the model processes inputs and generates outputs.

```
  Input Text ──▶ [Layer 1] ──▶ [Layer 2] ──▶ ... ──▶ [Layer N] ──▶ Output Text
                    ↑              ↑                       ↑
              (weights &      (weights &             (weights &
               biases)         biases)                biases)
                    └──────────────┴───────────────────────┘
                           These are the "parameters"
                       3.2B model = 3.2 billion of these numbers
```

> **Rule of thumb:** More parameters = smarter but slower and more RAM-hungry. Fewer = faster but less capable.

### Context Length

The **context length** is the max number of tokens (roughly words) the model can handle in one go — both input and output combined.

```
  ┌──────────────────────────────────────────────────────────┐
  │                  CONTEXT WINDOW                          │
  │                                                          │
  │  [System Prompt][Conversation History][Current Input]    │
  │                                                          │
  │  Short Context (2k):  [ ██████████████░░░░░░░░░░░░░░ ]   │
  │                         Fits short chats                 │
  │                                                          │
  │  Long Context (128k): [ ████████████████████████████ ]   │
  │                         Fits entire books                │
  └──────────────────────────────────────────────────────────┘
```

### Embedding Length

The **embedding length** is the size of the vector representation for each token (e.g., 3072 dimensions).

```
  Word "ocean" ──▶ Embedding Model ──▶ [0.23, -0.45, 0.12, 0.87, ...]
                                        ◀────── 3072 numbers ─────────▶
                                        Higher dimensions = more nuanced meaning captured
```

### Quantization

**Quantization** reduces model size by lowering the precision of its weights — trading a small accuracy drop for huge storage and speed gains.

```
  ┌──────────────────────────────────────────────────────────────┐
  │                    QUANTIZATION TRADE-OFFS                   │
  │                                                              │
  │  Full Precision (F16):  ████████████████  Size: 14 GB        │
  │                         Accuracy: ████████████ (100%)        │
  │                                                              │
  │  8-bit (Q8):            ████████          Size: 7 GB         │
  │                         Accuracy: ████████████ (99%)         │
  │                                                              │
  │  4-bit (Q4):            ████              Size: 4 GB         │
  │                         Accuracy: ███████████  (95%)         │
  │                                                              │
  │  2-bit (Q2):            ██               Size: 2 GB          │
  │                         Accuracy: ████████     (80%)         │
  └──────────────────────────────────────────────────────────────┘
  Q4 is the sweet spot: 4x smaller, barely noticeable accuracy drop
```

---

## Ollama Setup & Commands

### System Requirements

| Component | Minimum |
|-----------|---------|
| OS | macOS, Linux, or Windows |
| Storage | 10 GB free (models range from 1–20+ GB) |
| RAM | 8 GB+ recommended |
| Processor | Modern CPU (GPU optional but speeds things up significantly) |

### Installation

1. Download Ollama from [ollama.com](https://ollama.com)
2. Run the installer
3. Ollama starts a local server automatically on `http://localhost:11434`

### Essential CLI Commands

```bash
# Model management
ollama list                          # Show all downloaded models
ollama pull llama3.2                 # Download a model
ollama run llama3.2                  # Download (if needed) and start interactive chat
ollama rm llama3.2                   # Delete a model

# Custom models
ollama create james -f ./Modelfile   # Create a custom model from a Modelfile

# Help
ollama help                          # Show all available commands
```

### In-Chat Commands (while in `ollama run`)

```
/set              Set session variables
/show             Show current model information
/load <model>     Switch to a different model
/save <model>     Save the current session
/clear            Clear session context (start fresh conversation)
/bye              Exit the chat
/?, /help         Help menu
"""               Begin a multi-line message
```

### Model Selection Tips

When browsing models on the Ollama models page:
- **Featured / Most Popular / Newest** — useful filters
- **Parameters** — higher = smarter but heavier
- **Pull count** — signals community trust and stability
- **Sizes** — each model page lists variants (1.5B, 3B, 7B, 13B, etc.)
- **README tab** — always read this; explains what the model is optimized for

**Models for different tasks:**

| Task | Recommended Model |
|------|-----------------|
| General text generation | `llama3.2`, `mistral` |
| Code generation & review | `codellama` |
| Image + text (multimodal) | `llava` |
| Math & reasoning problems | `deepseek-r1` |
| Text embeddings (for RAG) | `nomic-embed-text` |

---

## The Ollama REST API

Ollama runs a local HTTP server that you can call from any language or tool. By default it listens on `http://localhost:11434`.

```
  Your Code / curl / Postman
          │
          │  HTTP POST
          ▼
  ┌─────────────────────────┐
  │  localhost:11434        │
  │                         │
  │  /api/generate  ◀───────┼──── Single prompt → single response
  │  /api/chat      ◀───────┼──── Multi-turn conversation
  │  /api/embeddings◀───────┼──── Text → vector
  │  /api/tags      ◀───────┼──── List local models
  └─────────────────────────┘
```

### Generate Endpoint

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2",
  "prompt": "Why is the sky blue?",
  "stream": false
}'
```

### Chat Endpoint

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "llama3.2",
  "messages": [
    {"role": "user", "content": "Tell me a fun fact"}
  ],
  "stream": false
}'
```

### JSON Mode (Force structured output)

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2",
  "prompt": "What color is the sky at different times of day? Respond using JSON",
  "format": "json",
  "stream": false
}'
```

> **Full API reference:** [github.com/ollama/ollama/blob/main/docs/api.md](https://github.com/ollama/ollama/blob/main/docs/api.md)

---

## Using Ollama with Python

There are two ways to interact with Ollama in Python:

### Method 1: Via `requests` (raw HTTP)

Good for understanding the underlying API and maximum flexibility.

```python
import requests
import json

url = "http://localhost:11434/api/generate"
data = {
    "model": "llama3.2",
    "prompt": "Tell me a short funny story"
}

response = requests.post(url, json=data, stream=True)

if response.status_code == 200:
    for line in response.iter_lines():
        if line:
            result = json.loads(line.decode("utf-8"))
            print(result.get("response", ""), end="", flush=True)
```

### Method 2: Via the `ollama` Python Library

Cleaner, higher-level API. Install with `pip install ollama`.

```python
import ollama

# List all local models
response = ollama.list()

# Simple chat
res = ollama.chat(
    model="llama3.2",
    messages=[{"role": "user", "content": "Why is the sky blue?"}]
)
print(res["message"]["content"])

# Streaming chat
res = ollama.chat(
    model="llama3.2",
    messages=[{"role": "user", "content": "Why is the ocean salty?"}],
    stream=True
)
for chunk in res:
    print(chunk["message"]["content"], end="", flush=True)

# Generate (non-chat)
res = ollama.generate(model="llama3.2", prompt="Tell me about black holes")
print(res["response"])

# Show model details
print(ollama.show("llama3.2"))
```

---

## Custom Model Creation with Modelfiles

A **Modelfile** lets you create a reusable custom model with specific behavior baked in. It defines the base model, system prompt, and generation parameters.

```
  Modelfile defines:
  ┌────────────────────────────────────────────────────────┐
  │                                                        │
  │  FROM llama3.2              ◀──── Base model          │
  │                                                        │
  │  PARAMETER temperature 0.3  ◀──── How creative/random │
  │  PARAMETER top_p 0.9        ◀──── Nucleus sampling    │
  │  PARAMETER num_ctx 4096     ◀──── Context window size │
  │                                                        │
  │  SYSTEM """                 ◀──── Personality & rules │
  │  You are James, a smart                                │
  │  assistant who answers                                 │
  │  succinctly.                                           │
  │  """                                                   │
  └────────────────────────────────────────────────────────┘
```

**Temperature guide:**
```
  0.0 ──────────────────────────────────────── 1.0
  │                                              │
  Deterministic                           Very creative
  (same answer                           (different answer
   every time)                            each time)
  
  Use ~0.1–0.3 for:     Use ~0.7–1.0 for:
  - Factual Q&A          - Creative writing
  - Code generation      - Brainstorming
  - Data extraction      - Story generation
```

```bash
ollama create james -f ./Modelfile    # Build the model
ollama list                           # Confirm it appears
ollama run james                      # Chat with your custom model
```

**Creating custom models in Python:**

```python
import ollama

modelfile = """
FROM llama3.2
SYSTEM You are a very smart assistant who knows everything about oceans.
PARAMETER temperature 0.1
"""

ollama.create(model="ocean-expert", modelfile=modelfile)
res = ollama.generate(model="ocean-expert", prompt="Why is the ocean salty?")
print(res["response"])
ollama.delete("ocean-expert")    # Clean up when done
```

---

## RAG Systems — The Full Picture

### What is RAG?

**RAG (Retrieval-Augmented Generation)** solves a fundamental limitation of LLMs: they only know what they were trained on. They can't answer questions about your private documents, internal data, or events after their training cutoff.

RAG also addresses **hallucination**: by instructing the LLM to answer only from retrieved context, it can't fabricate information.

### The Full RAG Pipeline

```
  ══════════════════════════════════════════════════════════════
  PHASE 1 — INGESTION (runs once, when you first set up the system)
  ══════════════════════════════════════════════════════════════

  ┌─────────────┐
  │ Your PDFs / │
  │ Documents   │
  └──────┬──────┘
         │  Load & parse text
         ▼
  ┌──────────────────────┐
  │   Text Extraction    │  (UnstructuredPDFLoader / PDFPlumberLoader)
  │   "The ocean is...   │
  │    salty because..." │
  └──────────┬───────────┘
             │  Split into overlapping chunks
             ▼
  ┌──────────────────────┐
  │   Text Chunks        │  chunk_size=1200, chunk_overlap=300
  │  [chunk 1][chunk 2]  │  (overlap prevents losing context at boundaries)
  │  [chunk 3][chunk 4]  │
  └──────────┬───────────┘
             │  Embed each chunk
             ▼
  ┌──────────────────────┐
  │  Embedding Model     │  nomic-embed-text (runs locally via Ollama)
  │  text → numbers      │  "ocean" → [0.23, -0.45, 0.12, ...]
  └──────────┬───────────┘
             │  Store vectors
             ▼
  ┌──────────────────────┐
  │   Vector Database    │  ChromaDB (saved to disk)
  │  chunk1 → [vector]   │
  │  chunk2 → [vector]   │
  │  chunk3 → [vector]   │
  └──────────────────────┘

  ══════════════════════════════════════════════════════════════
  PHASE 2 — QUERY (runs on every user question)
  ══════════════════════════════════════════════════════════════

  ┌─────────────────────────┐
  │  User Question:         │
  │  "How do I report BOI?" │
  └───────────┬─────────────┘
              │  Embed the question (same model)
              ▼
  ┌─────────────────────────┐
  │  Question Vector:       │
  │  [0.45, 0.12, -0.33...] │
  └───────────┬─────────────┘
              │  Similarity search in vector DB
              ▼
  ┌─────────────────────────┐
  │  Retrieved Chunks:      │  (most similar vectors = most relevant content)
  │  "BOI reporting         │
  │   requires you to..."   │
  │  "Filing deadline is..."│
  └───────────┬─────────────┘
              │
              ▼
  ┌──────────────────────────────────────────────────┐
  │  Augmented Prompt:                               │
  │                                                  │
  │  "Answer ONLY based on this context:             │
  │   [retrieved chunks here]                        │
  │                                                  │
  │   Question: How do I report BOI?"                │
  └───────────────────────┬──────────────────────────┘
                          │
                          ▼
               ┌──────────────────┐
               │   llama3.2 LLM   │
               └────────┬─────────┘
                        │
                        ▼
               ┌──────────────────┐
               │  Final Answer    │
               │  (grounded in    │
               │   your docs)     │
               └──────────────────┘
```

### What You Need for a RAG System

| Component | Role | Example |
|-----------|------|---------|
| **LLM** | Reads context and generates answers | `llama3.2` via Ollama |
| **Document Corpus** | Your private knowledge base | PDFs, text files, docs |
| **Embedding Model** | Converts text to vectors | `nomic-embed-text` |
| **Vector Store** | Stores and searches vectors | ChromaDB, Faiss, Pinecone |
| **Retrieval Mechanism** | Finds relevant chunks | MultiQueryRetriever |
| **Orchestration** | Connects all the pieces | LangChain |

---

## Vector Stores & Embeddings Deep Dive

### What Are Embeddings?

Embeddings are **numerical representations of text** — each word, sentence, or chunk gets converted into a list of numbers (a vector) that captures its meaning in multi-dimensional space.

The key insight: **text with similar meaning ends up with similar vectors.**

```
  ┌─────────────────────────────────────────────────────────────────┐
  │                   EMBEDDING SPACE (simplified to 2D)            │
  │                                                                 │
  │     cat ●  ● kitten      ◀── close together = similar meaning   │
  │         ●                                                       │
  │       kitty                                                     │
  │                                                                 │
  │                           automobile ●                          │
  │                                  ● car  ◀── close together     │
  │                             vehicle ●                           │
  │                                                                 │
  │                   ← distance = semantic difference →            │
  └─────────────────────────────────────────────────────────────────┘

  Real embeddings have 768, 1536, or 3072+ dimensions
  (impossible to visualize but the same principle applies)
```

### Similarity Search

When you query a vector database, it finds the stored vectors closest (by cosine similarity or dot product) to your query vector:

```
  Query: "why does seawater taste salty"
  Query vector: [0.45, -0.12, 0.78, ...]

  Vector DB compares against all stored chunk vectors:
  ┌─────────────────────────────────────────────┐
  │  chunk_1: "The ocean contains sodium..."    │  similarity: 0.94 ✓
  │  chunk_2: "Marine life adapts to salt..."   │  similarity: 0.87 ✓
  │  chunk_3: "The sky is blue because..."      │  similarity: 0.12 ✗
  │  chunk_4: "Salt concentration in seas..."   │  similarity: 0.91 ✓
  └─────────────────────────────────────────────┘
  Top k results returned → passed to LLM as context
```

### Vector Databases Compared

| Database | Persistence | Best For |
|----------|-------------|---------|
| ChromaDB | Disk (local) | Local dev, small-to-medium datasets |
| Faiss | In-memory | High-speed search, research |
| Pinecone | Cloud | Production at scale |
| Weaviate | Disk / Cloud | Production with rich metadata |

---

## Building PDF RAG with LangChain

### LangChain Overview

**LangChain** is a framework that makes it easy to build LLM applications by providing:
- Document loaders (PDF, Word, web pages, databases)
- Text splitters and chunking utilities
- Embedding model integrations
- Vector store connectors (ChromaDB, Faiss, Pinecone, etc.)
- Chain abstractions using a pipe `|` syntax
- Retriever patterns including MultiQueryRetriever

### MultiQueryRetriever — What and Why

Standard similarity search has a weakness: if your query is phrased differently from how the document phrases something, it may miss relevant chunks.

```
  Standard Retrieval:                 MultiQueryRetriever:
  ──────────────────                  ─────────────────────────────────────
  User: "How to file BOI?"            User: "How to file BOI?"
          │                                   │
          ▼                                   ▼
  1 similarity search               LLM generates 5 versions:
          │                           1. "How to file BOI?"
          ▼                           2. "BOI submission process"
  Some results                       3. "Reporting beneficial ownership"
  (may miss relevant                 4. "FinCEN BOI requirements"
   differently-worded                5. "Steps to complete BOI report"
   chunks)                                   │
                                     5 searches run in parallel
                                             │
                                     Results combined + deduplicated
                                             │
                                     More complete, better results ✓
```

### The LangChain Chain Pattern

LangChain uses a pipe (`|`) syntax to compose processing steps — each step feeds into the next:

```
  ┌───────────────────────────────────────────────────────────────┐
  │                    THE CHAIN                                  │
  │                                                               │
  │  {"context": retriever,  ──▶  prompt  ──▶  llm  ──▶  parser │
  │   "question": passthrough}                                    │
  │                                                               │
  │  Step 1: retriever    → fetches relevant document chunks      │
  │  Step 2: passthrough  → passes user question through as-is    │
  │  Step 3: prompt       → formats both into the RAG template    │
  │  Step 4: llm          → generates the answer                  │
  │  Step 5: parser       → converts output to plain string       │
  └───────────────────────────────────────────────────────────────┘
```

```python
chain = (
    {"context": retriever, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)

answer = chain.invoke("How do I report BOI?")
```

### Text Chunking — Why Overlap Matters

```
  Document text:
  "...The filing deadline is January 1st. All companies must
   submit their beneficial ownership information before this date..."

  WITHOUT overlap (chunk_size=50, overlap=0):
  Chunk 1: "...The filing deadline is January 1st. All companies"
  Chunk 2: "must submit their beneficial ownership information..."

  Problem: "January 1st" and "filing" are in chunk 1
           "submit" and "deadline" context is in chunk 2
           A search for "filing deadline" might miss chunk 2

  WITH overlap (chunk_size=50, overlap=15):
  Chunk 1: "...The filing deadline is January 1st. All companies"
  Chunk 2: "All companies must submit their beneficial ownership..."
                ↑↑↑↑↑↑↑↑↑↑↑↑↑
            Overlapping section ensures boundary context is preserved
```

---

## Function Calling (Tool Use)

### What is Function Calling?

Function calling (also called **tool use**) is a capability where the LLM can decide to invoke external functions you define — rather than just generating text.

```
  WITHOUT Function Calling:           WITH Function Calling:
  ─────────────────────────           ─────────────────────────────────────
  User: "What's the price             User: "What's the price of apples?"
         of apples?"                          │
         │                                    ▼
         ▼                            LLM sees available tools:
  LLM: "Apples typically              - fetch_price_and_nutrition(item)
        cost $1-3 per pound"          - fetch_recipe(category)
        (may hallucinate,                      │
         uses training data)         LLM decides: "I should call
                                      fetch_price_and_nutrition('apples')"
                                              │
                                              ▼
                                     Your Python function runs
                                     (hits a real API / database)
                                              │
                                              ▼
                                     Real result returned to LLM
                                              │
                                              ▼
                                     LLM: "Apples cost $2.34 per lb,
                                           with 95 kcal per 100g"
                                           (accurate, real data)
```

### The Full Function Calling Flow

```
  ┌──────────┐   1. User message     ┌───────────┐
  │  User    │──────────────────────▶│    LLM    │
  └──────────┘                       └─────┬─────┘
                                           │  2. LLM decides to call a tool
                                           ▼
                                    ┌─────────────┐
                                    │ Tool Call:  │
                                    │ function:   │
                                    │  "fetch_    │
                                    │   price"    │
                                    │ args:       │
                                    │  {"item":   │
                                    │   "apple"}  │
                                    └──────┬──────┘
                                           │  3. Your code runs the function
                                           ▼
                                    ┌─────────────┐
                                    │ Your Python │
                                    │ Function    │
                                    │ (real API   │
                                    │  call here) │
                                    └──────┬──────┘
                                           │  4. Return result to LLM
                                           ▼
                                    ┌─────────────┐
                                    │   Result:   │
                                    │ {price:     │
                                    │  "$2.34",   │
                                    │  cal: 95}   │
                                    └──────┬──────┘
                                           │  5. LLM formulates final answer
                                           ▼
                                    ┌─────────────┐    6. Response
                                    │    LLM      │─────────────────▶ User
                                    └─────────────┘
```

### Tool Definition Format

```python
tools = [
    {
        "type": "function",
        "function": {
            "name": "fetch_price_and_nutrition",
            "description": "Fetch price and nutrition data for a grocery item",
            "parameters": {
                "type": "object",
                "properties": {
                    "item": {
                        "type": "string",
                        "description": "The name of the grocery item"
                    }
                },
                "required": ["item"]
            }
        }
    }
]
```

> The `description` field is what the LLM reads to decide whether to call this tool — write it clearly and specifically.

### Why Async for Function Calling?

When you have 20 grocery items to fetch prices for, synchronous calls take 20x longer than necessary:

```
  Synchronous (one at a time):         Async (all at once):
  ─────────────────────────────         ─────────────────────────────
  fetch("apple")   → wait 0.1s         fetch("apple")   ─┐
  fetch("banana")  → wait 0.1s         fetch("banana")  ─┤ all start
  fetch("milk")    → wait 0.1s         fetch("milk")    ─┤  at once
  ...                                  ...              ─┤
  Total: 20 × 0.1s = 2 seconds         Total: ~0.1s     ─┘
```

---

## Voice RAG System

### Architecture

A voice RAG system combines document retrieval with text-to-speech — the user asks a question about a document and hears the answer spoken aloud.

```
  ┌──────────────┐
  │  PDF Files   │
  │  (in /data)  │
  └──────┬───────┘
         │  PDFPlumberLoader
         ▼
  ┌──────────────┐
  │ Text Extract │
  │  + Chunking  │
  └──────┬───────┘
         │  nomic-embed-text / FastEmbedEmbeddings
         ▼
  ┌──────────────┐
  │  ChromaDB    │
  │  (on disk,   │◀──── Persisted so you don't re-embed every run
  │  ./db/)      │
  └──────┬───────┘
         │
         │  ◀──── User types a question
         ▼
  ┌──────────────┐
  │ MultiQuery   │
  │ Retriever    │
  └──────┬───────┘
         │
         ▼
  ┌──────────────┐
  │  llama3.2    │
  │  (LangChain  │
  │   RAG chain) │
  └──────┬───────┘
         │  Text answer
         ▼
  ┌──────────────┐
  │  ElevenLabs  │
  │  TTS API     │◀──── eleven_turbo_v2 model, streaming
  └──────┬───────┘
         │  Audio stream
         ▼
  ┌──────────────┐
  │   Speaker    │  User hears the answer spoken aloud
  └──────────────┘
```

### Key Design Decisions

**Why PDFPlumberLoader over UnstructuredPDFLoader?**
- PDFPlumber is more accurate for structured PDFs with tables and columns
- UnstructuredPDFLoader is more flexible for a wider variety of document formats

**Why FastEmbedEmbeddings over OllamaEmbeddings?**
- FastEmbed runs quantized embedding models locally with much faster throughput
- OllamaEmbeddings is easier to set up but slower for large document sets

**Why persist the vector store?**
- Embedding 50+ pages of PDFs takes minutes and compute
- Saving to disk means future runs load in seconds

```python
# First run: create and save
vector_db = Chroma.from_documents(
    documents=docs,
    embedding=fastembedding,
    persist_directory="./db/vector_db"
)

# All future runs: load instantly
vector_db = Chroma(
    embedding_function=fastembedding,
    persist_directory="./db/vector_db"
)
```

---

## DeepSeek R1 — Reasoning Models

### What is DeepSeek R1?

DeepSeek R1 is an **open-source reasoning model** from DeepSeek (a Chinese AI company) that achieves performance comparable to — and in some benchmarks surpassing — OpenAI's o1 model.

What makes a reasoning model different from a standard LLM:

```
  Standard LLM:                       Reasoning Model (DeepSeek R1):
  ─────────────                       ──────────────────────────────
  User: "What is 17 × 23?"            User: "What is 17 × 23?"
          │                                   │
          ▼                                   ▼
  LLM → "391"                        <think>
  (direct, may be wrong)              Let me break this down:
                                      17 × 20 = 340
                                      17 × 3  = 51
                                      340 + 51 = 391
                                      </think>
                                      
                                      The answer is 391.
                                      (shows reasoning, more reliable)
```

### Core Architecture: Transformer Foundation

```
  ┌─────────────────────────────────────────────────────────────────┐
  │                    TRANSFORMER ARCHITECTURE                     │
  │                                                                 │
  │   Input tokens                                                  │
  │      │                                                          │
  │      ▼                                                          │
  │  ┌──────────────────────────────────────────────────┐           │
  │  │              ATTENTION MECHANISM                  │          │
  │  │  Lets each token "look at" all other tokens      │           │
  │  │  to understand relationships and context          │          │
  │  └──────────────────────────────────────────────────┘           │
  │      │                                                          │
  │      ▼  (repeated N times = N layers)                           │
  │  ┌──────────────────────────────────────────────────┐           │
  │  │              FEED-FORWARD NETWORK                 │          │
  │  │  Processes and transforms the attended features   │          │
  │  └──────────────────────────────────────────────────┘           │
  │      │                                                          │
  │      ▼                                                          │
  │   Output token probabilities → generate next token              │
  └─────────────────────────────────────────────────────────────────┘
```

### DeepSeek R1 Additional Innovations

- **Reinforcement Learning from Human Feedback (RLHF)** — trained to prefer correct, reasoned answers
- **Chain-of-Thought prompting built in** — reasons step-by-step before answering
- **Distillation pipeline** — a giant base model teaches smaller, deployable versions
- **Multiple model sizes** — from 1.5B (laptop-friendly) to 671B (datacenter-class)

### OpenAI-Compatible Local API

Ollama exposes an OpenAI-compatible API — meaning code written for OpenAI works on local models with minimal changes:

```
  OpenAI (cloud):                       Ollama (local):
  ──────────────────────────────        ─────────────────────────────────────
  base_url = "https://api.openai.com"   base_url = "http://localhost:11434/v1/"
  api_key  = "sk-..."                   api_key  = "ollama"  (any string)
  model    = "gpt-4o"                   model    = "deepseek-r1:1.5b"

  Everything else is identical ✓
```

```python
from openai import OpenAI

client = OpenAI(
    api_key="ollama",
    base_url="http://localhost:11434/v1/"
)

response = client.chat.completions.create(
    model="deepseek-r1:1.5b",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Solve the traveling salesman problem"}
    ],
    stream=True
)

for chunk in response:
    print(chunk.choices[0].delta.content, end="", flush=True)
```

---

## Model Variants Explained

Models come in different forms optimized for different hardware constraints:

```
  ┌─────────────────────────────────────────────────────────────────────┐
  │                      MODEL VARIANT SPECTRUM                         │
  │                                                                     │
  │  BASE MODEL                                                         │
  │  ┌──────────────────┐                                               │
  │  │ Full parameters  │  Max accuracy, needs datacenter GPU           │
  │  │ 671B parameters  │  Think: supercomputer                         │
  │  └────────┬─────────┘                                               │
  │           │  Distillation (smaller model mimics larger one)         │
  │           ▼                                                         │
  │  DISTILLED MODELS                                                   │
  │  ┌──────────────────┐                                               │
  │  │ Medium: ~7B-14B  │  Good reasoning, needs GPU (8-16GB VRAM)      │
  │  │ Small:  ~3B      │  Decent capability, CPU-friendly              │
  │  │ Tiny:   ~1.5B    │  Simple tasks only, runs anywhere             │
  │  └────────┬─────────┘                                               │
  │           │  Quantization (reduce number precision)                 │
  │           ▼                                                         │
  │  QUANTIZED VERSIONS                                                 │
  │  ┌──────────────────┐                                               │
  │  │ Q4 / Q8 versions │  ~4x smaller, slightly less accurate          │
  │  │ of any size above│  Much faster inference, lower RAM             │
  │  └──────────────────┘  Think: phone                                 │
  └─────────────────────────────────────────────────────────────────────┘

  Practical guide:
  ┌────────────────────┬────────────────────────────────────────────────┐
  │ Your Hardware      │ Recommended Model                              │
  ├────────────────────┼────────────────────────────────────────────────┤
  │ 4 GB RAM           │ llama3.2:1b or deepseek-r1:1.5b               │
  │ 8 GB RAM           │ llama3.2:3b, good for most tasks              │
  │ 16 GB RAM          │ llama3.2:8b, strong reasoning                 │
  │ 32 GB RAM          │ llama3.2:70b (Q4), near-GPT-4 quality         │
  │ 80+ GB VRAM        │ Full 70B+ base models                         │
  └────────────────────┴────────────────────────────────────────────────┘
```

---

## Open Source vs Closed Source LLMs

```
  ┌────────────────────────────────┬──────────────────────────────────────┐
  │      OPEN SOURCE MODELS        │       CLOSED SOURCE MODELS           │
  │   (Ollama, Hugging Face)       │  (OpenAI, Anthropic, Google)         │
  ├────────────────────────────────┼──────────────────────────────────────┤
  │ ✅ Full transparency            │ ✅ Reliability & SLA guarantees    │
  │ ✅ Unlimited customization      │ ✅ Ease of use (API key → running) │
  │ ✅ Community collaboration      │ ✅ Enterprise compliance features  │
  │ ✅ Cost control (pay hardware)  │ ✅ Consistently optimized at scale │
  │ ✅ Complete privacy             │                                    │
  │                                │                                      │
  │ ⚠️  Initial setup complexity   │ ⚠️  Usage costs (per token)         │
  │ ⚠️  You own the infra          │ ⚠️  Your data leaves your network   │
  │ ⚠️  Hardware requirements      │ ⚠️  Limited customization           │
  │ ⚠️  You manage updates         │ ⚠️  Vendor lock-in risk             │
  └────────────────────────────────┴──────────────────────────────────────┘

  Decision framework:
  ┌─────────────────────────────────────────────────────────────┐
  │ Use LOCAL when:              │ Use CLOUD when:              │
  │ - Sensitive / private data   │ - Need guaranteed uptime SLA │
  │ - Learning & development     │ - Scaling to millions of req │
  │ - Cost is a concern          │ - Non-technical team members │
  │ - Need full customization    │ - Need latest frontier model │
  │ - Offline use required       │ - Fast integration priority  │
  └─────────────────────────────────────────────────────────────┘
```

---

## Applications You Can Build

With the knowledge in this guide, you can build all of these from scratch:

| Application | What It Does | Core Technologies |
|-------------|-------------|------------------|
| **Document Chatbot** | Chat with your PDFs or internal docs | RAG + LangChain + ChromaDB |
| **Grocery Categorizer** | Classify and sort items using LLM | `ollama.generate` + file I/O |
| **Image Describer** | Describe or analyze images | LLaVA multimodal model |
| **Voice Assistant** | Documents → spoken answers | RAG + ElevenLabs TTS |
| **Sentiment Analyzer** | Classify text emotion/tone | `ollama.chat` + custom prompt |
| **Custom AI Persona** | Domain-specific assistant | Modelfiles |
| **Async Data Fetcher** | LLM + real API calls combined | Function calling + AsyncClient |
| **Code Reviewer** | Review, explain, and generate code | `codellama` or `deepseek-r1` |
| **Research Analyzer** | Summarize and reason about papers | DeepSeek R1 chain-of-thought |
| **Streamlit Q&A App** | Web UI for document Q&A | RAG + Streamlit |
| **Multi-Agent System** | Specialized assistants in one UI | Ollama + category routing |

---

## Full Tech Stack Reference

### Models

| Model | Use Case | Pull Command |
|-------|---------|-------------|
| `llama3.2` | General text, chat, summarization | `ollama pull llama3.2` |
| `llava:7b` | Image + text multimodal | `ollama pull llava:7b` |
| `codellama` | Code generation, explanation, review | `ollama pull codellama` |
| `nomic-embed-text` | Text embeddings for RAG | `ollama pull nomic-embed-text` |
| `deepseek-r1:1.5b` | Reasoning, math, logic | `ollama pull deepseek-r1:1.5b` |
| `mistral` | Fast, efficient text generation | `ollama pull mistral` |

### Python Dependencies

```bash
# Core
pip install ollama                          # Ollama Python client
pip install requests                        # HTTP client for REST API
pip install openai                          # OpenAI SDK (works with Ollama too)

# LangChain ecosystem
pip install langchain                       # LLM orchestration framework
pip install langchain-community             # Document loaders, vector stores
pip install langchain-ollama               # LangChain + Ollama integration
pip install langchain-text-splitters       # Text chunking utilities

# Vector databases & embeddings
pip install chromadb                        # Local vector database
pip install fastembed                       # Fast local embedding generation

# Document parsing
pip install unstructured                    # General document parsing
pip install pdfplumber                      # Accurate PDF text extraction

# Applications
pip install streamlit                       # Web UI framework
pip install elevenlabs                      # Text-to-speech synthesis
pip install python-dotenv                  # Environment variable management
```

### UI Tools (No Code Required)

| Tool | What It Does |
|------|-------------|
| **Msty App** (msty.app) | Desktop GUI for local models — attach images, docs, build knowledge stacks |
| **LM Studio** | Model explorer and GUI chat — great for DeepSeek R1 distilled models |

### Interaction Methods Summary

```
  ┌──────────────────────────────────────────────────────────────────┐
  │                  WAYS TO INTERACT WITH OLLAMA                    │
  │                                                                  │
  │  1. CLI (ollama run llama3.2)                                    │
  │     └── Best for: quick testing, exploring models                │
  │                                                                  │
  │  2. REST API (curl / any HTTP client)                            │
  │     └── Best for: any language, maximum control                  │
  │                                                                  │
  │  3. Python ollama library (import ollama)                        │
  │     └── Best for: Python apps, clean API                         │
  │                                                                  │
  │  4. OpenAI SDK with local base_url                               │
  │     └── Best for: migrating existing OpenAI code to local        │
  │                                                                  │
  │  5. LangChain (ChatOllama, OllamaEmbeddings)                     │
  │     └── Best for: RAG systems, complex LLM pipelines             │
  │                                                                  │
  │  6. Desktop apps (Msty, LM Studio)                               │
  │     └── Best for: non-technical users, quick demos               │
  └──────────────────────────────────────────────────────────────────┘
```

---

## Key Takeaways

1. **Ollama abstracts all the complexity** of running LLMs locally — you get a CLI and REST API without managing CUDA, GPU drivers, or model weight formats

2. **Model parameters directly affect the performance vs. resource trade-off** — understanding quantization and context length lets you pick the right model for your hardware

3. **RAG is the key to LLMs knowing about your private data** — it bridges the gap between static training knowledge and your dynamic, private documents

4. **Embeddings are the foundation of semantic search** — similar meaning → similar vectors → accurate retrieval even when exact words don't match

5. **Function calling transforms LLMs from chatbots into agents** — they can trigger real actions in your systems, not just generate text

6. **DeepSeek R1 brings reasoning-grade AI to local deployment** — chain-of-thought problem solving, for free, privately, on consumer hardware

7. **The OpenAI-compatible API means existing code is portable** — swap `api_key` and `base_url`, and code targeting OpenAI runs on local Ollama models

8. **Persistent vector stores are essential for production RAG** — embed once, query forever, saving time and compute on every application startup

---

*This document covers the complete theoretical and conceptual foundation for building local LLM applications — from model fundamentals through production RAG systems, voice interfaces, function calling, and reasoning model deployment.*

