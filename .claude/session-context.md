# Session Context

## Current Focus
Brainstorming the **mcp-building skill** — a new workflow skill for designing and constructing bespoke MCP servers as core architectural components of agent-mediated applications.

## MCP Servers for This Session
None — pure discussion/planning session

## MCP Servers Added This Session
| Server | Tools | Status |
|--------|-------|--------|
| (none) | - | - |

## Key Decisions
- MCP servers are not "thin glue" — they're robust middleware, potentially the entire logic layer between thin UI and backend
- The mcp-building skill represents a new architectural paradigm, not just a construction helper
- The architecting skill needs to learn to recognize when MCP-native architecture is appropriate

## Session Progress

### 2026-01-14 ~afternoon — Brainstorming Session

**Starting Point:**
- User wanted to build a skill for constructing MCP servers using the SDK and Python
- Loaded the brainstorming skill from `~/.claude/skills/workflow/brainstorming`

**Key Insight Emerged:**
The harder challenge isn't building the MCP server (SDK provides a path) — it's recognizing *when* an app should be agent-mediated in the first place.

**Reframed the Two Challenges:**

1. **Decision framework for architecting:** When to recognize "this is judgment-work that wants to be agent-mediated" — and flag the project for the MCP-native path.

2. **The mcp-building skill itself:** Given that the project *is* MCP-native, guide construction of bespoke MCP server(s) that embody the app's core capabilities.

**Recognition Pattern Developed:**

Primary signal: **The "How" is underspecified** — user speaks in intent, not procedure. The gap between intent and execution is where the agent lives.

Secondary signals:
- Natural language as interface
- Domain benefits from memory/context
- Multi-step orchestration required
- Tolerance for non-determinism
- External service choreography

Counter-signals (traditional architecture):
- Precise, repeatable transformations
- High-throughput, low-latency requirements
- Regulatory/audit requirements for determinism
- Rich interactive UI is the product
- The "how" is fully specified

**Architectural Decision Tree:**
```
Does the brief describe INTENT (not PROCEDURE)?
    │
    ├─ NO → Traditional architecture
    │
    └─ YES → Does accomplishing intent require:
                 ├─ Judgment/interpretation?
                 ├─ Multi-step orchestration?
                 ├─ Semantic understanding?
                 ├─ Context/memory across sessions?
                 └─ External service choreography?
                 │
                 └─ (Any YES) → MCP-native candidate
```

**Resources Consulted:**
- User's five MCP examples (home infra, reading list, finance, calendar, writing projects)
- Dan Shipper's "Agent Computer Interfaces" article
- Web research on MCP use cases, Anthropic's agent guidance, Google's agentic AI patterns

**Key Reframe — Interface Agnosticism:**
The MCP server doesn't care if requests come from browser chat, Claude Code terminal, Swift voice app, another agent, or a cron job. The capability layer is interface-agnostic. UI is just one of many skins over the durable core.

---

### 2026-01-14 ~evening — Landscape Document Completed

**Shift in Approach:**
- Recognized we needed to "map the territory" before briefing the skills
- Decided to produce a **landscape document** capturing the known world as of January 2026

**Research Conducted:**
- MCP official documentation (architecture, tools, roadmap)
- MCP composition/chaining patterns (pipeline, handoff, graph, orchestrator)
- Agent orchestration frameworks (mcp-agent, patterns from Anthropic, Google)
- MCP-native vs. traditional framework approaches
- Industry adoption status (OpenAI, Microsoft, Google, Linux Foundation)

**Landscape Document Produced:**
`agent-mcp-landscape-jan2026.md` — Six sections:

1. **The Paradigm Shift** — How agent-mediated apps invert traditional architecture; three levels of agent integration (feature, core, builder); the key insight about intent vs. execution

2. **The Decision Space for Architecting** — Primary signal (intent vs. procedure), secondary signals, counter-signals, spectrum not binary

3. **MCP Server Design Patterns** — Five patterns documented:
   - Monolithic capability server
   - Micro-servers (one capability each)
   - Pipeline/chain
   - Orchestrator + workers
   - Hybrid (MCP + traditional)
   - Plus tool granularity guidance

4. **The Handoff Contract** — What architecting produces → what mcp-building receives; draft interface structure (YAML example)

5. **Open Questions** — Genuine frontiers: tool smartness, state management, error recovery, cost modeling, ecosystem choices

6. **References & Sources** — All consulted materials properly linked

**Key Clarifications This Session:**
- Output should be **aspirational** (what we want skills to do) not **prescriptive** (how they do it)
- Two intertwined skills require coordinated briefing
- Landscape document provides shared foundation for both briefs

**Where We Are Now:**
- Landscape document complete and ready for review
- User moving project to top-level repo for proper git/GitHub setup
- Next: Review landscape, then move toward skill intent briefs

---

### 2026-01-14 ~late evening — Concept Mining

**Capability Domains:**
The term `capability_domains` from the landscape document's handoff example triggered deeper exploration. Found it sits at intersection of three lineages:
1. **Enterprise Architecture** — capability mapping (what business does, not how)
2. **Security** — object-capability model (unforgeable tokens of authority)
3. **AI Agents** — tool capabilities (agent's action space)

Synthesis: A **capability domain** is a coherent area of agent action, characterized by shared vocabulary, related permissions, and tools that frequently compose together.

Implications: Domains become the organizing unit for MCP server design — scoping permissions, state, vocabulary, and trust boundaries.

**MCP-On-Demand (Level 3 Architecture):**
Explored whether agents can spawn MCP servers dynamically when existing tools are inadequate.

Key sources:
- Anthropic's "Code Execution with MCP" — tools as code APIs, agents save implementations as reusable skills
- Cloudflare's "Code Mode" — MCP tools auto-converted to TypeScript, agent writes code in sandbox

Assessment: **Yes, feasible now.** The gap between "agent writes code that uses tools" and "agent writes code that IS tools" is small. FastMCP makes server creation ~10 lines.

Connection to capability domains: Dynamic tool generation becomes tractable when domain-scoped — the domain provides guardrails, vocabulary, and trust boundaries for generated tools.

**Working Papers Produced:**
- `capability-domains.md` — full synthesis of the concept
- `mcp-on-demand.md` — Level 3 architecture analysis

---

## Artifacts Produced

| File | Purpose |
|------|---------|
| `agent-mcp-landscape-jan2026.md` | Cartographic foundation for skill briefing |
| `capability-domains.md` | Concept paper: organizing principle for agent architecture |
| `mcp-on-demand.md` | Concept paper: agents spawning their own tools |
| `.claude/session-context.md` | Session state and progress tracking |

## Notes
- Session started: 2026-01-14
- Focus: Brainstorming/planning, no implementation
- Mode: Adaptive (conversational with structured questions at decision points)
- User prefers to think slowly; session may span multiple sittings

## Session Status
Completed: 2026-01-14 late evening
Servers cleaned: (none added)
Tool count: N/A (no MCP servers used)
