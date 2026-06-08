# RAG: How AI Learns to Look Things Up

**AI Learning** · June 9, 2026

## 🧒 Like You're 5

Imagine you're taking a test, but the teacher says *"You can't use your textbook — only what's in your head!"* You'd get some things right, but you'd also make up stuff you're not sure about. That's a normal AI.

Now imagine the teacher says *"Okay, you ARE allowed to open your textbook."* Suddenly you can look up facts, find exact quotes, and give answers you KNOW are correct. That's RAG — giving your AI a textbook to check before it answers.

## ↓ Now let's call it what it really is ↓

## 🔧 For the Engineer

**Retrieval-Augmented Generation (RAG)** pipelines combine a retriever (vector search over a knowledgebase) with a generator (LLM) so that answers are grounded in real documents — not just parametric memory. This dramatically reduces hallucinations and lets the model answer questions about data it was never trained on.

### 1. Indexing Phase
Documents are split into chunks (256–1024 tokens with overlap), embedded via a model like `text-embedding-ada-002` or `BGE-large`, and stored in a vector DB (Chroma, Pinecone, Weaviate, Milvus, or pgvector).

### 2. Retrieval Phase
User query → embed → top-K nearest neighbor search in vector space. Common metrics: cosine similarity, dot product, or hybrid search (dense + BM25 sparse). Retrieval quality is the #1 bottleneck in RAG systems.

### 3. Augmented Prompt
Retrieved chunks are injected into the prompt context alongside the user query:

```
System: Use the following context to answer the question.
If you don't know, say "I don't know."

Context:
{chunk_1}
{chunk_2}
{chunk_3}

Question: {user_query}
```

### Reranking
First-pass retrieval is fast but imprecise. A cross-encoder reranker (e.g., `cross-encoder/ms-marco-MiniLM-L-6-v2`) re-scores the top-20 results and picks the best 3–5. This 2-stage approach is the production standard.

### Evaluating RAG
Key metrics: **context recall** (did we retrieve the right chunks?), **answer faithfulness** (is the answer supported by context?), and **answer relevance**. Frameworks: RAGAS, TruLens.

```python
from langchain_community.vectorstores import Chroma
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain.chains import RetrievalQA

vectorstore = Chroma.from_documents(docs, OpenAIEmbeddings())
retriever = vectorstore.as_retriever(search_kwargs={"k": 5})
qa = RetrievalQA.from_chain_type(
    llm=ChatOpenAI(model="gpt-4o"),
    retriever=retriever,
    return_source_documents=True
)
result = qa.invoke({"query": "What is observability?"})
```

## ⚡ Micro-action

**Do this in 5 minutes:** Go to [github.com/explodinggradients/ragas](https://github.com/explodinggradients/ragas) and read the Metrics section of the README. Ragas gives you `context_precision`, `context_recall`, `faithfulness`, and `answer_relevancy` out of the box. Understanding these four numbers tells you whether your RAG pipeline actually works or just looks like it works.

---
Tags: RAG, Vector Search, LangChain, Reranking, RAGAS
💫 Small steps. Every day.
