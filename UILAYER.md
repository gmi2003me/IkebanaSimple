# Idea Expansion Agent — UI Layer Prompt

> Source of truth for the app's UI. This file describes screens, states, and
> interaction patterns only — it does not describe AI behavior, question content,
> or synthesis logic. That lives in `PROMPT.md`; treat it as a black box this UI
> wraps around.
> Edit within your section only when possible — see `## 0. Editing Guide` for
> conventions that keep diffs small and merge conflicts rare.

---

## 0. Editing Guide

- Each `##` section is independent. Don't restructure another section to fix yours.
- Within a section, prefer one idea per bullet / one bullet per line. Never combine
  edits into a single run-on paragraph — it causes line-level merge conflicts.
- Adding to a list? Append at the end of the list, not alphabetically interleaved,
  unless the list explicitly says "keep alphabetized."
- If you change behavior described in `## 1. Role & Scope` or `## 2. Non-Goals`,
  flag it in your PR description — those two sections are the contract every other
  section depends on.
- Bump `## 9. Changelog` with every substantive edit (one line, your initials, date).

---

## 1. Role & Scope

You are designing the UI for a single-page web app that walks someone through
turning a rough, early-stage product idea into a fuller, more rigorously examined
version of itself, through a guided conversation with an AI thinking partner.

This prompt covers **layout, phase states, and interaction patterns only.** The
content the AI produces (questions, synthesis, etc.) is governed entirely by
`PROMPT.md` — this file should accommodate that behavior without re-describing it.

The session always moves through four phases, in this order:

1. **Intake** — the user states their idea, raw, in whatever form it arrives.
   No critique or expansion happens yet — this is just capture.
2. **Question Loop** — the AI asks a *round* of several questions at once, the
   user answers the batch, and the AI decides whether to run another round or
   move on. This repeats until coverage is sufficient — no fixed round count.
3. **Synthesis** — the AI produces one structured deliverable with several named
   sections (refined statement, what changed and why, blind spots, expanded
   surface area, open tensions).
4. **Comparison** — the last section of that same deliverable is the user's
   original idea, quoted verbatim, so the delta is visible without scrolling back.

**Success is measured against one bar:** someone unfamiliar with the product
should be able to use the UI and always know which phase they're in, what's
expected of them, and how to move forward — without needing to read the
behavior prompt or any documentation.

---

## 2. Non-Goals

Explicitly out of scope. Do not let the design drift into these:

- **Not a generic chatbot-with-sidebar UI.** The four phases must be visually
  legible as distinct phases, not just inferred from message content.
- **Not a multi-mode product.** No domain switcher, no domain-specific branding —
  this is a general-purpose tool, not tied to any company, industry, or persona.
- **Not a settings/auth/admin product.** No login flows, no multi-user or team
  features, no admin screens.
- **Not a flattened Synthesis.** The Synthesis document's named sections are
  structural, not optional formatting — never collapse them into one
  undifferentiated block of AI output.

---

## 3. Phase Layout (must reflect this order)

1. **Intake** — single large freeform input, minimal framing, no pressure about
   format or completeness. The only phase with just one field.
2. **Question Loop** — each round presents multiple questions together as a
   batch (not a one-question-at-a-time quiz). Prior rounds remain visible but
   de-emphasized once a new round starts.
3. **Synthesis** — read-only structured document. Typographically the most
   considered screen in the app; the content is the centerpiece.
4. **Comparison** — not a separate screen; the final section *within* Synthesis,
   visually marked as a quoted/verbatim artifact distinct from the AI's prose.

Synthesis also supports an optional, repeatable **Blind Spot Roll-In sub-flow**
(see `## 5.4`) — a branch off Synthesis, not a fifth phase. The four phases
above remain the only top-level phases; Roll-In always returns to Synthesis
when it concludes.

Each phase transition should feel like a deliberate hand-off — something closes
(the prior phase ends) and something opens (the next phase begins) — never a
silent continuation that leaves the user unsure which phase they're in.

---

## 4. Persistent UI Elements

- Lightweight, always-visible indication of current phase (Intake / Questions /
  Synthesis), so the user knows where they are and roughly how much remains.
- A way to see prior rounds/history without losing focus on the current phase.
- During the Question Loop, a visible (not buried) way to end the loop early,
  paired with a note about what coverage gaps remain if the user does so.
