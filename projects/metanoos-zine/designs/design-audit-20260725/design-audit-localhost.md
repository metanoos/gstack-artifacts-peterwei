# Zine Design Audit

- Date: 2026-07-25
- Branch: `codex/integrated-trace-annotation`
- Surface: native Tauri desktop app, with the read-only web shell checked separately
- Mode: diff-aware, standard depth
- Classifier: app UI
- Authority: repository `DESIGN.md`

## First Impression

I’m looking at a serious editorial instrument, not a dashboard and not a chat app. The product communicates **dense, trace-aware writing** immediately.

I notice that the four-part shell is unusually legible even before I read labels: a view rail, a collection/replay column, the authored text, and a bottom action palette. The strongest visual weakness is that the almost-empty document leaves the selected violet sentence as the only expressive anchor while the thin gold root/tab rules carry disproportionate weight.

The first three things my eye goes to are:

1. the selected violet authored sentence;
2. the gold-outlined `root` scope and active file tab;
3. the bottom `AUTHOR / AI / FILESYSTEM` action palette.

If I had to describe it in one word: **instrumental**.

### Page Area Test

- Left rail: app views and durable settings. Clear in under two seconds.
- Collection column: Root tree above, mounted replay controls below. Clear.
- Main workspace: the selected zine’s text and provenance chrome. Clear.
- Bottom palette: authorship, AI operations, and filesystem actions. Clear, though the three rows are visually compressed.

### Litmus Checks

- Brand/product unmistakable in first screen: **Yes**
- One strong visual anchor present: **Yes, but content-dependent**
- Understandable by scanning labels only: **Yes**
- Each area has one job: **Yes**
- Cards necessary: **Not used**
- Motion improves hierarchy: **Not yet observed**
- Still premium without decorative shadows: **Yes**

### Trunk Test

**Partial, 5/6.** The site identity, current view, major sections, local options, and selected location are clear. There is no global search affordance.

## Inferred Rendered Design System

- Fonts: IBM Plex Mono dominates chrome; Newsreader carries prose; the browser shell also reports a small number of inherited `Times` nodes. This matches the declared three-family system in intent.
- Colors: eight rendered foreground/background values in the initial shell. The palette is coherent and restrained: neutral paper/ink plus one gold accent.
- Heading scale: no semantic HTML headings in the initial shell.
- Spacing: the sampled shell uses the declared 8px/32px values, with optical computed values introduced by `rem` sizing.
- Touch targets: no undersized visible targets in the read-only web shell at desktop size.
- Motion: the initial browser shell reports 95 elements whose computed transition property is `all`; reduced-motion media state is available but not active.
- Performance: 1.884s initial total load in the local web shell, including remote font fetches.

## Environment Note

The browser-only shell is intentionally read-only and stops at `Cannot mint Root: the AUTHOR signing key is unavailable.` The audit therefore uses the native signed-authoring app for product surfaces. That web fallback is recorded separately as evidence and is not scored as the primary product state.

## Findings

### FINDING-001 — Trace-analysis failures disappear before the first annotation

- Impact: High
- Category: Interaction / recovery
- Status: Best-effort fix
- Commit: `78f4b7f`
- Files: `TraceAnnotationOverlay.tsx`, `App.tsx`, `App.css`, new regression test
- Evidence: [`trace-annotation-qualified-marks.jpeg`](screenshots/trace-annotation-qualified-marks.jpeg), [`finding-001-after.png`](screenshots/finding-001-after.png)

I notice the annotation overlay previously returned `null` whenever the file had
zero annotations, including when post-Step analysis had produced an error. That
made a failed first annotation indistinguishable from a Step with nothing to
analyze. The fix presents a compact, assertive trace-analysis alert in that
state and clears stale errors after the next successful observation. All 1,531
client tests and typechecks passed. The rebuilt native app returned to its vault
lock screen, so the alert's production rendering could not be re-exercised
without the owner's passphrase.

### FINDING-002 — Primary navigation treated “Models” as the product concept

- Impact: Medium
- Category: Content / product language
- Status: Verified in browser shell; native bundle built successfully
- Commit: `9de0a31`
- Files: `AppNavigation.tsx`, `ModelsView.tsx`, `PromptInspectorModal.tsx`, `App.tsx`
- Evidence: [`models-before.jpeg`](screenshots/models-before.jpeg), [`finding-002-after.png`](screenshots/finding-002-after.png)

I notice the main rail said `Models`, the view introduced `LLM providers`, and
the Prompt Inspector described what it sends to `the LLM`. That conflicts with
the declared language system: AI is the participant users recognize; provider
and model are configuration detail. The rail and view title now say `AI`, the
provider screen uses direct AI language, and error/Inspector copy routes people
to AI settings. The after screenshot verifies the new rail label in the current
browser build.

