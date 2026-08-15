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

It merges seven workflows into one flow:

1. **Socratic idea mining** — when the user has no idea, interview them one question at a time to dig out a real problem worth building.
2. **Decision-tree grilling** — stress-test the chosen direction with a relentless interview; every question carries a recommended answer.
3. **Fixing** — turn the agreed design tree into ADR + CONTEXT.md + spec + design brief.
4. **Ticket splitting** — break the spec into independently deliverable tickets with blocking edges, flagging UI tickets.
5. **Design gating** — UI tickets are gated by a design pass *before* implementation and a design axis *after* (see Design Track).
6. **Test-first implementation** — implement tickets one by one (red → green → refactor).
7. **Built-in review** — every ticket is reviewed against repo standards + spec + design before it counts as done.

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
Stage 2  Fixing             (agreed tree)      → ADR + CONTEXT.md + spec + design brief
Stage 3  Ticket splitting   (spec)             → ordered tickets w/ dependencies + UI flags
Stage 4  Build loop         (tickets)          → per ticket: design gate → tests → code → 3-axis review → done
```

**Stage gates — do not skip:**
- Stage 0 → 1: user picked one candidate direction.
- **Stage 1 → 2**: user confirmed the shared understanding (frontier empty).
- **Stage 2 → 3**: spec (and design brief, if UI is involved) is written and accepted.
- **Stage 3 → 4**: tickets are confirmed (grain + dependencies).
- **Within Stage 4, each ticket**: design gate (UI tickets) → tests written first → implementation → 3-axis review passed → next.

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

## Stage 2 — Fixing (ADR + CONTEXT.md + spec + design brief)

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
4. **design-brief.md** (required if the product has any UI; skip only for pure-library/CLI projects) — covers:
   - Page inventory with page-kind classification (system/tool page vs marketing page vs game UI — drives the design-skill routing below)
   - Per-page layout, component states, interaction requirements
   - Design principles distilled from the user's stated taste (e.g. no decorative background blocks; hierarchy via spacing/type/alignment; centered & symmetric; status badges must not crowd titles)
   - 2–3 distinct visual directions with tradeoffs → user picks one → refine

Spec and design brief must be **self-contained**: anyone (or any agent) reading them can implement without asking the user.

### Design Track (design-skill routing)

The design engine is **bundled** with this skill under `references/design/` (the design-taste
synthesis — see `ATTRIBUTION.md` for sources & licenses). It works in any environment with no
external installs.

When implementing or reviewing UI, route by page kind:

| Page kind | Primary | Fallback |
|---|---|---|
| Marketing / product pages | `references/design/` (built-in taste) | `design-guidelines.md` |
| System / tool pages (forms, tables, dashboards) | `references/design/` (built-in taste) | `design-guidelines.md` |
| Game UI | environment's `game-ui-design` if available, else `references/design/` | `design-guidelines.md` |

The bundled `references/design/` engine is always the default; `references/design-guidelines.md`
remains as a quick-reference/checklist distillation. If the environment additionally has a
specialized skill (e.g. `ui-ux-pro-max` for system pages), it may be used to *augment*, not
replace, the built-in engine. Never ship UI without a design pass.

---

## Stage 3 — Ticket splitting

Trigger: spec accepted.

Break the spec into **tracer-bullet tickets**:

- Each ticket is independently deliverable and verifiable.
- Declare **blocking edges**: ticket B blocked by ticket A ("Blocked by: A").
- **Flag ticket kind**: `logic` (pure logic / no UI) or `ui` (touches interface). UI tickets reference the relevant design-brief page in their Notes and are subject to the design gate in Stage 4.
- Publish to the configured tracker (local Markdown under `.scratch/<feature>/issues/` by default).
- Each ticket contains: deliverable, acceptance criteria, blocked-by, ticket kind, estimated scope.

Present the ticket list to the user. Confirm **grain and dependencies** before starting Stage 4.
The user may request merges/splits/dependency changes — apply them.

---

## Stage 4 — Build loop (test-first + review per ticket)

Trigger: tickets confirmed.

Process **one ticket at a time**, in dependency order:

1. **Confirm the public test seam.** Before writing tests, state the external contract(s) of this ticket (function signatures, endpoints, components) and confirm with the user.
2. **Design gate (UI tickets only).** Before implementing a `ui` ticket: load the design brief page for this surface, then run the design pass — invoke the routed design skill (see Design Track) or apply `references/design-guidelines.md` — and state the concrete design spec for this page (spacing scale, type hierarchy, accent color, states). Do not implement UI without this pass.
3. **Write tests first** (red) — cover the contract: happy path, edge cases, failure branches.
4. **Implement** (green) — minimal code to pass, following the design spec for UI tickets.
5. **Refactor** (optional) — keep green.
6. **Review this ticket** — three axes, in parallel sub-agents:
   - **Standards**: does the code follow the repo's documented standards?
   - **Spec**: does it match what the spec asked for?
   - **Design** (UI tickets): does the interface follow the design spec / design-guidelines (spacing scale, alignment, hierarchy, no AI-slop, states complete)?
   Report all three side by side.
7. **Done = tests green + all reviews passed.** If any review finds issues: fix → re-run tests → re-review. Never skip review to save time.
8. Mark the ticket done, move to the next.

During the loop, the user's role: confirm test seams, resolve design questions the review surfaces, and accept/reject review findings. The agent implements, tests, and reviews.

---

## Pitfalls

- **Never skip a stage gate.** Especially: don't implement before the shared understanding is confirmed.
- **Socratic stage must stay one-question-at-a-time.** A question dump turns into an interview the user will abandon.
- **Don't propose features in Stage 0.** Stay in discovery; solutions come in Stage 1+.
- **Recommended answers are mandatory in Stage 1.** A question without `➡️` forces the user to invent answers — the #1 friction point.
- **Facts vs decisions**: agent finds facts, user makes decisions. Never make the user research.
- **Review is part of the ticket, not an afterthought.** A ticket that passes tests but fails review is not done.
- **Never ship UI without a design pass.** UI tickets skip the design gate only when the ticket is flagged `logic`. This is the #1 fix for "the agent's frontend is ugly".
- **Spec drift**: if Stage 4 reveals the spec is wrong, update the spec + ADR (with a note) instead of silently coding around it.

## References

- `references/socratic-questions.md` — question bank for Stage 0
- `references/spec-template.md` — spec.md template for Stage 2
- `references/design/` — bundled design engine (design-taste synthesis: SKILL.md + anti-slop, design-systems, interaction-states, motion, pre-flight). Works with no external installs. See `ATTRIBUTION.md` for sources & licenses.
- `references/design-guidelines.md` — quick-reference distillation + design-axis checklist (fallback / checklist baseline)
- `references/ticket-template.md` — ticket template for Stage 3 (includes ticket-kind flag)
- `references/review-axes.md` — standards vs spec vs design review detail for Stage 4

## Attribution

This skill synthesizes ideas from:
- **mattpocock/skills** (MIT) — grilling, to-spec, to-tickets, tdd, code-review workflows
- **AI Agent 从 0 到 1 实战指南** — Socratic idea-mining methodology (B-G-U-C-O-A framework, first-principles MVP scoping)