- Any control that triggers an async/API-backed action shows a busy indicator
  with a spinning animation on that specific control while the action is in
  flight (e.g. submitting a question round, ending the loop, generating
  roll-in suggestions, accepting a suggestion, sending roll-in feedback).
  Other controls remain visible but inactive until it resolves — only the
  clicked control animates.

---

## 5. Input & Interaction Per Phase

### 5.1 Intake
- One freeform input affordance. No structure imposed on how the idea is typed.

### 5.2 Question Loop
- A grouped-answer input: the user answers a full batch of questions, then
  submits the round as a unit, not question-by-question.
- User can answer in whatever order/format feels natural; short answers are fine.
- Explicit "end the loop now" action, distinct from just submitting an empty round.

### 5.3 Synthesis
- No input needed — this is a read/export experience.
- Obvious copy/export action for the full structured document.
- Each item in "Blind Spots Surfaced" carries one of two states: a "Roll this
  into my idea" link, or, once resolved, a quiet status note reading "This has
  been integrated." Never show both for the same item.

### 5.4 Blind Spot Roll-In (sub-flow off Synthesis)
- Clicking "Roll this into my idea" navigates to a dedicated Roll-In page —
  not a modal — scoped to that single blind spot.
- The page shows 3 integration suggestions as ranked cards (best first, per
  the AI's own recommendation), each with its rationale visible without a click.
- Two ways forward per card set: **accept** one of the 3 directly, or **give
  feedback**, which opens a lightweight dialog/chat with the AI scoped only to
  refining this one integration. The user can go back and forth in that dialog
  as long as needed.
- Once the user accepts (either an original card or a feedback-refined
  version), the page hands off back to Synthesis automatically — no separate
  confirmation screen. The idea is updated, and the rolled-in blind spot now
  shows "This has been integrated" instead of its link.
- A visible way to abandon the Roll-In and return to Synthesis unchanged,
  for users who start the flow and decide not to integrate after all.

---

## 6. Tone & Visual Identity

- Calm, considered, "thinking tool" aesthetic — closer to a long-form writing or
  research app than a chat product.
- Typography-forward, especially in Synthesis, where the document is the focus.
- Minimal color, generous whitespace, no decorative chrome competing with the
  content being produced.

---

## 7. States to Account For

- Empty state / session start (Intake, first idea capture)
- Mid-Question-Loop, round N in progress, prior rounds visible but de-emphasized
- End-of-loop choice point (continue another round vs. wrap up now, with gap
  visibility)
- Synthesis document, fully rendered with all named sections including the
  verbatim original
- Blind Spot Roll-In: 3-suggestion view for a selected blind spot
- Blind Spot Roll-In: feedback dialog in progress, mid-iteration
- Synthesis after one or more blind spots have been integrated, showing a mix
  of remaining "Roll this into my idea" links and "This has been integrated"
  notes
- Returning to a past session, if applicable
- Busy/in-flight state on a clicked control, immediately on click and until
  the triggered action resolves

---

## 8. Example Interaction Sketch (non-binding, illustrative only)

This section is for onboarding new contributors to the prompt's intent — it is
not instruction text the model should imitate verbatim.

```
Intake: user types a one-sentence idea into a single open field, no labels
beyond a soft placeholder.

Question Loop, round 1: a batch of 4 questions appears together. User answers
all 4, submits. A subtle marker separates round 1 (now collapsed) from round 2,
which appears once the AI decides more coverage is needed.

End-of-loop point: after round 3, the user is offered "wrap up now" alongside
a one-line note on what's still uncovered, or "continue" to keep going.

Synthesis: a single scrollable document appears with clearly separated named
sections, ending in a visually distinct quoted block — the user's original
idea, verbatim — sitting right after the refined version for direct comparison.
A copy/export control sits at the top or bottom of the document.
```

---

## 9. Changelog

- 2026-06-22 — initial version drafted, restructured to mirror PROMPT.md
  conventions (sections, editing guide, changelog).
- 2026-06-22 — added Blind Spot Roll-In sub-flow (`## 5.4`), updated Phase
  Layout note and States to Account For accordingly.
- 2026-06-22 — added busy/spinner indicator requirement for async-triggering
  controls (`## 4`, `## 7`).
