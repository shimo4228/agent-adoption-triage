# agent-adoption-triage

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/agent-adoption-triage) [![GitMCP](https://img.shields.io/endpoint?url=https://gitmcp.io/badge/shimo4228/agent-adoption-triage)](https://gitmcp.io/shimo4228/agent-adoption-triage)

An [Agent Skill](https://agentskills.io/specification) holding a **five-question triage for routing AI work to the right architecture** — script, algorithmic search, LLM workflow, or autonomous agentic loop — *before* any agent is built. The core inversion: the question is not whether an agent can do the work (it usually can), but whether **accountability can still be distributed after you deploy it**. Route the work first; pick the architecture second.

## Install

### Claude Code

```bash
# Copy into your global skills directory
cp -r skills/agent-adoption-triage ~/.claude/skills/agent-adoption-triage
```

### SkillsMP

```bash
/skills add shimo4228/agent-adoption-triage
```

## The five questions

| # | Question | Routes to |
|---|----------|-----------|
| Q0 | Design phase or operation phase? | Surfaces the Phase-crossing decision when it is load-bearing |
| Q1 | Can the work be expressed as deterministic rules? | No → an LLM is in the loop somewhere |
| Q2 | Is the search space static and finite? | Yes → Script Quadrant / No → Algorithmic Search Quadrant |
| Q3 | Can you draw the workflow on a whiteboard before running it? | Yes → LLM Workflow Quadrant |
| Q4 | Can a pre-named gap-bearer be identified (and, if operation-phase, is the Phase-crossing decision recorded)? | Yes → Autonomous Agentic Loop Quadrant / No → **do not deploy** |

The four quadrants (Business AI Quadrants) classify **the work, not the agent**: deterministic × semantic-judgment on one axis, pre-defined × exploratory control flow on the other. Choosing the Autonomous Agentic Loop Quadrant commits the organization to a non-removable **attribution gap** — which is why Q4 demands a named party who absorbs it at deployment time, not post-incident.

## What Else Is Inside

- Per-quadrant governance sets: required controls and the redirect path when something fails (component-level redirect works in the LLM Workflow Quadrant; in the Autonomous Agentic Loop Quadrant it is foreclosed and terminates at the gap-bearer)
- Hybrid architecture guidance (Plan-and-Execute, router agents, tiered handoff) — run the tree per portion
- A 10-item anti-patterns checklist (e.g. "bounded work on an autonomous loop", "approval gate as runtime checkbox", "audit log as sample") with recovery paths
- A one-page triage record template to keep with the deployment decision

## When It Triggers

- Someone says "let's build an agent for X"
- Deciding whether a use case actually needs an autonomous agent
- Reviewing an agent deployment proposal, or auditing an existing agent for accountability risk
- Choosing between a pre-defined LLM workflow and a runtime-directed loop

## Curation model

This repository is **manually curated**, not script-synced: it is the installable publication of the adoption navigator maintained in the [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice) research repository (`docs/quadrants/`). Improvements flow here editorially, the same way the paired ADRs were extracted.

## About this skill

This skill is a design-pattern skill from the [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice) research line — harness-neutral ADRs on accountability distribution for agent systems ([DOI 10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013)). It is the "how" counterpart to [AAP ADR-0009 (Triage Before Autonomy) and ADR-0010 (Phase Separation)](https://github.com/shimo4228/agent-attribution-practice/tree/main/docs/adr) and the [`docs/quadrants/`](https://github.com/shimo4228/agent-attribution-practice/tree/main/docs/quadrants) navigator. Sibling skill from the same research line: [llm-agent-security-principles](https://github.com/shimo4228/llm-agent-security-principles) (the "how" counterpart to AAP ADR-0001..0004). AAP is one of three research lines by [@shimo4228](https://github.com/shimo4228), alongside the [Agent Knowledge Cycle (AKC)](https://github.com/shimo4228/agent-knowledge-cycle) ([DOI 10.5281/zenodo.19200726](https://doi.org/10.5281/zenodo.19200726)) — a six-phase bidirectional growth loop for agent-operator intent alignment — and [Contemplative Agent](https://github.com/shimo4228/contemplative-agent) ([DOI 10.5281/zenodo.19212118](https://doi.org/10.5281/zenodo.19212118)) — autonomous agents grounded in four contemplative axioms.

## License

MIT
