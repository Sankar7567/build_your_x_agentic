# Day 003: Context Window Sliding Buffer & Memory Pruner

## 📌 Executive Summary
This component manages the agent's conversation history within fixed LLM context limits by implementing intelligent sliding window mechanisms and semantic importance scoring. Unlike naive truncation, it preserves critical information while discarding less relevant exchanges, enabling sustained coherent reasoning over extended interactions. The system dynamically balances recency versus relevance to maintain optimal context for decision-making without exceeding token limits.

## 🎯 Learning Objectives
- Master token counting algorithms for various LLM encodings (cl100k_base, o200k_base)
- Implement adaptive importance scoring combining recency, semantic relevance, and information density
- Validate memory pruning strategies that preserve essential context while minimizing information loss

## 🏗️ Software Requirements Specification (SRS)
### Functional Requirements
1. **Token-Aware Sliding Window:**
   - **Input:** Conversation history as list of message objects with `role` (system/user/assistant) and `content` (string), maximum context tokens limit
   - **Processing Logic:** Calculate cumulative token count using tiktoken-compatible encoding, identify pruning threshold when limit exceeded, remove oldest messages while preserving system messages and recent high-value exchanges
   - **Output:** Pruned conversation history fitting within token limit, metadata on removed messages and token savings

2. **Semantic Importance Scorer:**
   - **Input:** Individual conversation turns, current task objective or agent goal
   - **Processing Logic:** Score each turn based on: (a) recency weight (exponential decay), (b) semantic similarity to current objective using embeddings, (c) information density (named entities, numbers, specific terms), (d) dialogue act classification (questions/answers vs. chitchat)
   - **Output:** Normalized importance score (0.0-1.0) for each conversation turn

3. **Adaptive Pruning Strategy:**
   - **Input:** Conversation history with importance scores, token limit, preservation ratio for recent exchanges
   - **Processing Logic:** Apply hybrid algorithm: always keep system messages, preserve N most recent turns regardless of score, apply importance-based filtering to middle section, ensure critical information (tool results, key decisions) never pruned
   - **Output:** Optimized conversation history maximizing relevance within constraints

4. **Context Overflow Protection:**
   - **Input:** Incoming message that would exceed context limit
   - **Processing Logic:** Predictive triggering of pruning before adding new message, summary generation of pruned content when information loss exceeds threshold, emergency compression for extreme overflow scenarios
   - **Output:** Safe context window ready for new message, optional summary of pruned content for agent awareness

### Non-Functional Requirements
- **Latency Target:** Importance scoring ≤ 2ms per turn, pruning decision ≤ 5ms for 100-turn history
- **Token Overhead:** Maximum 50 tokens overhead for pruning metadata and scoring storage
- **Dependencies:** Python 3.11+, tiktoken 0.5.0+ (for accurate token counting). Strictly ZERO heavy agent frameworks (LangChain, AutoGen, CrewAI, LlamaIndex). Uses only standard library and tiktoken for token management.

## 🛠️ Recommended Tech Stack
- **Language:** Python 3.11+
- **Key Modules:** `typing` (TypedDict, Optional, List, Dict), `dataclasses`, `collections` (deque for efficient sliding window), `heapq` (for priority-based pruning), `bisect` (for efficient insertion in sorted lists)
- **LLM API/Model:** Anthropic Claude 3.5 Sonnet via Messages API (raw SDK) or OpenAI GPT-4o via raw SDK; token counting uses cl100k_base encoding for Claude/GPT-4 or o200k_base for GPT-4o
