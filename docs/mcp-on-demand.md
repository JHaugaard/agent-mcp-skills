# MCP On Demand: Agents That Build Their Own Tools

**Status:** Working paper
**Date:** January 2026
**Context:** Exploring Level 3 agent architecture — agents that create their own capabilities

---

## The Question

Can an agent spawn an MCP server on demand when existing tools are inadequate for the required task?

**The argument:**
- MCP servers are just code
- Agents can write code
- SDK primitives can reside in accessible libraries
- Therefore: agents can write MCP servers

This paper examines whether this is practical, how it's emerging, and what it means for agent architecture.

---

## The Progression

Agent capability has evolved through distinct levels:

```
Level 1: Agent calls predefined tools
         ↓
Level 2: Agent writes code that composes predefined tools
         ↓
Level 3: Agent writes code that creates new tools from primitives
         ↓
Level 4: Agent spawns new MCP servers on demand
```

Most current agent systems operate at Level 1 or 2. The question is whether Level 3-4 is practical — and if so, when.

---

## Evidence: Code-Mode Patterns

Two recent developments suggest the infrastructure is crystallizing.

### Anthropic: Code Execution with MCP

Anthropic's approach presents MCP tools as **code APIs** rather than direct function calls:

- Tools organized in a filesystem structure (`./servers/google-drive/`, etc.)
- Agent explores the filesystem to find available servers
- Agent writes TypeScript/JavaScript to interact with tools
- **Key capability:** Agents can develop and persist reusable functions as "skills"

> "Over time, agents build a toolbox of higher-level capabilities."

The pattern: agents save working implementations to a `./skills/` directory. These persist across sessions and become part of the agent's expanding toolkit.

**Result:** 98.7% reduction in token consumption compared to exposing all tools directly.

### Cloudflare: Code Mode

Cloudflare's approach auto-converts MCP tools to TypeScript definitions:

- Schema fetched from MCP server → TypeScript API generated
- Agent receives one meta-tool: "execute TypeScript in sandbox"
- LLM writes code that calls the generated APIs
- Execution happens in V8 isolates (lightweight, secure)

> "LLMs are better at writing code to call MCP, than at calling MCP directly."

The insight: code is a more natural interface for complex tool composition than structured function calls.

---

## The Minimal Viable MCP Server

With FastMCP, a functional MCP server is trivially small:

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("dynamic-server")

@mcp.tool()
def new_capability(input: str) -> str:
    """Dynamically created capability"""
    return process(input)

if __name__ == "__main__":
    mcp.run()
```

If an agent can write Python, and the SDK is accessible, the agent can:

1. Identify a capability gap
2. Write MCP server code using SDK primitives
3. Spin up the server (locally or in a sandbox)
4. Register it with the host
5. Use it immediately

The gap between "writes code that uses tools" and "writes code that is tools" is surprisingly small.

---

## What Makes This Feasible Now

| Enabler | Why It Matters |
|---------|----------------|
| **FastMCP / SDK simplicity** | Server creation is ~10 lines of boilerplate |
| **Code sandboxing** | V8 isolates, containers provide safe execution |
| **TypeScript/Python fluency** | Agents are highly capable in these languages |
| **MCP dynamic discovery** | Protocol supports runtime server registration |
| **Code-mode patterns** | Agent already writes code to orchestrate tools |

---

## The Capability Domain Connection

Dynamic MCP generation becomes more tractable when scoped to capability domains:

```
Agent encounters task in "expense_tracking" domain
  → Existing tools insufficient
  → Agent examines domain primitives (SDK, base classes, schemas)
  → Agent generates a new tool within the domain's trust boundary
  → Tool inherits domain's permissions, vocabulary, state access
```

The domain provides:
- **Guardrails** — what the generated tool is allowed to do
- **Vocabulary** — what inputs/outputs should look like
- **Context** — shared state it can access
- **Trust boundary** — permissions it inherits

Without domain scoping, dynamic tool generation is unconstrained and risky. With it, the agent operates within known boundaries.

---

## Patterns for On-Demand MCP

### Pattern 1: Skill Crystallization

Agent solves a novel problem by composing existing tools. The solution works. Agent saves the composition as a reusable skill/tool for future use.

**Characteristics:**
- Incremental capability growth
- Human-reviewable before promotion to "official" tool
- Low risk — just codifying what already worked

### Pattern 2: Template Instantiation

Agent receives a domain template (base class, schema) and generates a specialized instance.

```python
# Domain template
class ExpenseToolTemplate:
    domain = "expense_tracking"
    allowed_operations = ["read", "write", "categorize"]

