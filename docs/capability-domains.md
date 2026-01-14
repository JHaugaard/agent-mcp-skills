# Capability Domains: An Organizing Principle for Agent-Mediated Architecture

**Status:** Working paper
**Date:** January 2026
**Context:** Emerged from brainstorming the mcp-building and architecting skills

---

## The Term

The phrase "capability domains" appeared naturally in the landscape document's handoff contract example:

```yaml
capability_domains:
  - expense_tracking
  - budget_management
  - spending_analysis
```

On examination, this seemingly simple term sits at the intersection of three established fields, each contributing a distinct meaning of "capability."

---

## Three Lineages

### 1. Enterprise Architecture: Capability Mapping

In business architecture, a **capability** is "what a company must do to generate value" — abstracted above *how* it's done.

Key properties:
- Capabilities belong to **domains** (coherent business areas)
- They're modeled independent of the processes that realize them
- They provide a stable vocabulary that survives organizational change
- They bridge strategy and implementation

A capability map answers: "What do we need to be able to do?" — without prescribing how.

### 2. Security: Object-Capability Model

In computer security (dating to 1966), a **capability** is an unforgeable token of authority — the right to perform specific operations on a specific object.

Key properties:
- Possession of the capability *is* the authorization
- Capabilities can be delegated and attenuated, but not forged
- No ambient authority — you can only do what you hold tokens for
- The question isn't "who are you?" but "what can you do?"

This model underpins least-privilege security: grant the minimum capabilities needed.

### 3. AI Agents: Tool Capabilities

In the MCP/agent world, capabilities are what the agent *can do* — the tools exposed to it.

Key properties:
- Tools define the agent's action space
- MCP permissions apply least-privilege: agents shouldn't have more rights than the user they serve
- Capabilities are the boundary between reasoning and acting

---

## The Synthesis

A **capability domain** for agent-mediated applications fuses these three meanings:

> A coherent area of agent action, characterized by shared vocabulary, related permissions, and tools that frequently compose together to accomplish user intent within that area.

| Dimension | What the Domain Provides |
|-----------|--------------------------|
| **Business Architecture** | A coherent area of value-generating activity |
| **Security/Authorization** | A scope for permissions — tools share a trust boundary |
| **Agent Action Space** | A cluster of related tools the agent can invoke |
| **DDD Bounded Context** | A semantic boundary where terms have consistent meaning |

---

## Properties of Capability Domains

### Cohesion
Tools within a domain share:
- **Vocabulary** — terms mean the same thing
- **State** — they may access common data structures
- **Trust level** — similar security profiles
- **Lifecycle** — they evolve together

### Separation
Domains are distinct from each other:
- Cross-domain operations are explicit integration points
- Different domains may have different permission models
- A domain can be deployed, tested, and evolved independently

### Hierarchy
Domains can nest:
- "Finance" might contain "expense_tracking" and "investment_tracking"
- Granularity depends on the application's complexity

---

## Implications for Architecture

### For the Architecting Skill

Identifying capability domains becomes an early, load-bearing task:

- What domains does this application span?
- Are they separable (suggesting micro-servers) or tightly coupled (monolithic)?
- Which domains require judgment vs. procedure?
- Do domains have different trust/security profiles?

### For the MCP-Building Skill

Capability domains become structural backbone:

- One server per domain? Or domains as modules within a server?
- Tools within a domain share resources, state, vocabulary
- Cross-domain operations become explicit interfaces
- Domain = natural unit for testing, deployment, permissioning

### For the Handoff Contract

The `capability_domains` section becomes richly specified:

```yaml
capability_domains:
  expense_tracking:
    description: "Recording, categorizing, storing financial transactions"
    trust_level: high  # handles financial data
    state_requirements: persistent
    external_integrations: [bank_api, receipt_ocr]

  spending_analysis:
    description: "Aggregating, summarizing, forecasting spend patterns"
    trust_level: medium  # read-only derived data
    state_requirements: stateless  # computed from expense_tracking
    external_integrations: []
```

---

## Capability Domain Discovery

A structured process for eliciting domains from a project brief:

1. **Identify verbs** — What actions does the user want to take?
2. **Cluster by coherence** — Which actions share vocabulary, state, trust?
3. **Name the clusters** — Each cluster is a candidate domain
4. **Check boundaries** — Are the domains truly separable? Where do they interact?
5. **Assess judgment requirements** — Which domains need agent reasoning vs. deterministic execution?

---

## Relationship to Domain-Driven Design

Capability domains align with DDD's **bounded contexts**:

- Each domain has a ubiquitous language
- Cross-domain communication is explicit (context mapping)
- The domain model is valid within its boundary

The difference: DDD focuses on *modeling the problem space*; capability domains focus on *what the agent can do about it*. They're complementary views of the same territory.

---

## Open Questions

1. **Domain evolution** — How do domains grow, split, merge over time? When does a single domain become two?

2. **Cross-domain orchestration** — When a workflow spans domains, who orchestrates? The agent? An orchestrator server? The user?

3. **Domain templates** — Can we provide reusable domain patterns (e.g., "CRUD domain," "analysis domain," "integration domain")?

4. **Dynamic domains** — If agents can spawn MCP servers on demand, can they spawn new domains? What are the constraints?

---

## Summary

"Capability domains" may not be novel terminology, but applying it as the **organizing unit for agent-mediated app architecture** — bridging business capability, security scope, bounded context, and tool clustering — provides useful scaffolding for both the architecting and mcp-building skills.

The domain answers: *What coherent area of action is this, and what can the agent do within it?*

---

## References

- [Business Capability Map: The Essential Guide](https://www.ardoq.com/knowledge-hub/business-capability-map)
- [TOGAF Core Concepts - Architecture Domains](https://pubs.opengroup.org/architecture/togaf9-doc/arch/chap02.html)
- [Business Capability Modelling | The Essential Project](https://enterprise-architecture.org/university/business-capability-modelling/)
- [Capability-based security - Wikipedia](https://en.wikipedia.org/wiki/Capability-based_security)
- [Object-capability model - Wikipedia](https://en.wikipedia.org/wiki/Object-capability_model)
- [MCP Permissions: Securing AI Agent Access to Tools](https://www.cerbos.dev/blog/mcp-permissions-securing-ai-agent-access-to-tools)
- [Domain-Driven Modernization - IBM](https://www.ibm.com/think/insights/domain-driven-modernization-of-enterprises-to-a-composable-it-ecosystem-part-1)
