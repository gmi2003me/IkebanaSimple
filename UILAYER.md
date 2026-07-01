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

The session always moves through seven phases, in this order:

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
5. **Success Metrics** — the user defines one North Star Metric and exactly three
   KPIs, guided by AI recommendations drawn from the session, ending in a
   summary screen with a trigger to begin prototyping.
6. **Prototype** — the user manually triggers this phase. The AI proposes
   variation axes, the user confirms, and 3–5 low-to-mid fidelity HTML prototypes
   are generated and displayed side by side. User selects one after giving
   feedback. Skipped for non-digital products.
7. **Handoff** — a markdown artifact is generated containing the refined idea,
   metrics, selected prototype rationale, decisions to preserve, out-of-scope
   items, and a ready-to-use builder prompt.

**Success is measured against one bar:** someone unfamiliar with the product
should be able to use the UI and always know which phase they're in, what's
expected of them, and how to move forward — without needing to read the
behavior prompt or any documentation.

---

## 2. Non-Goals

Explicitly out of scope. Do not let the design drift into these:

- **Not a generic chatbot-with-sidebar UI.** All phases must be visually
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
5. **Success Metrics** — a two-part guided selection flow (North Star Metric,
   then KPIs), followed by a summary screen. Entered from Synthesis once the
   user is done with any Roll-Ins; does not return to Synthesis.
6. **Prototype** — entered from the Success Metrics summary screen via a manual
   trigger. The AI proposes variation axes first; user confirms before prototypes
   are generated. 3–5 HTML prototypes displayed side by side with annotations.
   If the idea is non-digital, this phase is skipped and the user proceeds
   directly to Handoff.
7. **Handoff** — a generated markdown artifact, displayed inline and
   downloadable. Terminal state of the session.

Synthesis also supports an optional, repeatable **Blind Spot Roll-In sub-flow**
(see `## 5.4`) — a branch off Synthesis, not a top-level phase. Roll-In always
returns to Synthesis when it concludes; Success Metrics is entered from Synthesis
after all Roll-Ins are complete.

Each phase transition should feel like a deliberate hand-off — something closes
(the prior phase ends) and something opens (the next phase begins) — never a
silent continuation that leaves the user unsure which phase they're in.

---

## 4. Persistent UI Elements

- Lightweight, always-visible indication of current phase (Intake / Questions /
  Synthesis / Success Metrics / Prototype / Handoff), so the user knows where
  they are and roughly how much remains.
- A way to see prior rounds/history without losing focus on the current phase.
- During the Question Loop, a visible (not buried) way to end the loop early,
  paired with a note about what coverage gaps remain if the user does so.
- Any control that triggers an async/API-backed action shows a busy indicator
  with a spinning animation on that specific control while the action is in
  flight (e.g. submitting a question round, ending the loop, generating
  roll-in suggestions, accepting a suggestion, sending roll-in feedback,
  confirming variation axes, generating prototypes).
  Other controls remain visible but inactive until it resolves — only the
  clicked control animates.

---

## 5. Input & Interaction Per Phase

### 5.1 Intake
- One freeform input affordance. No structure imposed on how the idea is typed.
- A **"Create an idea"** link sits inline with the "Your idea" field label,
  right-aligned on the same row.
- Clicking it invokes the AI per `## 6c. Idea Suggestion` (PROMPT.md) and
  fills the textarea with the returned single-sentence idea.
- Repeated clicks replace the previous content — the textarea is always
  overwritten, never appended to.
- The link shows the standard busy/spinner state (per `## 4`) while the call
  is in-flight; the Submit button remains disabled during that time.
- The generated idea is editable — the user may modify it freely after it
  appears before clicking Begin.

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

### 5.5 Success Metrics

The Success Metrics phase has three sequential screens: North Star Metric
selection, KPI selection, and a final summary. A persistent "Step X of 3"
indicator within this phase (NSM = step 1, KPIs = step 2, Summary = step 3)
tells the user where they are inside Success Metrics independently of the
top-level phase indicator.

**North Star Metric screen**
- Display 3 AI-recommended NSM cards in a ranked list (best-fit first).
- Below the cards, an open text input for the user to enter their own candidate.
- Each card, and the user's submitted text entry, shows two actions:
  - **"Use this"** — prominent primary action; confirms this as the NSM and
    advances to the KPI screen immediately.
  - **"Refine"** — secondary action; expands an inline text box beneath that
    card only. The user types refinement notes and submits. The card updates
    in place to show the revised metric; the same two actions reappear.
    Repeat until "Use this" is clicked. No limit on refinement rounds.
- Only one refinement box is open at a time; opening one collapses any other.
- The user's own text input follows the same "Use this" / "Refine" pattern
  once they submit it.