### FINDING-003 — A local rewrite appeared as a whole-line replacement

- Impact: High
- Category: Trace readability
- Status: Best-effort fix
- Commit: `5f0cb76`
- Files: `AppShell.tsx`, new `editor-diff.ts`, new regression test
- Evidence: [`trace-flow-diff-prev.jpeg`](screenshots/trace-flow-diff-prev.jpeg)

I notice `Diff vs prev` struck the entire sentence and rendered another entire
sentence in green for a small phrase change. That destroys the main/alternate
relationship the review is supposed to reveal. The editor diff now preserves
unchanged prose and isolates exact word, whitespace, and punctuation changes.
Unit coverage proves a one-word rewrite no longer replaces its whole line and
that paragraph line breaks remain exact. Native visual verification is deferred
until the current vault is unlocked.

### FINDING-004 — The implemented reducer is narrower than the V1 deletion-first theory

- Impact: High
- Category: Product interaction / analysis
- Status: Deferred — semantic detector and annotation-model change
- Evidence: [`trace-annotation-qualified-marks.jpeg`](screenshots/trace-annotation-qualified-marks.jpeg), [`trace-flow-multiburst-draft.jpeg`](screenshots/trace-flow-multiburst-draft.jpeg)

I notice the current reducer waits for a bounded oscillation: at least two
material deletions, multiple revision transactions, and a repeated rewrite at
one locus. The stated V1 instead treats every deletion event as the elementary
divergence mark, with multi-character deletions carrying a count. Those are
different products. The audit's exact 68-edit Step compacts into three deletion
bursts of 12, 11, and 10 characters; the current detector accepts that
trajectory, but ordinary single deletions intentionally produce no annotation.

What if Layer 1 were a lossless derived deletion index rather than an
“interesting oscillation” detector? Each mark could bind the deleted text, the
surviving replacement, deletion count, exact locus, and timestamps. Higher
layers could then rank or group marks without deciding whether a deletion
deserves to exist.

### FINDING-005 — Rhythm exists as raw evidence but lacks a concise latest-activity projection

- Impact: Medium
- Category: Information hierarchy / AI context
- Status: Deferred — analysis/prompt contract
- Evidence: [`prompt-inspector-before.jpeg`](screenshots/prompt-inspector-before.jpeg)

I notice the Prompt Inspector already preserves the exact context block and a
bounded directory log with timing and character deltas. That is the correct
citation substrate, but the latest actions are buried inside the full evidence.
A separate deterministic `LATEST ACTIVITY` projection would improve orientation
without asking the AI to summarize the log before it analyzes it.

The projection should remain mechanical: the last N actions or bounded time
window, inter-action intervals, deletion-burst duration, deleted/inserted
character totals, replacement latency, and locus distance. Words such as
“hesitant,” “confident,” or “stuck” remain AI hypotheses and must cite those
measurements.

### FINDING-006 — Spaces presents an empty canvas without an empty-state explanation

- Impact: Medium
- Category: Feedback / empty state
- Status: Deferred
- Evidence: [`spaces-before.jpeg`](screenshots/spaces-before.jpeg)

I notice a user with no matching pins sees controls, attribution, and a dark map
but no statement about whether data is loading, the current precision has no
zines, or the relay request failed. A small non-blocking map overlay should name
the current state and disappear as soon as pins render.

### FINDING-007 — Dense chrome crosses into low-legibility text

- Impact: Medium
- Category: Typography / responsive layout
- Status: Deferred
- Evidence: [`times-before.jpeg`](screenshots/times-before.jpeg), [`web-fallback-mobile.png`](screenshots/web-fallback-mobile.png)

I notice Times, settings metadata, and the mobile-width rail use type well below
ordinary reading size. The density is intentional and appropriate for an
instrument, but several labels become deciphering tasks rather than scanning
aids. The fixed rail also consumes a disproportionate share of a 390px viewport.
A dedicated compact-layout pass should preserve information density while
raising critical text contrast and collapsing the rail by default at narrow
widths.

### FINDING-008 — Generic transitions are applied too broadly

- Impact: Polish
- Category: Motion
- Status: Deferred
- Evidence: [`first-impression.png`](screenshots/first-impression.png)

The initial browser shell reports 95 elements with `transition-property: all`.
The rendered interface is calm, but broad transitions make future property
changes unpredictable and do not express hierarchy. Limit transitions to the
specific color, border, opacity, or transform properties that communicate a
state change.

## Quick Wins

1. Rename the primary `Models` destination to `AI`. **Fixed.**
2. Localize editor diffs to changed words and punctuation. **Fixed.**
3. Surface first-annotation analysis failures instead of returning no UI. **Fixed.**
4. Add `No zines at this precision in view` to an empty Spaces result.
5. Replace broad `transition: all` declarations with explicit properties.

