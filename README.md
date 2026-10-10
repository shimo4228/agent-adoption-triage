# agent-adoption-triage

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/agent-adoption-triage)

An [Agent Skill](https://agentskills.io/specification) holding a **five-question triage for routing AI work to the right architecture** (script, algorithmic search, LLM workflow, or autonomous agentic loop) *before* any agent is built. It is for anyone who hears "let's build an agent for X" and has to decide, or review, whether the use case actually needs an autonomous agent. The core inversion: the question is not whether an agent can do the work (it usually can), but whether **accountability can still be distributed after you deploy it**, that is, whether a failure can still be traced to someone who answers for it. Route the work first; pick the architecture second.

The skill is one Markdown file of guidance: it runs no code, makes no network calls of its own, and needs no API key; anything that leaves your machine goes out through your agent's usual model calls.

## Install

```bash
git clone https://github.com/shimo4228/agent-adoption-triage.git
mkdir -p ~/.claude/skills
cp -r agent-adoption-triage/skills/agent-adoption-triage ~/.claude/skills/
```

Claude Code then picks the skill up when someone proposes building an agent, reviews an agent deployment, or is choosing between a pre-defined LLM workflow and a runtime-directed loop; you can also call it by name with `/agent-adoption-triage`. Other agents that read the Agent Skills format take the same folder in their own skills directory.

## The five questions

Run them in order for each piece of work. Each path places the work in one of four quadrants (the Business AI Quadrants), which classify **the work, not the agent**. Work that reaches Q4 is Autonomous Agentic Loop work either way; a Q4 "No" keeps it there but ends the triage at **do not deploy**.

| # | Question | Routes to |
|---|----------|-----------|
| Q0 | Design phase or operation phase? | Either answer → Q1; an operation-phase answer adds the Phase-crossing decision at Q4 |
| Q1 | Can the work be expressed as deterministic rules? | Yes → Q2 / No → Q3 |
| Q2 | Is the search space static and finite? | Yes → Script Quadrant / No → Algorithmic Search Quadrant |
| Q3 | Can you draw the workflow on a whiteboard before running it? | Yes → LLM Workflow Quadrant / No → Q4 |
| Q4 | Can a pre-named gap-bearer be identified (and, if operation-phase, is the Phase-crossing decision recorded)? | Yes → Autonomous Agentic Loop Quadrant / No → **do not deploy** |

Choosing the Autonomous Agentic Loop Quadrant commits the organization to a non-removable **attribution gap**: once model output, tool choice and context blend at runtime, no one can separate their contributions after a failure. So Q4 asks for a **gap-bearer**: a specific person, a role with formal succession, or a contractual party who accepts at deployment time, not after an incident, that failures of this work land on them. Q0 does not branch; it records whether the work is still being designed or already runs in day-to-day operation. A loop that runs in operation also needs a recorded **Phase-crossing decision**: when a new pattern appears, the loop either handles it in place or routes it back to design. Either answer is admissible; leaving it open is not.

## What a triage produces

A completed triage produces a short record kept with the deployment decision. Here is one filled in by hand, as an illustration, for a case from the skill's anti-patterns list: a support-ticket classifier first proposed as an agent.

```text
Work item:        classify incoming support tickets against the FAQ
Phase (Q0):       operation
Q1 deterministic: no   Q2 static-finite: (skipped)   Q3 drawable: yes
Quadrant:         LLM Workflow
Governance set:   LLM Workflow controls, e.g. one narrow call per role with a schema,
                  approval gate on prompt and schema changes, every call logged
Re-run trigger:   tickets start needing steps that cannot be drawn in advance
```

Q3 is yes, so the work never reaches Q4 and needs no gap-bearer; the skill calls building it as an agent "bounded work on an autonomous loop".

## What else is inside

- Per-quadrant governance sets: required controls, and who a failure is traced back to (in the LLM Workflow Quadrant the audit log leads to the failing component and its owner; in the Autonomous Agentic Loop Quadrant that trace is foreclosed and responsibility ends at the gap-bearer)
- Hybrid architecture guidance (Plan-and-Execute, router agents, tiered handoff where novel cases escalate from a workflow to a gated loop): run the tree on each portion
- A 10-item anti-patterns checklist (e.g. "bounded work on an autonomous loop", "approval gate as runtime checkbox", "audit log as sample") with recovery paths
- A one-page triage record template to keep with the deployment decision

## More from the author

- **[Where ReAct Agents Are Actually Needed in Business](https://dev.to/shimo4228/where-react-agents-are-actually-needed-in-business-33do)** ([日本語](https://zenn.dev/shimo4228/articles/react-agent-business-quadrant)): the essay that first laid out the four quadrants this triage routes to, and why most LLM applications belong in the LLM Workflow Quadrant rather than an autonomous loop.
- **[Is ReAct Needed in Production? — Separating Design and Operation Phases](https://dev.to/shimo4228/is-react-needed-in-production-separating-design-and-operation-phases-4ll0)** ([日本語](https://zenn.dev/shimo4228/articles/react-agent-business-quadrant-3)): the argument behind Q0, that design and operation optimize for opposite properties (flexibility vs predictability), which is why the triage asks for the phase first.
- **[Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice)**: the research repository this skill is curated from by hand; its design records ADR-0009 (Triage Before Autonomy) and ADR-0010 (Phase Separation) explain why the triage works this way.
- **[code-and-llm-collaboration](https://github.com/shimo4228/code-and-llm-collaboration)**: four patterns for layering deterministic code and LLM calls in one pipeline (guard, filter, judge + enforce, orchestrator + worker), for work this triage routes to the LLM Workflow Quadrant.
- **[llm-agent-security-principles](https://github.com/shimo4228/llm-agent-security-principles)**: structural security for autonomous agents (remove the capability instead of guarding it, treat accumulated content as untrusted, one external adapter per agent process); the installable form of AAP ADR-0001 to ADR-0004.
- **[agent-observability-patterns](https://github.com/shimo4228/agent-observability-patterns)**: three skills (replayable audit logs, read-only instruments, shadow-mode validation) for observing an agent before you intervene; the installable form of AAP ADR-0006 and ADR-0005.
- **[shimo4228](https://github.com/shimo4228/shimo4228)**: the author's hub, with AAP next to the other long-running practice lines and their DOIs.

## License

MIT

<details>
<summary>For tools and AI assistants</summary>

agent-adoption-triage is an Agent Skill (one Markdown instruction file in the open Agent Skills format) that gives a coding agent a five-question triage for routing a piece of AI work to one of four architectures before anything is built, for engineers, architects and reviewers deciding whether a use case actually needs an autonomous agent. The four architectures are the Business AI Quadrants: the Script (1), Algorithmic Search (2), LLM Workflow (3) and Autonomous Agentic Loop (4) Quadrants.

It exists because most "should we build an agent?" conversations start from the architecture and work backwards to the justification. The skill inverts that: the question is not whether a model can do the work (it usually can) but whether accountability can still be distributed after deployment, and that depends on which quadrant the work belongs to. Choosing the Autonomous Agentic Loop Quadrant commits the organization to an attribution gap that cannot be removed, because the contributions of model output, tool choice and context can no longer be separated after the fact; so Q4 requires a named gap-bearer at deployment time, and an operation-phase loop also needs a recorded Phase-crossing decision. When the gap-bearer, or a required Phase-crossing decision, cannot be named, the triage ends at "do not deploy": the work stays in the Autonomous Agentic Loop Quadrant, but it should not be deployed.

Canonical facts: MIT license; a single `SKILL.md` with no code; maintained by one author (@shimo4228). Status: curated by hand, with no sync script; it is the installable publication of the `docs/quadrants/` decision tree and governance docs in Agent Attribution Practice. Requirements: Claude Code or another agent that reads Agent Skills; no API key and no network access of its own. Beyond the five questions (Q0 design or operation phase, Q1 deterministic rules, Q2 static and finite search space, Q3 workflow drawable in advance, Q4 gap-bearer and Phase-crossing decision), the skill holds per-quadrant governance sets naming who each failure is traced back to, hybrid-architecture guidance (run the tree per portion), a 10-item anti-patterns checklist with recovery paths, and a triage record template.

Example: a completed triage is a short record kept with the deployment decision. It lists the work item, the phase (Q0), the Q1 to Q3 answers, the quadrant, and for Quadrant 4 the named gap-bearer and, for an operation-phase loop, the Phase-crossing decision (handle new patterns in place, or route them back to design), followed by the governance set applied and what change would trigger a re-run. For operation-phase work the skill names a default composition, the Script and LLM Workflow Quadrants (1 + 3), with the Algorithmic Search Quadrant (2) added where it applies, as a default rather than a requirement; so "operation phase, Script + LLM Workflow composition" is an acceptable answer for the phase and quadrant fields, while "we didn't decide" is not.

Links: [skills/agent-adoption-triage/SKILL.md](skills/agent-adoption-triage/SKILL.md) is the skill itself; [llms.txt](llms.txt) and [llms-full.txt](llms-full.txt) are the machine-readable summary and reference. The skill is the "how" counterpart to [AAP ADR-0009 (Triage Before Autonomy)](https://github.com/shimo4228/agent-attribution-practice/blob/main/docs/adr/0009-triage-before-autonomy.md) and [ADR-0010 (Phase Separation)](https://github.com/shimo4228/agent-attribution-practice/blob/main/docs/adr/0010-phase-separation.md) in [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice), concept DOI [10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013); cite AAP by that DOI.

</details>