**KPI screen**
- Display 5 AI-recommended KPI cards, ordered by relevance (most relevant
  first). Each shows its rationale inline, visible without any click.
- Below the cards, an open text input for the user to enter their own
  candidate KPI.
- Each card and any user-submitted entry shows two actions:
  - **"Use this"** — adds the KPI to the selected set; the card moves to a
    "selected" visual state. The running count updates immediately (e.g.
    "1 of 3 selected").
  - **"Refine"** — same inline expansion pattern as the NSM screen; card
    updates in place on submission; "Use this" / "Refine" reappear.
- A selected KPI shows a **"Remove"** action in place of "Use this," so the
  user can de-select before the count reaches 3.
- Once 3 KPIs are selected, all remaining unselected cards and the text input
  are visually disabled (not hidden). The phase advances to the summary screen
  automatically, with no separate confirm step.

**Summary screen**
- Read-only. Shows:
  - North Star Metric, clearly labeled, in a visually prominent treatment.
  - Three KPIs listed in selection order, numbered, below the NSM.
- A copy/export action for the full summary content.
- A **"Begin Prototyping"** primary action that manually triggers phase 6.
  For non-digital products, this button is replaced by a **"Generate Handoff"**
  action that skips directly to phase 7.

### 5.6 Prototype (Generation & Selection)

**Variation axis confirmation**
- Before generating prototypes, the AI proposes 3–5 variation axes as a
  short list. Display these as readable cards with a brief rationale each.
- The user may accept the proposed axes or give feedback to adjust them.
  An inline text input below the list accepts redirects. The list updates
  in place on submission.
- A **"Generate prototypes"** action confirms the axes and triggers generation.
  Show the standard busy/spinner while prototypes are being generated.

**Prototype display**
- Present all prototypes in a single view. Each prototype occupies its own
  panel with:
  - Its descriptive label (design hypothesis) as a header
  - One-sentence rationale beneath the label
  - The rendered HTML prototype in a sandboxed iframe
  - Annotation callouts visible within or directly adjacent to the iframe,
    matching the decision points and assumption flags baked into the HTML
- Panels should be scannable side by side (or in a scrollable row) — the
  user needs to compare them without toggling between views.

**Feedback & selection**
- Below the prototype panels, an open feedback input inviting structured
  response: which assumptions landed, which felt wrong, which direction is
  closest, what's missing from all of them.
- Each panel has a **"Select this"** action. Clicking it marks that prototype
  as selected and advances to Handoff automatically.
- If the user wants to iterate on a specific prototype before selecting, an
  **"Iterate"** action on that panel opens an inline dialog scoped to that
  prototype. On resolution, the prototype panel updates in place; "Select
  this" reappears.

### 5.7 Handoff Artifact

- The handoff markdown is generated inline and displayed as a formatted,
  readable document — not raw markdown.
- A **"Download .md"** action and a **"Copy"** action are both available.
- This is the terminal state of the session. No navigation forward is offered.

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
- Success Metrics — NSM screen: 3 candidate cards + open text input, all
  unselected
- Success Metrics — NSM refinement in progress: one card's inline text box
  open, revised metric displayed, awaiting "Use this"
- Success Metrics — KPI screen: 5 candidate cards + open text input, running
  count at 0 of 3
- Success Metrics — KPI screen mid-selection: 1 or 2 KPIs selected, count
  updated, remaining cards still active
- Success Metrics — KPI refinement in progress: one card's inline text box
  open, revised KPI displayed
- Success Metrics — summary screen: NSM + 3 KPIs, read-only, "Begin
  Prototyping" (or "Generate Handoff" for non-digital) action visible
- Prototype — variation axis confirmation: proposed axes displayed, feedback
  input available, "Generate prototypes" action not yet clicked
- Prototype — generating: busy/spinner active, prototype panels not yet shown
- Prototype — prototypes displayed: all panels visible, feedback input open,
  no prototype selected yet
- Prototype — iteration in progress: one panel's inline dialog open, others
  still visible but inactive
- Prototype — prototype selected: transitions immediately to Handoff
- Handoff — artifact displayed: formatted markdown inline, download + copy
  actions available, terminal state

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
- 2026-06-29 — added Success Metrics as fifth phase; updated `## 1`, `## 3`,
  `## 4`, `## 7`; added `## 5.5` with NSM screen, KPI screen, and summary
  screen interaction specs. (GM)
- 2026-06-29 — added "Create an idea" link spec to `## 5.1 Intake`. (GM)
- 2026-07-01 — added Prototype (phase 6) and Handoff (phase 7); updated `## 1`,
  `## 3`, `## 4`, `## 7`; added `## 5.6` and `## 5.7`; updated §5.5 summary
  screen to include "Begin Prototyping" trigger. (HC)
