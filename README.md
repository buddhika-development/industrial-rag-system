# Advanced RAG System: Quality-Filtered Ingestion & Reranked Retrieval

This repository contains an advanced **Retrieval-Augmented Generation (RAG)** implementation that moves beyond naive retrieval by incorporating a **Data Ingestion Pipeline** with quality gates and a **Two-Stage Retrieval** process using rerankers.

## 🚀 Key Differences from Standard RAG
* **Quality-Gate Ingestion:** Only "high-value" chunks are vectorized. If a chunk's quality score is $\le 0.6$, it is discarded to prevent "database noise."
* **Two-Stage Retrieval:** Instead of just using vector similarity, the system uses a **Reranker** to re-evaluate the top-k results, significantly improving response accuracy.

---

## 🏗️ System Architecture

### 1. Data Injector Pipeline
The ingestion process ensures that the vector database (Pinecone) stays clean and contextually rich.
<img width="964" height="786" alt="image" src="https://github.com/user-attachments/assets/dc11401f-9534-4e83-98b9-82579e4a3edc" />

* **Reader & Uploader:** Extracts raw text from documents.
* **Recursive Chunking:** Uses `RecursiveCharacterTextSplitter` to maintain semantic integrity within chunks.
* **Quality Checker:** A dedicated logic layer (or LLM-based evaluator) that measures the "utility" of a chunk.
* **Filtering Logic:** * If **Quality > 0.6**: Convert to vector embedding $\rightarrow$ Store in **Pinecone**.
    * If **Quality ≤ 0.6**: Ignore the chunk.

### 2. Retriever Pipeline
The retrieval process focuses on precision to ensure the LLM receives the most relevant context possible.
<img width="791" height="655" alt="image" src="https://github.com/user-attachments/assets/73443f77-7250-4f3f-bf20-038449d79e88" />

* **Embedding Conversion:** Converts the user query into a vector.
* **Vector Search:** Retrieves the initial set of similar documents from Pinecone.
* **Reranking Layer:** Re-orders the retrieved documents based on their actual relevance to the query, filtering out "false positives" from the vector search.
* **Response Generation:** The LLM generates the final answer using only the high-quality, reranked context.

---

## 🛠️ Tech Stack

* **Language:** Python (Jupyter Notebooks)
* **Orchestration:** LangChain / LlamaIndex
* **Vector Database:** Pinecone
* **Text Splitting:** `RecursiveCharacterTextSplitter`
* **Reranker:** Cross-Encoder models (e.g., Cohere or BGE-Reranker)
* **LLM:** Mistral AI


## 📌 Implementation Notes
* **Thresholding:** The current quality threshold is set to **0.6**. This can be adjusted based on the specific domain of your data.
* **Reranking:** By using a reranker, we solve the common "Lost in the Middle" problem often found in standard RAG systems.
