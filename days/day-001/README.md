# Day 001: ReAct Loop from Scratch

## 🏗️ Software Requirements Specification (SRS)
### Functional Requirements
1. **Reasoning Parser:** Extract structured reasoning traces and action commands from LLM output using regex or string parsing. Must handle malformed outputs gracefully with fallback mechanisms.
2. **Tool Executor:** Execute predefined tools (e.g., calculator, web search mock) based on parsed actions, returning results or error messages to the agent for next reasoning step.
3. **Conversation Manager:** Maintain chat history between reasoning steps, truncating when approaching context limits while preserving essential context.
4. **Termination Condition:** Detect when the agent has completed its goal (via special action like "FINISH") or exceeded maximum iterations.

### Non-Functional Requirements
- **Latency & Overhead:** Target <2s per reasoning-action cycle (excluding LLM latency). Memory overhead should be O(n) where n is conversation turns.
- **Dependencies:** Python standard library only (typing, dataclasses, json, re, asyncio optional). Permitted: `httpx` for API calls, `pydantic` for validation. Zero heavy agent frameworks (LangChain/AutoGen/etc.).

## 🛠️ Recommended Tech Stack
- **Language:** Python 3.11+
- **Key Modules:** `typing` (TypedDict, Literal), `dataclasses`, `json`, `re`, `asyncio` (for async API calls), `httpx`
- **LLM API/Model:** Anthropic Claude 3 Haiku (fast/cheap for testing) or OpenAI GPT-3.5-turbo
