# Agent-Mediated Applications & MCP: A Landscape Document

**Status:** Working draft
**Date:** January 2026
**Purpose:** Map the known territory before briefing the mcp-building and architecting skills

---

## 1. The Paradigm Shift

### What's Different About Agent-Mediated Apps

Traditional web applications follow a pattern established over decades:

```
User → UI (HTML/JS) → API/Backend → Database
```

The UI is the product. Users interact through forms, buttons, and screens. The backend serves the UI. Logic lives in code that executes deterministically.

Agent-mediated applications invert this:

```
User → Agent (LLM) → Capabilities (MCP) → Backend/Services
```

The **capabilities** are the product. The agent interprets user intent and decides how to accomplish it. The UI — if there is one — is a window into the agent's work, not the primary interaction surface.

### Three Levels of Agent Integration

| Level | Description | Architecture | UI Role |
|-------|-------------|--------------|---------|
| **1 - Agent as Feature** | Agent can use app tools | Traditional app + agent features | Primary interface |
| **2 - Agent as Core** | Agent IS the app's logic | MCP server(s) as middleware | Observation/steering |
| **3 - Agent as Builder** | Agent creates its own tools | Dynamic MCP generation | Minimal or none |

Most current "AI-powered" apps are Level 1: a chatbot bolted onto a traditional application.

The emerging opportunity is **Level 2**: applications where the MCP server(s) embody the core capabilities, and the agent orchestrates them to accomplish user intent. The UI becomes interface-agnostic — requests can come from chat, voice, CLI, other agents, or automation.

### The Key Insight

**The gap between intent and execution is where the agent lives.**

When a user says "find me 90 minutes for deep work this week," they're expressing intent. The execution requires:
- Understanding what "deep work" means for this user
- Scanning calendar availability
- Evaluating which slots fit the criteria
- Negotiating trade-offs if no perfect slot exists

This is judgment-work. It can't be reduced to a deterministic algorithm. The agent provides the judgment; MCP tools provide the capabilities the agent needs to act on that judgment.

---

## 2. The Decision Space for Architecting

### Primary Signal: Intent vs. Procedure

The clearest indicator of whether an app is an MCP-native candidate:

**Does the brief describe INTENT (what to achieve) or PROCEDURE (how to do it)?**

| Brief Language | Type | Example |
|----------------|------|---------|
| "Rename files matching pattern X to Y" | Procedure | Deterministic transformation |
| "Organize my downloads folder" | Intent | Requires judgment about what "organized" means |
| "POST user data to /api/users" | Procedure | Explicit API call |
| "Save this article about distributed systems" | Intent | Requires understanding content, choosing tags |

When the brief is procedure-focused, traditional architecture is appropriate. The "how" is already specified; code just needs to execute it.

When the brief is intent-focused, agent-mediation becomes valuable. The agent bridges the gap between what the user wants and what the system does.

### Secondary Signals

Beyond the intent/procedure distinction, these signals suggest MCP-native architecture:

| Signal | Why It Matters |
|--------|----------------|
| **Natural language as interface** | Users interact via conversation, not forms/buttons |
| **Domain benefits from memory/context** | Understanding improves with history |
| **Multi-step orchestration** | Accomplishing goals requires sequencing with decisions between |
| **Tolerance for non-determinism** | Same input can reasonably produce different outputs |
| **External service choreography** | Core function is coordinating calls across services |

### Counter-Signals (Traditional Architecture)

| Signal | Why Traditional Fits |
|--------|---------------------|
| **Precise, repeatable transformations** | Determinism is required |
| **High-throughput, low-latency** | Agent overhead is unacceptable |
| **Regulatory/audit determinism** | Must explain exactly why output was produced |
| **Rich interactive UI is the product** | User experience IS the value |
| **The "how" is fully specified** | No judgment required |

### The Spectrum, Not a Binary

Real applications rarely sit purely at one end. More common:

- **Traditional app with agent features** (Level 1): E-commerce site with AI-powered search
- **Agent-mediated with traditional components** (Level 2 hybrid): Research assistant that uses traditional databases
- **Mostly traditional, agent for specific workflows**: Accounting app where expense categorization is agent-mediated

Architecting's job is to recognize where on the spectrum an app sits, and whether MCP-native architecture serves the core use cases.

---

## 3. MCP Server Design Patterns

### The Protocol Foundation (January 2026)

MCP provides three primitives:
- **Tools**: Executable functions the agent can invoke
- **Resources**: Data sources the agent can read
- **Prompts**: Reusable templates for structuring interactions

Servers expose these to clients (hosts like Claude Desktop, IDEs, custom apps). The protocol handles lifecycle, capability negotiation, and transport (STDIO for local, HTTP for remote).

