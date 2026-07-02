---
name: agent-adoption-triage
description: Five-question triage for routing AI work to the right architecture — script, algorithmic search, LLM workflow, or autonomous agentic loop — before any agent is built. Use when someone says "let's build an agent for X", when deciding whether a use case actually needs an autonomous agent, when reviewing an agent deployment proposal or an existing agent for accountability risk, or when choosing between a pre-defined LLM workflow and a runtime-directed loop. Covers the Business AI Quadrants, the design/operation Phase axis, gap-bearer naming, the Phase-crossing decision, per-quadrant governance requirements, and an anti-patterns checklist for recovering misrouted work.
compatibility: Developed and tested on Claude Code; portable to other Agent Skills-compatible agents.
user-invocable: true
origin: shimo4228
---

# Agent Adoption Triage

Most "should we build an agent?" conversations start from the architecture ("we'll use a ReAct loop") and work backwards to the justification. This skill inverts that: **route the work first, pick the architecture second.** The question is not whether an agent *can* do the work — current models usually can. The question is whether **accountability can still be distributed after you deploy it**, and that is decided by which quadrant the work belongs to, not by model capability.

This skill is the "how" counterpart to two ADRs and the quadrants navigator in the [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice) research line (concept DOI [10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013)):

- [AAP ADR-0009 Triage Before Autonomy](https://github.com/shimo4228/agent-attribution-practice/blob/main/docs/adr/0009-triage-before-autonomy.md) — the problem-space triage (Q1–Q4)
- [AAP ADR-0010 Phase Separation](https://github.com/shimo4228/agent-attribution-practice/blob/main/docs/adr/0010-phase-separation.md) — the design/operation Phase axis (Q0)
- [AAP `docs/quadrants/`](https://github.com/shimo4228/agent-attribution-practice/tree/main/docs/quadrants) — decision tree, governance mapping, case studies, anti-patterns

The ADRs cover the "why." This skill is the runnable version: walk the five questions, land on a quadrant, apply that quadrant's governance set, and record the answers.

---

## The Business AI Quadrants

Two axes classify any piece of work an organization considers giving to AI:

| | Pre-defined control flow | Exploratory control flow |
|---|---|---|
| **Deterministic** | (1) Script Quadrant | (2) Algorithmic Search Quadrant |
| **Semantic judgment** | (3) LLM Workflow Quadrant | (4) Autonomous Agentic Loop Quadrant |

- **Horizontal axis** — can the work be expressed as deterministic rules, or does it require semantic judgment (an LLM)?
- **Vertical axis** — is the control flow decided in advance by code and humans, or must the next step be decided at runtime in light of what was just observed?

The quadrants describe **the work, not the agent**. The same agent process can serve work in different quadrants, and each piece of work needs its own triage.

Key properties:

- **(1) Script** — scripts and pipelines, no LLM. Classical SE accountability applies.
- **(2) Algorithmic Search** — classical search, DP, MCTS, RL. Scheduling, routing, allocation. Solved by algorithms, not LLMs.
- **(3) LLM Workflow** — the execution path is decided in advance; the LLM is called as a single bounded step within it. Because each call has a pre-assigned role, its contribution is post-hoc separable: when something fails, **redirect succeeds** — you can walk the audit log to the failing component and its owner. This is the default for most current LLM applications, chronically misrouted to (4) because industry vocabulary lacks a positive name for it. Two sub-forms, same load-bearing property: *conversational* (chat assistants where the human is the judging agent) and *batch* (bounded LLM functions inside a deterministic pipeline).
- **(4) Autonomous Agentic Loop** — the LLM decides each next step at runtime (ReAct and descendants: Deep Research, exploratory coding agents, open-ended browsing). Legitimate when the work genuinely requires open-ended exploration. Choosing it commits the organization to a **non-removable attribution gap**: model output, tool selection, history reference, and prompt context blend at runtime, so post-hoc separation of "which judgment element caused the failure" is foreclosed. The gap cannot be engineered away — it can only be **absorbed by a pre-named party** (the gap-bearer).

**Phase is an independent third dimension.** Every quadrant can appear in the *design phase* (exploratory work, workflows being built, prototyping) or the *operation phase* (a deployed workflow running its known path, where predictability is the requirement). Coding agents and Deep Research tools are typically design-phase residents. The one placement with an extra rule is operation-phase Quadrant 4 — see Q4 Part B.

---

## The five questions

Run these in order for each piece of work. For hybrid systems, run them per portion.

### Q0. Design phase or operation phase?

- **Design phase** → continue to Q1–Q4; all quadrants are legitimate candidates.
- **Operation phase** → continue to Q1–Q4, but if the result is Quadrant 4, the Phase-crossing decision must be recorded explicitly (Q4 Part B). The empirical default for operation-phase workflows is a composition of Quadrants 1 + 3 (+ 2 where applicable) — a default, not a requirement.

### Q1. Can the work be expressed as deterministic rules?

Test: can two reasonable engineers produce identical outputs from the same input and specification, without consulting each other? If yes, no semantic judgment is required.

- **Yes** → Q2. **No** → Q3.

### Q2. Is the search space static and finite?

- **Yes** → **Script Quadrant.** Use a script or pipeline. Done.
- **No** → **Algorithmic Search Quadrant.** Use a classical solver. The LLM may have a place upstream (parsing intent into a structured problem instance), but the search belongs to the algorithm. Done.

### Q3. Can the workflow be defined in advance?

"Workflow" means: the role of each LLM call, the order of calls, and the data flow between them. Test: **can you draw the sequence of LLM calls on a whiteboard before running the system, labeling each call's role and schema?** If the next call's role depends on what the previous output happened to be, the workflow is exploratory.

- **Yes** → **LLM Workflow Quadrant.** Build it as deterministic control flow plus bounded LLM calls, one call per role, each with a documented schema. Done.
- **No** → Q4.

This is the central triage question, and the one most often skipped. Half the work that "obviously needs an agent" turns out to be drawable once someone actually tries.

### Q4. Can a pre-named gap-bearer be identified — and, if operation-phase, is the Phase-crossing decision recorded?

Reaching Q4 means the work genuinely requires the **Autonomous Agentic Loop Quadrant**. Two parts; both must be yes to deploy.

**Part A (always).** Who absorbs the attribution gap? Test: can the deployment record name a specific human, an organizationally identifiable role with formal succession, or a contractual party (insurance pool, regulatory body) who acknowledges **at deployment time** that they bear responsibility for failures of this work? Leaving this to post-incident discovery is the *moral crumple zone* failure mode (Elish 2019): responsibility lands on whoever is operationally proximate — usually the person with the least actual control.

**Part B (only if Q0 = operation).** When a new pattern surfaces during operation, will the loop handle it dynamically in place, or will it be routed back to the design phase as feedback? Both answers are admissible; **"we'll figure that out when it happens" is not.** Design-phase placements satisfy this automatically — the design phase *is* where Phase-crossing happens.

- **Yes (both parts)** → **Autonomous Agentic Loop Quadrant**, with the gap-bearer (and Phase-crossing decision, when applicable) recorded alongside the architectural choice.
- **No** → **do not deploy.** This is the configuration the whole triage exists to prevent: an autonomous loop running with an unmanaged gap.

---

## Governance by quadrant

Once the quadrant is known, apply its governance set. The AAP ADR numbers are the "why" references; the control descriptions are self-contained.

| Quadrant | Governance | Required controls | Redirect on failure |
|---|---|---|---|
| (1) Script | Light | Code review, tests | Code author / pipeline owner |
| (2) Algorithmic Search | Light | Algorithm validation, search-space audit | Algorithm author |
| (3) LLM Workflow | Medium | See list below | Role owner of the failing call → routing-logic designer → upstream-data owner |
| (4) Autonomous Agentic Loop | High | Everything in (3) **plus** the loop controls below | The pre-named gap-bearer (component-level redirect is foreclosed) |

**LLM Workflow controls** (AAP ADR-0001, 0003–0007):

- No capability the workflow shouldn't have: no shell execution or arbitrary outbound HTTP shipped in the harness; each LLM call invoked through a narrow function with a documented schema (0001).
- RAG content, accumulated state, and externally fetched content treated as untrusted when injected into prompts (0003).
- At most one external side-effect surface per deployed unit; multi-surface workflows split into per-surface deployments (0004).
- Human approval gate on **behavior-modifying changes** — prompts, role definitions, schemas, routing logic — not a runtime "OK" click on every action (0005).
- Every LLM call's input, output, role identifier, and timestamp logged; control flow reconstructible from logs — every event, not a sample (0006).
- Prompts, role definitions, schemas, and routing rules live in version-controlled files (0007).

**Additional Autonomous Agentic Loop controls** (AAP ADR-0002, 0008–0010):

- Capabilities that cannot be made absent are gated deterministically *outside* the LLM (pre-tool-use hooks, structural quarantine) — probabilistic prohibition will not hold under prompt injection when the loop selects tools at runtime (0002).
- The loop's iterations, tool calls, observations, and prompts logged append-only; the trace must show *that* the loop failed even though it cannot show *which judgment element* contributed how much (0006, load-bearing here).
- Exactly one named human bound to the agent process; formal rotation is compatible (0008).
- The triage decision and the named gap-bearer recorded at deployment time (0009).
- Operation-phase placements: the Phase-crossing decision recorded at deployment time (0010).

**Post-incident in Quadrant 4** operates at the architectural level, not the component level: the gap-bearer decides whether to retrain, re-architect (often: move the now-understood work to the LLM Workflow Quadrant), or retire the deployment.

---

## Hybrid architectures

Real systems combine quadrants. Run the tree on each portion; the autonomous portion still needs Q4 answered for that portion.

- **Plan-and-Execute** — autonomous loop generates the plan; an LLM workflow executes each step. The loop is the Quadrant-4 portion.
- **Router agent** — an LLM workflow whose first call classifies inputs into branches. Router and branches are all bounded: still Quadrant 3.
- **Tiered handoff** — LLM workflow handles common cases; novel cases escalate to an approval-gated autonomous loop.

The same descent applies *inside* a single skill or feature: some subcomponents may be frozen paths while one genuinely needs runtime judgment. Evaluate subcomponent by subcomponent instead of labeling the whole thing "agentic" or "deterministic."

## When to re-run the triage

The answers change as work matures. Re-run when:

- Design-phase work matures into operation (the resulting placement often shifts from Quadrant 4 to 3 — a legitimate maturation, not a regression).
- Model or tooling improvements make a formerly undefinable workflow drawable (Q3 flips to yes).
- A regulatory change alters whether a gap-bearer can be named (Q4).
- Experience changes the Phase-crossing decision (re-record it; don't silently drift).

---

## Anti-patterns checklist

Each of these signals a misrouted quadrant or a skipped question:

- **Bounded work on an autonomous loop.** FAQ classification, invoice matching, customer support built as a ReAct agent. Symptoms: sandbox strength keeps escalating; human-in-the-loop load grows; postmortems say "the agent did something weird" instead of naming a component. Recovery: re-run Q3 — the workflow is drawable; build it as one.
- **OR problem on an autonomous loop.** Scheduling / routing / allocation given to an LLM loop. Worse solutions than a classical solver, plus an attribution gap it never needed. Recovery: Q1 yes, Q2 no → Algorithmic Search.
- **Deterministic task on an autonomous loop.** Form filling as an "agent": slower, costlier, non-deterministic, harder to audit. Recovery: Q1 yes, Q2 yes → Script.
- **Ungoverned loop in a high-risk domain.** Quadrant-4 work deployed without answering Q4 Part A. Post-incident, responsibility lands on the operationally proximate (moral crumple zone). Recovery: name the gap-bearer or do not deploy.
- **Operation-phase loop without a Phase-crossing decision.** "New patterns? It handles them dynamically" — chosen by default, not by decision. Recovery: run Q0 + Q4 Part B and record the answer.
- **Operation patterns absorbed in place instead of routed to design.** "We hit an edge case — the agent figured it out." Design feedback silently masked; the design phase atrophies. Recovery: tag new patterns, escalate to design review, encode or explicitly accept.
- **Approval gate as runtime checkbox.** A confirmation dialog before each action habituates into click-through. The gate belongs on behavior-modifying *changes*, decided by a named human.
- **Audit log as sample.** Sampled logging is monitoring, not audit; the incident run is never in the sample. Log every event, append-only.
- **Quadrant as agent type.** "We're building a Quadrant-3 agent" locks the agent to a quadrant and hides the triage. Quadrants classify work; triage each piece of work the agent handles.
- **Skipping the triage because "it obviously needs an agent."** Answer Q3 by actually drawing the workflow before concluding it can't be drawn.

---

## Triage record

A completed triage produces a short record kept with the deployment decision:

```
Work item:        <what the system is asked to do>
Phase (Q0):       design | operation
Q1 deterministic: yes/no   Q2 static-finite: yes/no   Q3 drawable: yes/no
Quadrant:         (1) Script | (2) Algorithmic Search | (3) LLM Workflow | (4) Autonomous Agentic Loop
If Quadrant 4:
  Gap-bearer:     <named human / formal role / contractual party> (acknowledged at deployment)
  Phase-crossing: handle in place | route back to design   (operation-phase only)
Governance set:   <controls applied, from the table above>
Re-run trigger:   <what change would invalidate this triage>
```

"This is operation phase, default composition (Quadrants 1 + 3)" is a complete record. "We didn't decide" is not.
