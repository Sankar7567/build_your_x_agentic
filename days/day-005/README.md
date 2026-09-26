# Day 005: Self-Correction Code Executor Sandbox

## 📌 Executive Summary
This agent executes untrusted code in isolated environments with automatic error detection and self-correction capabilities. Unlike basic code execution tools, it analyzes runtime errors, syntax violations, and logical flaws through LLM reasoning to generate corrected code attempts. The system enables agents to iteratively refine code solutions without human intervention, reducing debugging time by 70%+ for common programming tasks.

## 🎯 Learning Objectives
- Master secure code execution using subprocess isolation and resource limits
- Implement error classification systems for syntax, runtime, and logical errors
- Validate self-correction loops that improve code quality through iterative LLM feedback

## 🏗️ Software Requirements Specification (SRS)
### Functional Requirements
1. **Isolated Code Executor:**
   - **Input:** Code string to execute, language identifier (currently Python-only), resource limits (CPU time, memory, disk I/O)
   - **Processing Logic:** Create subprocess with restricted permissions, apply resource limits via rlimit/seccomp, capture stdout/stderr and exit codes, prevent file system escapes and network access
   - **Output:** Execution result containing success status, output streams, error messages, and resource usage metrics

2. **Error Classification & Analysis System:**
   - **Input:** Raw execution output (stdout, stderr, exit code), original code string
   - **Processing Logic:** Categorize errors into: syntax (parsing failures), runtime (exceptions), timeout (resource limits), logical (incorrect output), generate structured error reports with line numbers and error types
   - **Output:** Error analysis object suitable for LLM consumption with error category, location, and suggested focus areas

3. **LLM-Powered Code Corrector:**
   - **Input:** Original code, execution results, error analysis, task description or intent
   - **Processing Logic:** Construct correction prompt showing code, error details, and expected behavior, request LLM to generate corrected version addressing all issues, validate syntax before return
   - **Output:** Corrected code string ready for re-execution attempt

4. **Iterative Refinement Controller:**
   - **Input:** Maximum correction attempts, progress metrics from previous attempts
   - **Processing Logic:** Manage correction cycles, detect stagnation (identical errors), implement exponential backoff between attempts, terminate on success or max attempts reached
   - **Output:** Final execution result with correction history and performance metrics

### Non-Functional Requirements
- **Latency Target:** Code execution ≤ 2s per attempt (excluding actual computation), error analysis ≤ 100ms, code correction ≤ 500ms (excluding LLM latency)
- **Token Overhead:** Maximum 3000 tokens per correction cycle (includes code, error details, and task context)
- **Dependencies:** Python 3.11+, subprocess, resource, signal modules. Strictly ZERO heavy agent frameworks (LangChain, AutoGen, CrewAI, LlamaIndex). Uses only standard library for isolation and process control.

## 🛠️ Recommended Tech Stack
- **Language:** Python 3.11+
- **Key Modules:** `subprocess` (for isolated execution), `resource` (for CPU/memory limits), `signal` (for timeout handling), `typing` (TypedDict, Optional, Literal), `dataclasses`, `json`, `textwrap` (for code formatting)
- **LLM API/Model:** Anthropic Claude 3.5 Sonnet via Messages API (raw SDK) or OpenAI GPT-4o via raw SDK; configured with temperature=0.2 for focused corrections, max_tokens=2000 per correction call
