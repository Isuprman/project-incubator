---
name: project-incubator
description: >-
  Take a project from zero to shippable in one flow — Socratic idea mining,
  decision-tree grilling, spec & ticket generation, then test-first
  implementation with built-in review per ticket. Use when the user has no
  idea yet ("I have no idea what to build"), wants to stress-test a rough
  idea, or wants a complete from-zero-to-code workflow. Combines
  socratic-questioning, grilling, to-spec, to-tickets, tdd, and code-review
  into a single guided pipeline.
version: 1.0.0
---

# Project Incubator

A single guided pipeline that takes a project from *nothing* to *working code with tests and review*.

It merges six workflows into one flow:

1. **Socratic idea mining** — when the user has no idea, interview them one question at a time to dig out a real problem worth building.
2. **Decision-tree grilling** — stress-test the chosen direction with a relentless interview; every question carries a recommended answer.
3. **Fixing** — turn the agreed design tree into ADR + CONTEXT.md + spec.
4. **Ticket splitting** — break the spec into independently deliverable tickets with blocking edges.
5. **Test-first implementation** — implement tickets one by one (red → green → refactor).
6. **Built-in review** — every ticket is reviewed against repo standards + spec before it counts as done.

The pipeline is **stage-gated**: the agent must not skip a stage, and must not start
implementing before the user confirms the shared understanding.

---

## Trigger

Use when the user says any of:

- "I have no idea what to build" / "没有想法，帮我找一个值得做的项目"
- "Help me incubate a project from zero" / "帮我从 0 到 1 孵化一个项目"
- "Grill / stress-test this idea" (combined with wanting the full flow)
- "I want a spec and tickets for my idea" + wants code afterward

The user can also start mid-pipeline: if they already have an idea, skip Stage 0
(confirm it in one sentence) and begin at Stage 1.

---

## Pipeline overview

```
Stage 0  Socratic mining    (no idea yet)      → 3 candidate directions, scored
Stage 1  Grilling           (has a direction)  → agreed design tree
Stage 2  Fixing             (agreed tree)      → ADR + CONTEXT.md + spec
Stage 3  Ticket splitting   (spec)             → ordered tickets w/ dependencies
Stage 4  Build loop         (tickets)          → per ticket: tests → code → review → done
```

**Stage gates — do not skip:**
- Stage 0 → 1: user picked one candidate direction.
- Stage 1 → 2: user confirmed the shared understanding (frontier empty).
- Stage 2 → 3: spec is written and accepted.
- Stage 3 → 4: tickets are confirmed (grain + dependencies).
- Within Stage 4, each ticket: tests written first → implementation → review passed → next.

---

## Stage 0 — Socratic idea mining

Trigger: the user has no idea, or a vague feeling ("想做点什么但不知道做什么").

Rules:

- Ask **one** question per round. Never a list.
- Mine from: the user's real experience, recurring annoyances, people they can reach, and verifiability.
- Prefer questions that reveal *pain* and *frequency*, not preferences: "What have you repeatedly wished was less annoying?" beats "What colors do you like?"
- Do not propose solutions yet. Stay in discovery.
- The agent does its own fact-finding (filesystem, tools); the user only answers from their life.

Exit condition: after ~10 rounds max, synthesize **3 candidate directions**, each scored:

| Dimension | Scale | Meaning |
|-----------|-------|---------|
| Pain intensity | 1–5 | How sharp / frequent is the underlying problem |
| Reachability | 1–5 | Can the user actually reach first users |
| 2-week MVP | 1–5 | Can a minimal version be built & validated in 2 weeks |

Present the table, give a recommendation, and **wait for the user to pick one**.
Do not proceed to Stage 1 until they choose.

---

## Stage 1 — Decision-tree grilling

Trigger: a direction is chosen (or the user already had one).

Work the decision tree in **rounds**:

- The **frontier** = every decision whose prerequisites are already settled — the questions you can ask *now*.
- Ask the **whole frontier in one round**: number each question (Q1, Q2, …), give a **recommended answer** for each (`➡️`), then wait.
- Format each question:

```
❓ **Q1** - **<question title>**: <question body, possibly multiple paragraphs, with options>

➡️ <recommended answer>
```