Key January 2026 capabilities:
- **Streamable HTTP**: Scalable remote deployment (Lambda, etc.)
- **OAuth 2.1**: Standard authentication
- **Elicitation**: Servers can request structured input from users
- **Tool Output Schemas**: Typed responses for better integration

### Server Composition Patterns

#### Pattern A: Monolithic Capability Server

Single server exposing all tools for a domain.

```
[Agent] → [Finance MCP Server]
              ├── log_expense()
              ├── categorize_transaction()
              ├── get_spending_summary()
              ├── check_budget_status()
              └── generate_report()
```

**When to use:**
- Coherent domain with shared state
- Tools frequently used together
- Single deployment/lifecycle makes sense

**Trade-offs:**
- Simple to deploy and manage
- All-or-nothing capability exposure
- Can become unwieldy as tool count grows

#### Pattern B: Micro-Servers (One Capability Each)

Separate server per capability or tightly-related capability group.

```
[Agent] → [Expense Logger MCP]     → log_expense()
        → [Categorizer MCP]        → categorize_transaction()
        → [Budget Checker MCP]     → check_budget_status()
        → [Report Generator MCP]   → generate_report()
```

**When to use:**
- Capabilities have different deployment needs
- Independent scaling required
- Different security/access profiles per capability

**Trade-offs:**
- Maximum flexibility
- Higher operational overhead
- More connection management

#### Pattern C: Pipeline/Chain

Servers arranged for sequential processing, output of one becomes input of next.

```
[Agent] → [Scraper MCP] → [Extractor MCP] → [Summarizer MCP] → [Critic MCP]
              ↓                  ↓                 ↓                ↓
          raw HTML          structured        summary          critique
                              data
```

**When to use:**
- Clear transformation stages
- Each stage is independently valuable
- Different models/capabilities per stage

**Trade-offs:**
- Highly composable
- Clear debugging at each stage
- Latency accumulates

#### Pattern D: Orchestrator + Workers

Central orchestrator delegates to specialized worker servers.

```
                    [Orchestrator MCP]
                    /       |        \
        [Research MCP]  [Draft MCP]  [Edit MCP]
```

**When to use:**
- Complex workflows with branching
- Dynamic task routing based on content
- Specialist capabilities that shouldn't know about each other

**Trade-offs:**
- Flexible workflow composition
- Orchestrator becomes coordination bottleneck
- More complex debugging

#### Pattern E: Hybrid (MCP + Traditional)

MCP servers for judgment-work, traditional services for deterministic operations.

```
[Agent] → [Intent Parser MCP]     ← judgment
        → [Traditional REST API]   ← deterministic CRUD
        → [Validator MCP]          ← judgment
```

**When to use:**
- Some operations genuinely don't need agent judgment
- Existing services to integrate
- Performance-critical paths need determinism

**Trade-offs:**
- Best of both worlds
- More architectural complexity
- Clear boundaries required

### Tool Granularity Guidance

From MCP best practices and emerging patterns:

| Principle | Rationale |
|-----------|-----------|
| **Fine-grained, single-purpose** | Easier for agent to understand and select |
| **Clear input/output schemas** | Agent knows what to provide, what to expect |
| **Descriptive names and docs** | Agent's "UI" is the tool description |
| **Group via parameters, not separate tools** | `search(type="users")` vs. `search_users()` |
| **Human-in-the-loop for sensitive ops** | Trust/safety requirement |

**The "too micro" question:** When is a task too small for an MCP tool?

Rule of thumb: If the operation is a pure function with no I/O, no external calls, and no judgment — it probably doesn't need to be an MCP tool. The agent can just reason about it. MCP tools are for **capabilities that interact with the world**.

---

## 4. The Handoff Contract

### What Architecting Produces → What MCP-Building Receives

When architecting identifies an MCP-native candidate, it needs to hand off:

#### Required Context

| Element | Description |
|---------|-------------|
| **Intent summary** | What the app is trying to accomplish (from brief) |
| **Capability domains** | What areas the agent needs to act in |
| **Core workflows** | Key sequences of operations |
| **Integration points** | External services, databases, APIs |
| **User interaction model** | How users will invoke the agent |

#### Architectural Decisions Already Made

| Decision | Options Architecting Resolves |
|----------|-------------------------------|
| **Agent level** | 1 (feature), 2 (core), or hybrid |
| **Server topology** | Monolithic, micro, pipeline, orchestrator, hybrid |
| **Deployment context** | Local (STDIO), remote (HTTP), or mixed |
| **State requirements** | Stateless tools vs. session-aware |
| **Security model** | Auth approach, trust boundaries |

