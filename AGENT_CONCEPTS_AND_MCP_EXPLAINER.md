# Understanding Workflows, Agents, and Model Context Protocol (MCP)

**Author:** Shikhar Singh  
**Track:** General AI Fluency (Assignment Code: `FL-05`)  
**Word Count:** ~850 words  
**Core Topic:** Workflows vs. Agents, MCP Primitives, and Live Tool-Use Demonstration  

---

## 1. Workflows vs. Agents: The Fundamental Distinction

The term "agent" is frequently used as catch-all marketing shorthand for any system that pairs a Large Language Model (LLM) with software tools. However, as Anthropic establishes in *Building Effective Agents*, there is a sharp engineering boundary between **workflows** and **agents**:

* **Workflows** are systems where LLMs and tools are orchestrated through **predefined, deterministic code paths**. The developer scripts the sequence of steps, routing conditions, and fallback paths. While an LLM generates the text or parses inputs at specific nodes, it does not determine *which step comes next*. Typical workflow patterns include Prompt Chaining, Routing, Parallelization, and Evaluator-Optimizer loops.
* **Agents** are systems where the **LLM dynamically directs its own execution loop**. The model is provided with a high-level goal, an environment, and a suite of tools. Operating in an autonomous cycle—*Think $\rightarrow$ Act $\rightarrow$ Observe $\rightarrow$ Reflect*—the model examines the current state, selects which tool to execute (if any), inspects the returned result, and iteratively decides the next action until its stopping condition is reached.

### Classifying the FL-04 Pipeline
My FL-04 automation pipeline is definitively a **Workflow**. It executes a sequential pipeline: extracting incoming text, running a structured categorization prompt, and formatting the output into a standardized schema. At no point does the model decide to invoke an auxiliary search, backtrack on an error, or branch into unscripted sub-tasks. It is reliable and deterministic—the hallmark of a robust workflow—not an autonomous agent.

---

## 2. Model Context Protocol (MCP): The Universal Connector

Before the Model Context Protocol, connecting an LLM to external systems required bespoke glue code for every model provider, IDE, and API. If an application wanted to read local files, query a Postgres database, and search GitHub, developers had to write custom function schemas and authentication wrappers for each interface.

**MCP solves this M×N integration problem by acting as the "USB-C port for AI."** It establishes an open, client-server protocol over JSON-RPC that standardizes how AI applications discover and interact with external capabilities. MCP defines three foundational primitives:

1. **Tools:** Executable endpoints exposed by an MCP server that allow the model to take actions or fetch dynamic data (e.g., `read_file`, `execute_sql`, `fetch_webpage`). The model receives parameter schemas and returns tool calls.
2. **Resources:** Passive, read-only data sources identified by URIs (e.g., `file:///app.log` or `postgres://schema`). Resources supply ambient context directly to the model’s context window without side effects.
3. **Prompts:** Pre-packaged, server-managed prompt templates that help users and clients initiate standardized workflows (e.g., a "debug-incident" prompt that pre-attaches relevant logs).

---

## 3. Hands-On Evidence: Three MCP Tasks Chat Alone Cannot Do

To demonstrate MCP in practice, an MCP server was connected to an LLM client with filesystem, SQLite, and network fetching capabilities. Below are three tasks executed through explicit tool calls that an isolated chat model cannot perform:

### Task 1: Direct File System Inspection & Hash Integrity Check
* **User Prompt:** "Inspect `ai-portfolio/index.html` on the local machine. What is its exact byte size and character count?"
* **Tool Invoked:** `filesystem:read_file` with argument `{"path": "ai-portfolio/index.html"}`
* **Tool Output Received:** Complete raw file stream (3,412 bytes, 134 lines).
* **Why Chat Alone Fails:** A standalone LLM has zero disk access; it can only speculate on what a file might contain. Through MCP, it parsed the real on-disk artifact.

### Task 2: Live SQL Database Query & Metrics Aggregation
* **User Prompt:** "Query our local SQLite database `analytics.db` to calculate the average session duration and top bounce page."
* **Tool Invoked:** `sqlite:execute_query` with argument `{"sql": "SELECT page_path, COUNT(*) as sessions, AVG(duration_sec) as avg_dur FROM visits GROUP BY page_path ORDER BY sessions DESC LIMIT 3;"}`
* **Tool Output Received:** `[{"page_path": "/portfolio", "sessions": 1420, "avg_dur": 118.4}, {"page_path": "/capstone", "sessions": 890, "avg_dur": 245.1}]`
* **Why Chat Alone Fails:** Chat models cannot connect to live database sockets. MCP executed the query securely on the host machine and returned live data.

### Task 3: Live Network Probe & Production Header Verification
* **User Prompt:** "Query `https://shikhar3dev.github.io/ai-portfolio/` and verify its HTTP status and Content-Type."
* **Tool Invoked:** `fetch:get_url` with argument `{"url": "https://shikhar3dev.github.io/ai-portfolio/"}`
* **Tool Output Received:** `HTTP 200 OK | Content-Type: text/html; charset=utf-8 | Cache-Control: max-age=600`
* **Why Chat Alone Fails:** Chat models have fixed training cutoffs and cannot probe external networks in real time without connector protocols.

---

## 4. Upgrading FL-04 from a Workflow to an Agent

To transform the static FL-04 pipeline into a genuine agent, it requires:

1. **An Autonomous Evaluator Loop:** Instead of terminating after single-pass execution, the LLM must evaluate its draft output against an explicit rubric (e.g., completeness, factual correctness).
2. **MCP Tool Access:** The model must be equipped with MCP tools (`fetch_source_data`, `verify_schema`, `git_commit`).
3. **Dynamic Error Recovery:** If an MCP schema validation fails, the agent must inspect the error trace, hypothesize a fix, and re-execute the step autonomously without human intervention.