- User replies per question: "同意" / "agree", or a corrected answer. Recompute the frontier and ask the next round.
- A question whose answer depends on another question still open in this round belongs to a *later* round.
- **Finding facts is the agent's job, never the user's.** If a frontier question needs an environment fact (files, tools), dispatch a sub-agent — don't make the user look it up. Don't block the rest of the frontier on it.
- **Decisions are the user's.** Put each to them and wait.

Exit condition: frontier is empty — every branch visited, nothing silently assumed.
**Do not act on the design until the user confirms the shared understanding.**

---

## Stage 2 — Fixing (ADR + CONTEXT.md + spec)

Trigger: user confirmed the design tree.

Deliverables:

1. **ADR** — record the key architectural decisions made during grilling (one ADR per significant decision, or one combined ADR). Location: `docs/adr/`.
2. **CONTEXT.md** — glossary / domain vocabulary that emerged (terms used by the team/domain). Location: repo root.
3. **spec.md** — a single spec covering:
   - Problem & target users
   - Core value & MVP scope
   - Explicit non-goals
   - The review protocol (rubric, scoring, output format)
   - Acceptance criteria (verifiable)
   - Boundaries & must-not-delete core

Spec must be **self-contained**: anyone (or any agent) reading it can implement without asking the user.

---

## Stage 3 — Ticket splitting

Trigger: spec accepted.

Break the spec into **tracer-bullet tickets**:

- Each ticket is independently deliverable and verifiable.
- Declare **blocking edges**: ticket B blocked by ticket A ("Blocked by: A").
- Publish to the configured tracker (local Markdown under `.scratch/<feature>/issues/` by default).
- Each ticket contains: deliverable, acceptance criteria, blocked-by, estimated scope.

Present the ticket list to the user. Confirm **grain and dependencies** before starting Stage 4.
The user may request merges/splits/dependency changes — apply them.

---

## Stage 4 — Build loop (test-first + review per ticket)

Trigger: tickets confirmed.

Process **one ticket at a time**, in dependency order:

1. **Confirm the public test seam.** Before writing tests, state the external contract(s) of this ticket (function signatures, endpoints, components) and confirm with the user.
2. **Write tests first** (red) — cover the contract: happy path, edge cases, failure branches.
3. **Implement** (green) — minimal code to pass.
4. **Refactor** (optional) — keep green.
5. **Review this ticket** — two axes, in parallel sub-agents:
   - **Standards**: does the code follow the repo's documented standards?
   - **Spec**: does it match what the spec asked for?
   Report both side by side.
6. **Done = tests green + review passed.** If review finds issues: fix → re-run tests → re-review. Never skip review to save time.
7. Mark the ticket done, move to the next.

During the loop, the user's role: confirm test seams, resolve design questions the review surfaces, and accept/reject review findings. The agent implements, tests, and reviews.

---

## Pitfalls

- **Never skip a stage gate.** Especially: don't implement before the shared understanding is confirmed.
- **Socratic stage must stay one-question-at-a-time.** A question dump turns into an interview the user will abandon.
- **Don't propose features in Stage 0.** Stay in discovery; solutions come in Stage 1+.
- **Recommended answers are mandatory in Stage 1.** A question without `➡️` forces the user to invent answers — the #1 friction point.
- **Facts vs decisions**: agent finds facts, user makes decisions. Never make the user research.
- **Review is part of the ticket, not an afterthought.** A ticket that passes tests but fails review is not done.
- **Spec drift**: if Stage 4 reveals the spec is wrong, update the spec + ADR (with a note) instead of silently coding around it.

## References

- `references/socratic-questions.md` — question bank for Stage 0
- `references/spec-template.md` — spec.md template for Stage 2
- `references/ticket-template.md` — ticket template for Stage 3
- `references/review-axes.md` — standards vs spec review detail for Stage 4

## Attribution

This skill synthesizes ideas from:
- **mattpocock/skills** (MIT) — grilling, to-spec, to-tickets, tdd, code-review workflows
- **AI Agent 从 0 到 1 实战指南** — Socratic idea-mining methodology (B-G-U-C-O-A framework, first-principles MVP scoping)
