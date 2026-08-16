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
disable-model-invocation: true
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
- **Stage 1 → 2**: user confirmed the shared understanding (frontier empty).
- **Stage 2 → 3**: spec is written and accepted.
- **Stage 3 → 4**: tickets are confirmed (grain + dependencies).
- **Within Stage 4, each ticket**: tests written first → implementation → review passed → next.

---

## Stage 0 — Socratic idea mining

Trigger: the user has no idea, or a vague feeling ("想做点什么但不知道做什么").

Rules:

- **First, pick the project mode.** Ask the user once, before mining: do they want an **MVP** (a minimal
  version to validate in ~2 weeks) or a **full version** (a complete system / portfolio piece, planned
  over ~1–2 months)? Default to MVP if they are unsure. The chosen mode changes the scoring dimensions
  below and the spec/ticket templates downstream. Do not silently assume one.
- Ask **one** question per round. Never a list.
- Mine from: the user's real experience, recurring annoyances, people they can reach, and verifiability.
- Prefer questions that reveal *pain* and *frequency*, not preferences: "What have you repeatedly wished was less annoying?" beats "What colors do you like?"
- Do not propose solutions yet. Stay in discovery.
- The agent does its own fact-finding (filesystem, tools); the user only answers from their life.

Exit condition: after ~10 rounds max, synthesize **3 candidate directions**, each scored by the
**mode's** dimensions:

MVP mode:

| Dimension | Scale | Meaning |
|-----------|-------|---------|
| Pain intensity | 1–5 | How sharp / frequent is the underlying problem |
| Reachability | 1–5 | Can the user actually reach first users |
| 2-week MVP | 1–5 | Can a minimal version be built & validated in 2 weeks |

Full mode:

| Dimension | Scale | Meaning |
|-----------|-------|---------|
| Pain intensity | 1–5 | How sharp / frequent is the underlying problem |
| Reachability | 1–5 | Can the user actually reach first users |
| Full delivery | 1–5 | Can a complete system be delivered in ~1–2 months with real depth (not a demo) |

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
3. **spec.md** — a single spec covering (adapted to the **project mode** chosen in Stage 0):
   - Problem & target users
   - Core value & scope — MVP mode: "MVP scope" (must-have / explicit non-goals / first validation surface); Full mode: "core modules & complete feature set" (module list, data model, full user journeys, explicit out-of-scope)
   - The review protocol (rubric, scoring, output format)
   - Acceptance criteria (verifiable)
   - Boundaries & must-not-delete core (MVP) / release plan & phase boundaries (Full)

Spec must be **self-contained**: anyone (or any agent) reading it can implement without asking the user.

---

## Stage 3 — Ticket splitting

Trigger: spec accepted.

Break the spec into **tracer-bullet tickets**:

- Each ticket is independently deliverable and verifiable.
- Declare **blocking edges**: ticket B blocked by ticket A ("Blocked by: A").
- **Embed Spec excerpt** in every ticket: copy the relevant spec sections verbatim into the ticket file so the implementer can work from the ticket alone, without recalling the conversation or re-reading the full docs. This is mandatory — a ticket that says "see spec §X" instead of quoting it is incomplete.
- Publish to the configured tracker (local Markdown under `.scratch/<feature>/issues/` by default).
- Each ticket contains: deliverable, acceptance criteria, spec excerpt, blocked-by, estimated scope.

Present the ticket list to the user. Confirm **grain and dependencies** before starting Stage 4.
The user may request merges/splits/dependency changes — apply them.

---

## Stage 4 — Build loop (test-first + review per ticket)

Trigger: tickets confirmed.

**Execution rhythm (critical):** process tickets **one at a time, in dependency order, and
finish each ticket completely before reporting back to the user.** Within a ticket, work in one
continuous pass — do NOT stop after each sub-step (tests written, backend done, frontend done…)
to report progress and wait. The agent completes the whole ticket (test seam → red → green →
refactor → review → mark done), then reports **once** with the deliverable summary and **waits
for the user's confirmation before starting the next ticket**.

Stop for the user ONLY at these gates:

1. **Test-seam confirmation** — before writing tests, state the contract and get a yes/no.
2. **Ticket-done confirmation** — after a ticket is fully done (tests green + review passed),
   report the deliverable and wait for the user to say "next" / confirm before moving to the
   next ticket. Do not auto-start the next ticket.

Everything else (writing tests, implementing, refactoring, re-running tests, fixing review
findings, marking done) proceeds without waiting. If the environment interrupts (build
timeout, daemon not running), fix or work around it and continue; only surface a blocker to
the user when it genuinely requires their action. Do not emit "I will continue…" progress
messages mid-ticket — just keep working until the ticket is done.

**Re-read the docs at fixed nodes (anti-drift, critical):** long flows dilute early context
(spec, acceptance criteria) and the agent starts working from memory. To counter this, re-read
the relevant files at these fixed points — never rely on memory for constraints:

1. **Ticket start** — re-read the ticket file (its embedded Spec excerpt) and the acceptance
   criteria of the current ticket. Work from the file, not from memory.
2. **Before review** — re-read the ticket's acceptance criteria + spec excerpt, then check the
   implementation against them line by line. If the review finds the implementation drifts
   from the excerpt, the review must FAIL, not be waived.
3. **Every 2–3 tickets** — re-read the global spec.md + CONTEXT.md once to prevent cross-ticket
   drift (e.g. forgetting an explicit non-goal). If the full spec is long, at minimum re-read
   the acceptance criteria and non-goals sections.

Process **one ticket at a time**, in dependency order:

1. **Confirm the public test seam.** Before writing tests, state the external contract(s) of this ticket (function signatures, endpoints, components) and confirm with the user.
2. **Write tests first** (red) — cover the contract: happy path, edge cases, failure branches.
3. **Implement** (green) — minimal code to pass.
4. **Refactor** (optional) — keep green.
5. **Review this ticket** — two axes, in parallel sub-agents:
   - **Standards**: does the code follow the repo's documented standards?
   - **Spec**: does it match what the spec asked for?
   Report both side by side.
6. **Done = tests green + all reviews passed.** If any review finds issues: fix → re-run tests → re-review. Never skip review to save time.
7. Mark the ticket done, then **report once** (deliverables + test results + review verdicts) and **wait for the user's confirmation** before starting the next ticket.

During the loop, the user's role: confirm test seams, and confirm each completed ticket. The
agent implements, tests, and reviews without step-by-step prompting; the user does not drive
progress inside a ticket.

---

## Pitfalls

- **Never skip a stage gate.** Especially: don't implement before the shared understanding is confirmed.
- **Socratic stage must stay one-question-at-a-time.** A question dump turns into an interview the user will abandon.
- **Don't propose features in Stage 0.** Stay in discovery; solutions come in Stage 1+.
- **Recommended answers are mandatory in Stage 1.** A question without `➡️` forces the user to invent answers — the #1 friction point.
- **Facts vs decisions**: agent finds facts, user makes decisions. Never make the user research.
- **Review is part of the ticket, not an afterthought.** A ticket that passes tests but fails review is not done.
- **Finish the ticket before reporting.** Do not emit mid-ticket progress updates ("backend done", "I will continue…") and stop for the user only at the two gates: test-seam and ticket-done confirmation.
- **Never work from memory on constraints.** Re-read the ticket file (and spec excerpts) at ticket start and before review — the anti-drift nodes in Stage 4. Drift is how "spec said X, code does Y" happens.
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