#### What MCP-Building Decides

| Decision | MCP-Building's Domain |
|----------|----------------------|
| **Tool definitions** | Names, schemas, descriptions |
| **Resource exposure** | What data to make available |
| **Prompt templates** | Reusable interaction patterns |
| **Implementation** | Python code using SDK |
| **Testing strategy** | How to verify capabilities work |

### The Interface Document

A structured handoff might look like:

```yaml
mcp_architecture_context:
  intent: "Personal finance tracker with conversational interface"

  agent_level: 2  # Agent as core

  capability_domains:
    - expense_tracking
    - budget_management
    - spending_analysis

  server_topology: monolithic  # Single domain, shared state

  deployment: local_stdio  # Personal tool, runs locally

  core_workflows:
    - name: log_expense
      trigger: "User mentions spending money"
      steps: [parse_amount, categorize, store, confirm]

    - name: check_budget
      trigger: "User asks about spending status"
      steps: [aggregate_period, compare_budget, summarize]

  integrations:
    - type: database
      system: sqlite
      purpose: expense storage

  interaction_model: conversational

  security:
    - local_only: true
    - no_external_api_keys: true
```

---

## 5. Open Questions (The Edge of the Map)

These are genuinely unresolved — not gaps in our research, but frontiers in the field:

### Tool Design Questions

- **How "smart" should tools be?** Should a tool just fetch data, or should it do light processing? Where's the line between tool logic and agent reasoning?

- **Tool composition**: Can/should tools call other tools? Or should all orchestration go through the agent?

- **Dynamic tool generation**: Level 3 (agent builds tools) — is this practical? When?

### Architectural Questions

- **State management**: MCP is stateful at the session level. What about cross-session state? Who owns persistence — the tool or a separate layer?

- **Error recovery**: When a multi-tool workflow fails partway through, who handles rollback? The agent? An orchestrator? The tools themselves?

- **Observability**: How do you debug an agent-mediated app? Traditional logging doesn't capture the judgment layer.

### Economic Questions

- **Cost modeling**: Agent-mediated apps have different cost profiles (LLM calls). How does this change architectural decisions?

- **Latency budgets**: When is agent overhead acceptable? Real-time vs. batch?

### Ecosystem Questions

- **MCP server reuse**: Thousands of servers exist. When to use off-the-shelf vs. build bespoke?

- **Framework choice**: MCP-native vs. adapted frameworks — how does this affect skill guidance?

- **Protocol evolution**: MCP is evolving. How do we build skills that don't become obsolete?

---

## 6. References & Sources

### Official Documentation
- [MCP Architecture](https://modelcontextprotocol.io/docs/concepts/architecture)
- [MCP Tools](https://modelcontextprotocol.io/docs/concepts/tools)
- [MCP Roadmap](https://modelcontextprotocol.io/development/roadmap)

### Orchestration Patterns
- [Advanced MCP: Agent Orchestration, Chaining, and Handoffs](https://www.getknit.dev/blog/advanced-mcp-agent-orchestration-chaining-and-handoffs)
- [Orchestrating Multiple MCP Servers in a Single AI Workflow](https://portkey.ai/blog/orchestrating-multiple-mcp-servers-in-a-single-ai-workflow/)
- [mcp-agent Framework](https://github.com/lastmile-ai/mcp-agent)

### Agent Architecture
- [Building Effective AI Agents - Anthropic](https://www.anthropic.com/research/building-effective-agents)
- [Choose a Design Pattern for Agentic AI - Google Cloud](https://docs.cloud.google.com/architecture/choose-design-pattern-agentic-ai-system)
- [MCP-Native vs Traditional Approaches](https://dev.to/hani__8725b7a/agentic-ai-frameworks-comparison-2025-mcp-agent-langgraph-ag2-pydanticai-crewai-h40)

### Industry Context
- [Top 10 MCP Use Cases 2025](https://www.iamdave.ai/blog/top-10-model-context-protocol-use-cases-complete-guide-for-2025/)
- [MCP Enterprise Adoption Guide](https://guptadeepak.com/the-complete-guide-to-model-context-protocol-mcp-enterprise-adoption-market-trends-and-implementation-strategies/)
- [Current State of MCP - Elasticsearch Labs](https://www.elastic.co/search-labs/blog/mcp-current-state)

### Conceptual Framing
- Dan Shipper, "Agent Computer Interfaces" (Every, 2025)
- User-provided examples: Home Infrastructure, Reading List, Finance, Calendar, Writing Projects

---

*This document captures the known landscape as of January 2026. It is intended as a foundation for briefing the architecting skill evolution and the new mcp-building skill.*