## Interaction Flows

### Trace-aware Step

1. I create a disposable file and Step a baseline sentence. Orientation is
   immediate and the Step feedback is clear. Goodwill `70 → 80`.
2. I replace one word through real backspaces and Step again. No mark appears;
   under the current oscillation policy that is expected, but under the stated
   deletion-first V1 it is a silent miss. Goodwill `80 → 65`.
3. I open `Diff vs prev`. The entire line is replaced visually, obscuring the
   exact relation. Goodwill `65 → 60`.
4. I open Prompt Inspector. It exposes the exact frozen prompt, full context,
   recent directory log, intervals, and character deltas. Goodwill `60 → 70`.
5. I perform three substantial same-locus deletion/replacement bursts. The
   exact stored Step has 68 transactions and the current detector accepts it.
   The first native audit window still shows no mark; later inspection proves
   that window was a stale packaged build. The current rebuilt bundle requires
   vault unlock before this visual path can be rerun. Goodwill remains `70`.

```text
Goodwill: 70 █████████████████████░░░░░░░░░
  Step 1: Find Press and Step       70 → 80  (+10 obvious core task)
  Step 2: Single rewrite            80 → 65  (-15 no deletion mark in stated V1)
  Step 3: Read local difference     65 → 60  (-5 whole-line comparison)
  Step 4: Inspect exact AI context  60 → 70  (+10 unusually transparent evidence)
  Step 5: Qualifying rewrite        70 → 70  (native result invalidated by stale build)
  FINAL: 70/100 — HEALTHY, WITH ONE CORE SEMANTIC GAP
```

### Cross-surface consistency

The Press, Stacks, Times, Spaces, Keys, AI, Networks, Docs, and Prompt Inspector
share the same rail, restrained tokens, dense mono chrome, and editorial body
voice. Keys earns its object cards; the rest mostly avoid card-grid drift. The
experience is consistently an instrument rather than a dashboard or chat app.

## Scores

### Baseline

- Design Score: **B−**
- AI Slop Score: **A** — distinctive editorial instrument; no template-dashboard drift

| Category | Grade |
| --- | --- |
| Visual hierarchy | A− |
| Typography | B− |
| Color | A |
| Spacing and layout | B |
| Interaction clarity | B− |
| Content quality | C+ |
| Consistency | B+ |
| Accessibility | B− |
| Motion | C |
| Performance feel | B− |

### Final

- Design Score: **B**
- AI Slop Score: **A**

| Category | Grade | Change |
| --- | --- | --- |
| Visual hierarchy | A− | — |
| Typography | B− | — |
| Color | A | — |
| Spacing and layout | B | — |
| Interaction clarity | B | ↑ |
| Content quality | B | ↑ |
| Consistency | A− | ↑ |
| Accessibility | B− | — |
| Motion | C | — |
| Performance feel | B− | — |

## Summary

- Total findings: 8
- Fixes: 1 verified, 2 best-effort, 0 reverted
- Deferred: 5
- Design score: **B− → B**
- AI Slop score: **A → A**
- Design-fix risk: 20% (four TSX files across three isolated fixes; no reverts)

PR summary: Design review found 8 issues and fixed 3. Design score B− → B;
AI slop score A → A.

## Verification and cleanup

- `npm test`: 1,531 passed, 0 failed
- `npm run typecheck`: passed
- `npm run build`: passed
- `npm run tauri build -- --debug`: passed; current `.app` and `.dmg` produced
- Final read-only browser audit: current `AI` navigation verified; 387ms cached
  local load; expected read-only key errors remain
- The disposable `design-review-trace.md` audit zine remains in the `metanoos`
  vault because the rebuilt app returned to the lock screen. Removing it through
  the product's recoverable Oblivion flow requires the vault owner to unlock the
  app; the audit did not request or handle that passphrase.

## Recommended Gate 2

The repository does not currently define a formal Gate 2. The next useful gate
is a product-loop gate:

> From an ordinary stepped writing session, Zine deterministically presents
> every deletion divergence, preserves the exact main/alternate relation and
> rhythm measurements, and prepares an inspectable AI request containing the
> full citable log plus a bounded latest-activity projection.

Pass criteria:

1. a single backspace produces one elementary deletion mark;
2. a contiguous deletion burst reports its exact character count and duration;
3. the mark relates deleted text to the surviving replacement at the same
   locus without whole-line noise;
4. the latest-activity block is deterministic and bounded, while the full log
   remains available for citation;
5. AI interpretation distinguishes measurement from inference and cites exact
   log entries;
6. a user can replay, inspect, dismiss, or account for the resulting annotation
   without changing the editorial tree.
