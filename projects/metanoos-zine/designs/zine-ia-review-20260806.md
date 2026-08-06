# Zine IA + flow review — 2026-08-06

Scope: pre-implementation review of layout, structure, and flow for essay writing
with LLM assistance. Source-grounded (no live site audit).

## What exists today

- Shell: vault → sidebar directory tree → workspace **panels** (split panes) → **tabs**
  (`workspace.ts:1-33`, `WorkspaceSidebar.tsx`, `WorkspacePanels.tsx`)
- Document tab: replay toolbar + CodeMirror body + typed margin rail
  (`DocumentWithMargin.tsx:799-1080`)
- Apparatus: 6 kinds, span/document/removal/membership anchors, `parentItemId`
  threading, honest anchor degradation (`apparatus.ts`)
- Margin thread click → opens a `conversation` **panel beside** the document
  (`workspace.ts:95-148`, `ApparatusConversation.tsx`)
- ActionPalette: context bar + prompt + one submit button per configured model
- Threads ALSO listed in sidebar under their file (`marginThreadsByResourceId`)

The proposed model is ~70% built. The remaining work is subtractive, not additive.

## Verdict on the core instinct

Correct, and it is the differentiator. Coding harnesses need a task registry because
work spans files. Essays do not: the artifact is one document and the unit of work is
a passage. The passage IS the task. Span-anchored threads are the right primitive, and
the hard part (position identity with honest degradation) is already built.

Keep: file → threads-in-margin as the primary model.
Do not build: project → task hierarchy.
Add later: one vault-level "open threads" triage view, when a folder of essays exists.

## Findings

### F1 (high) — "Step" is under-specified in the way that decides the product
Three sub-decisions hide inside one button:
- **Scope**: whole doc re-litigates dismissed annotations. Should be the *delta since
  last step*. The event journal already computes this.
- **Fan-out**: firing all 5 configured models produces 5x annotations on the same
  passage. Default to 1-2 voices, opt-in per step.
- **Lens**: "give feedback" produces AI slop annotations ("Consider expanding this
  point"). The step needs a named reading discipline.

Recommended: a step is a **named revision pass**, ordered on the real editing ladder:
Argument → Structure → Evidence → Line. The button is not "get feedback," it is
"run the next pass." Solves lens, bounds step count, and gives margin cards
meaningful labels.

### F2 (high) — Margin saturation is the failure mode
Google Docs annotations work because humans write ~5 per page. An LLM writes 20.
Three steps into a 2000-word essay the rail is a wall.

Fix: each step produces ONE margin card ("Step 3 · Structure · 6 observations") that
expands into the individual anchored quote-replies. Vertical density becomes bounded
by step count, not observation count. `parentItemId` already supports this.

### F3 (high) — Conversation-in-a-side-panel contradicts your own stated principle
README: the margin rail exists so apparatus projects "without changing the body's line
wrapping." But `openConversationBesideSource` reflows the body to half width, changing
measure mid-thought. That is the IDE metaphor leaking into a writing surface.

Fix: threads expand in place (widening the rail) or overlay anchored to the passage.
The body never moves.

### F4 (medium) — Threads have two homes
`marginThreadsByResourceId` nests threads in the sidebar tree AND the margin. Two
homes for one object. Margin is the home (spatial, anchored); sidebar is navigation
("where am I in the vault"). Cross-document review is a third view, not a tree node.

### F5 (medium) — Replay owns prime real estate for a rare mode
Scrub, play, and speed controls sit above the body on every document open. Replay is a
*review* mode, not a *writing* mode, and the placement signals "forensic tool" over
"writing surface." Make replay one of the `zine-document__modes` toggles.

### F6 (medium) — Ten navigational concepts
vault, folder, file, panel, tab, thread, margin item, mount scope, shield, query scope.
Ulysses has three. Trunk test fails: dropped into the app you cannot name each region's
job in 2 seconds. Panels are the first thing to cut for the writing surface.

### F7 (polish) — Un-anchored feedback has no home
The most valuable editorial note ("this essay does not know what it is about") has no
span. `DocumentAnchor` handles the data; the UI should render it as a pinned header
card at the top of the rail, not floating in it.

## Proposed shell

```
┌──────────────────────────────────────────────────────────────────┐
│ ⌘K                          Essay title                        ⚙ │
├──────────┬────────────────────────────────────┬─────────────────┤
│          │                                    │  · Step 3       │
│  files   │   BODY — fixed 66ch measure        │  · Step 2       │
│ (⌘\ to   │   never reflows                    │  · Step 1       │
│  hide)   │                                    │                 │
│          │                                    │  (dots while    │
│          │                                    │   writing;      │
│          │                                    │   opens on step)│
├──────────┴────────────────────────────────────┴─────────────────┤
│  [Argument] [Structure] [Evidence] [Line]      ⌘↵ Step          │
└──────────────────────────────────────────────────────────────────┘
```

## Proposed flow

1. **Write** — margin collapsed to a gutter of dots. No palette, no replay toolbar.
2. **Step** (⌘↵) — fires the selected pass against the delta since last step. Rail opens.
3. **Respond** — per annotation: dismiss / reply (spawns thread) / apply. Dismissal is
   as important as reply: it keeps the margin clean and it is training signal.
4. Collapse back to write.

Everything else (mount scope, models, relays, keys) is settings and belongs behind the
shell, not in it.

## Open decision

What a step means: fixed revision ladder vs. free-form prompt vs. user-defined lenses.
