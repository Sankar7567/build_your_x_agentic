# Day 004: Cosine Similarity Vector Store (No DB libs)

## 📌 Executive Summary
This vector store implements efficient cosine similarity search for high-dimensional embeddings using only Python standard library and mathematical optimizations. Unlike database-dependent solutions, it provides lightweight, embeddable vector retrieval suitable for edge deployments and learning environments. The system enables agents to store and retrieve contextual memories, examples, and knowledge snippets based on semantic similarity rather than exact keyword matching.

## 🎯 Learning Objectives
- Master cosine similarity mathematics and efficient implementation techniques
- Implement approximate nearest neighbor search using locality-sensitive hashing principles
- Validate vector storage performance with varying dimensions and dataset sizes

## 🏗️ Software Requirements Specification (SRS)
### Functional Requirements
1. **Vector Storage Engine:**
   - **Input:** Vectors as lists of floats with associated metadata (string key, optional payload), dimensionality validation
   - **Processing Logic:** Store vectors in memory-efficient arrays, normalize vectors for cosine similarity, build auxiliary index structures for faster search
   - **Output:** Storage confirmation with internal ID, ability to retrieve vectors by key or metadata

2. **Cosine Similarity Search:**
   - **Input:** Query vector (list of floats), top-k parameter for results count
   - **Processing Logic:** Calculate cosine similarity between query and all stored vectors using optimized dot product and magnitude calculations, return top-k most similar vectors
   - **Output:** List of (key, similarity_score, payload) tuples sorted by descending similarity

3. **Batch Operations Interface:**
   - **Input:** Multiple vectors for storage or multiple query vectors for search
   - **Processing Logic:** Optimize batch processing through vectorized operations where possible, reduce per-operation overhead
   - **Output:** Batch storage results or batch search results maintaining input order correspondence

4. **Persistence & Serialization:**
   - **Input:** Storage state to save, file path or file-like object
   - **Processing Logic:** Serialize vectors, metadata, and auxiliary indexes to disk in efficient binary format
   - **Output:** Saved storage state, ability to reload and continue operations seamlessly

### Non-Functional Requirements
- **Latency Target:** Single vector search ≤ 10ms for 10,000 vectors of 384 dimensions, batch search ≤ 100ms
- **Token Overhead:** N/A (this is a storage component, not LLM-facing)
- **Dependencies:** Python 3.11+, numpy 1.24.0+ (for vector operations - acceptable as it's not an agent framework). Strictly ZERO heavy agent frameworks (LangChain, AutoGen, CrewAI, LlamaIndex).

## 🛠️ Recommended Tech Stack
- **Language:** Python 3.11+
- **Key Modules:** `typing` (TypedDict, Optional, List, Tuple, Union), `math` (for sqrt and dot product operations), `json` (for metadata serialization), `bisect` (for maintaining sorted lists), `heapq` (for efficient top-k selection)
- **LLM API/Model:** Anthropic Claude 3.5 Sonnet via Messages API (raw SDK) or OpenAI GPT-4o via raw SDK; note that this component generates embeddings via LLM but does not depend on specific LLMs for its core operation