# Agent generates
class ReceiptParserTool(ExpenseToolTemplate):
    @mcp.tool()
    def parse_receipt(self, image_path: str) -> Expense:
        # Agent-generated implementation
        ...
```

**Characteristics:**
- Constrained generation
- Inherits domain properties
- More predictable than unconstrained generation

### Pattern 3: Meta-Tool

A standard tool whose purpose is to create other tools:

```python
@mcp.tool()
def spawn_capability(
    name: str,
    description: str,
    implementation: str,  # Code as string
    domain: str
) -> ToolRegistration:
    """Create a new MCP tool at runtime"""
    # Validate against domain constraints
    # Sandbox the implementation
    # Register with host
    # Return handle
```

**Characteristics:**
- Explicit capability creation
- Can enforce domain constraints at creation time
- Auditable — all spawns go through this interface

---

## Trust and Safety Considerations

Dynamic tool generation raises legitimate concerns:

| Concern | Mitigation |
|---------|------------|
| **Malicious code** | Sandbox execution (V8 isolates, containers) |
| **Privilege escalation** | Inherit permissions from spawning context, not escalate |
| **Persistence of bad tools** | Human review before promotion; session-scoped by default |
| **Resource exhaustion** | Rate limits on tool spawning; resource quotas per sandbox |
| **Auditability** | Log all generated code; version control for persisted tools |

The key principle: **generated tools should never have more authority than the agent that created them**, which itself has no more authority than the user it serves.

---

## Implications for Skills

### For mcp-building

The skill might need to support two modes:

1. **Human-guided construction** — Skill helps a human design and build MCP servers through structured workflow

2. **Agent-guided construction** — Skill provides primitives, templates, and scaffolding that an agent can use to generate servers at runtime

The second mode is more ambitious but potentially more aligned with where agent architecture is heading.

### For architecting

When assessing an application, consider:

- Does this app need dynamic capability generation?
- If so, what domains should support it?
- What templates/constraints should govern generation?
- What's the trust model for agent-created tools?

---

## The n8n Parallel

This connects to the earlier n8n discussion. n8n allows visual construction of workflows that can be exposed as MCP tools. The parallel:

| n8n Approach | On-Demand MCP Approach |
|--------------|------------------------|
| Human builds workflow visually | Agent builds tool programmatically |
| Workflow persists in n8n | Tool persists in skills directory |
| Exposed via n8n's MCP server | Exposed via spawned MCP server |
| Debugging via n8n UI | Debugging via execution logs |

Both are "capability crystallization" — the difference is who does the crystallizing.

**Learning from n8n:**
- Visual debugging of multi-step flows — what would this look like for agent-generated tools?
- Retry/error handling patterns — battle-tested at scale
- Execution history & observability — being able to replay and inspect

These patterns are worth adopting even when the human isn't the one building the tool.

---

## Open Questions

1. **Persistence scope** — Should generated tools be session-scoped (ephemeral) or persist for future sessions? Who decides?

2. **Promotion path** — How does a generated tool become a "first-class" tool? Human review? Demonstrated reliability over N uses?

3. **Discoverability** — If an agent generates a useful tool, how do other agents (or the same agent in future sessions) find it?

4. **Versioning** — How do we handle updates to generated tools? What if the generator improves?

5. **Composition** — Can a generated tool use other generated tools? How deep can this go?

---

## Summary

The question "Can an agent spawn MCP servers on demand?" has a clear answer: **yes, and the architecture for this is crystallizing now.**

The key enablers:
- Lightweight SDKs that make server creation trivial
- Code-mode patterns that have agents writing tool-calling code
- Sandboxing that makes execution of generated code safe
- Capability domains that provide scaffolding and constraints

The skill implications:
- mcp-building should consider both human-guided and agent-guided construction
- Architecting should recognize when dynamic capability generation is appropriate
- Domain templates become important as scaffolding for constrained generation

This is Level 3 of agent integration: **agents that build their own tools**. It's no longer theoretical.

---

## References

- [Code Execution with MCP - Anthropic](https://www.anthropic.com/engineering/code-execution-with-mcp)
- [Code Mode - Cloudflare](https://blog.cloudflare.com/code-mode/)
- [Microsoft Agent Framework](https://devblogs.microsoft.com/foundry/introducing-microsoft-agent-framework-the-open-source-engine-for-agentic-ai-apps/)
- [Agentic AI Frameworks 2026 - Exabeam](https://www.exabeam.com/explainers/agentic-ai/agentic-ai-frameworks-key-components-top-8-options/)
- [LLM Agents | Prompt Engineering Guide](https://www.promptingguide.ai/research/llm-agents)
