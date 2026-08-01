# Design: Zine Composition Replay — The Full Making of a Folder

**Status:** DRAFT FOR SPEC REVIEW  
**Mode:** BUILDER — PERSONAL INSTRUMENT  
**Date:** 2026-08-01  
**Repository:** `metanoos/zine`  
**Branch:** `codex/dogfood-ui`  
**Owner and first writer:** Peter Wei  
**Supersedes:** `/Users/peterwei/.gstack/projects/metanoos-zine/peterwei-codex-zine-design-20260726-090720.md`  
**Approved wireframe:** `/Users/peterwei/.gstack/projects/metanoos-zine/designs/composition-replay-wireframe-20260801-094605.html`

## Decision

Broaden Zine from deletion-first Ghost playback into **Composition Replay**: a truthful reconstruction of the whole text-composition process, including insertion, late insertion, deletion, replacement, undo/redo, settled selections, folder membership, and the logical multi-column workspace in which those actions occurred.

The default view is a **Composition Map** over the full current text. The prose remains static and fully readable while restrained trails show how surviving text arrived, where alternatives were removed or replaced, and which selections settled long enough to matter. Historical text is never fuzzily overlaid onto final-text geometry. Evidence that cannot map exactly to surviving positions moves to an explicit margin lane.

**Exact Replay** is one switch away. It reconstructs the document, folder membership, selections, columns, tabs, and active resources at each recorded frontier. “Exact” refers to authored bytes, event order, stable text identity, membership, and logical workspace state. It does not claim historical pixels, scroll offsets, panel widths, typing cadence, or window geometry.

One shared, versioned Composition Timeline compiler feeds both views. Files and folders are first-class replay scopes. A folder replay is the federated chronology of every action taken within that folder and its descendants, rendered across the editor’s multiple columns and per-column tab lists without copying descendant events into ancestor traces.

The manual **Step** mechanic is removed outright. No legacy Step reader, migration, storage, UI, or protocol compatibility is required. Exact trace frontiers and purpose-specific concepts replace every job Step previously performed.

## Problem Statement

Ordinary revision history makes the writing process a destination away from the present document. It replays old states after the writer leaves the current text, and it usually treats revision as a sequence of snapshots rather than a composition with surviving lineages, discarded branches, delayed returns, and movement across files.

Zine already records exact insertions, deletions, replacements, undo, redo, stable scalar identities, voices, and durable resource heads. But the product center and playback vocabulary remain deletion-first: Ghosts are the visible history, and manual Steps are the navigation landmarks. That model omits much of what makes composition meaningful:

- a sentence introduced late after its surroundings had stabilized;
- a phrase moved from notes into an essay;
- a replacement that changed the argument’s direction;
- a settled selection that shows what the writer was working on before the next authored action;
- a folder composed through actions across several files; and
- the actual logical workspace of columns and tabs in which those files were considered together.

Overlaying historical glyphs directly on the final text does not solve this. Earlier states had different line breaks and geometry. Once surrounding text changes, a literal overlay either drifts or pretends to precision the trace does not possess.

The product needs two complementary truths:

1. **How the current work came to be**, visible without leaving the current text.
2. **What the work actually was at an earlier frontier**, reconstructed without forcing that state into current geometry.

## What Makes This Cool

The current manuscript remains sovereign. Zine does not turn writing into analytics, a miniature DAW, or a forensic dashboard. At rest, the writer sees the complete present text. Its history is nearby as faint physical evidence. During playback or direct inspection, paths briefly illuminate: an insertion blooms from its stable anchor, a deletion peels into the margin, a replacement reveals the path between alternatives, and a settled selection appears as a quiet envelope.

A folder becomes a composition rather than a container. Playing **Writing Under Observation** can move from field notes to fragments to the main draft, open the tabs that were logically present, focus the column where the next action occurred, and show the phrase that crossed from one file into another. The writer sees the making of the work across files without any event changing ownership or being stored twice.

This is the product distinction: existing history tools make the past a place you visit; Zine makes composition history a truthful layer of the present while retaining exact-state replay underneath.

## First Lake

Use one authentic authored folder for **Writing Under Observation**. It contains the main essay and the real notes, fragments, sources, or conversations used to compose it.

The first lake is complete only when it demonstrates:

- exact file replay from the first action to current material;
- Composition Map and Exact Replay from the same compiled timeline;
- insert, late-insert, delete, replace, undo, redo, and settled-selection evidence;
- multi-file folder replay in global journal order, including honestly labeled cross-file recurrence;
- historical folder membership and logical column/tab reconstruction;
- explicit unavailable states for partial history or unmappable evidence;
- normalized playback without raw action timing; and
- a fixed-height transport that remains fixed regardless of descendant count.

This is not yet a corpus-wide resident replay engine or a public history format. It is one authentic folder-scale instrument built on architecture that does not need to be discarded when the scope grows.

## Constraints

### Truthfulness

- Current material remains byte-exact CommonMark.
- Stable position identities, never offsets or fuzzy matching, determine surviving mappings.
- Historical glyphs never pretend to align with final layout when their identities do not survive there.
- Unmappable deletions, replacements, and selections remain visible with an explicit reason.
- Exact Replay reflows exact historical text into current panel geometry and says so.
- Composition Map and Exact Replay must resolve the same compiled event identity at the same playhead.

### Privacy

- No keystroke-adjacent timestamps or pause durations are stored.
- No scroll offsets, pixel coordinates, viewport dimensions, panel widths, or window geometry are stored.
- Raw cursor motion is not stored.
- Logical workspace observations and settled selections are private local evidence stored as a separate non-authored event family inside the encrypted journal. They never advance authored resource heads.
- The observation stream is excluded from model context, publication, relay transport, and export by default. No disclosure control for it is part of this lake.

### Writing experience

- Backspace, typing, IME composition, tab changes, and selection cannot wait for replay projection work.
- Map animation is quiet and interruptible. At rest, the map settles into a faint static state.
- Reduced-motion mode replaces moving trails with state changes and static leaders.
- The full current text stays readable and selectable.
- The fixed transport never becomes one permanent row per resource.

### Existing architecture

- The append-only encrypted journal remains authoritative.
- Resource events keep their actual resource identity.
- Files retain material traces; folders retain ordered membership traces.
- Descendant events do not bubble into ancestor traces.
- The projections layer remains disposable and is the natural home for timeline compilation.
- Existing stable position identities, deterministic reducers, source mapping, folder membership, and column/tab UI are reused.

## Premises

1. **Composition Replay is the parent concept.** Ghosts remain disposition-aware discarded alternatives within the broader record of insertion, replacement, selection, movement, and workspace change.
2. **One evidence model feeds both views.** The Map cannot have a separate interpretation of history from Exact Replay.
3. **Files and folders are replay scopes.** A file replay follows one resource. A folder replay federates resource-local events through historical containment and global journal order.
4. **Logical workspace state matters.** Columns, tab lists, active tabs, tab moves, and settled focus are part of the private composition observation record.
5. **Physical workspace state does not.** Pixels, scroll, viewport, panel width, and transient focus are not captured.
6. **There are no manual Steps.** Authored actions are the chronology; exact frontiers identify stable states.
7. **There is no Step compatibility obligation.** This is a pre-release personal instrument. The new schema may reject or discard development-era Step traces.
8. **Timing is normalized.** Event order is exact; playback cadence is a presentation choice.
9. **Present-text geometry is not historical geometry.** Surviving identities may map to the current manuscript. Deleted, displaced, or unavailable branches use an honest margin lane.
10. **The default transport has constant height.** Detailed resource lanes are an on-demand inspection surface.

## Cross-Model Perspective

The external landscape reinforced the product distinction:

- Google Docs, Draftback, and Etherpad primarily replay earlier document states in a separate history mode.
- ProseMirror’s immutable steps, selections, and mappings validate the pattern of storing exact semantic actions and deriving views from them.
- Research on organic revision visualization demonstrates the value of insertion/deletion graphs and revision density, while also documenting overlap and scalability problems when every branch becomes a persistent visual object.

Two independent visual reviews converged on a manuscript-first direction:

- Treat Zine as a writer’s light table or palimpsest desk, not a timeline dashboard.
- Keep present prose dominant; reveal history through restrained illumination, leaders, underlines, margin fragments, and selection envelopes.
- Do not directly overlay duplicate historical glyphs on current glyphs.
- Keep the activity drawer optional and overlay it rather than resizing the writing surface.
- Avoid synthetic chapters after removing Steps. Pauses, automatic checkpoints, and resource switches may affect density, but they do not become authored landmarks.

One suggested interaction—**hold to expose lineage**—fits the evidence model but is not required for the first lake. Pressing and holding a surviving phrase would temporarily quiet unrelated history and reveal only that phrase’s insertion, replacements, deleted alternatives, moves, and settled selections.

## Approaches Considered

### Approach A: Renderer-first prefix player

The UI reads raw trace records, repeatedly reduces prefixes, and computes trails as it renders. Settled selections live in a small sidecar list.

This is the shortest prototype but makes the UI responsible for trace semantics, risks repeated prefix-reduction cost, and allows Composition Map and Exact Replay to drift. It is rejected as the product architecture.

### Approach B: Shared Composition Timeline compiler

A pure compiler consumes resource events plus a private logical-workspace observation stream and emits one immutable, versioned Composition Timeline. Both Map and Replay render that timeline.

This is chosen. It is the smallest coherent architecture that makes two views, file scope, folder scope, selection, workspace replay, normalized timing, unavailable states, and future performance work agree by construction.

### Approach C: Composition scene graph

The primary representation becomes an animated scene graph of text objects and lineages. It offers expressive cinematic playback but duplicates layout semantics, complicates accessibility and selection, and risks becoming a second source of truth.

It is rejected for the first lake. Scene-graph techniques may later render particular trail effects, but never become the authority.

## Recommended Architecture

```mermaid
flowchart LR
    J["Encrypted global journal"] --> V["Verified ordered prefix"]
    V --> R["Resource-local authored events"]
    V --> O["Private workspace observations"]
    R --> C["Composition Timeline compiler"]
    O --> C
    C --> T["CompositionTimelineV1"]
    T --> M["Composition Map"]
    T --> X["Exact Replay"]
    T --> A["Activity drawer"]
    M --> U["Multi-column Zine workspace"]
    X --> U
    A --> U
```

### Authority boundaries

There are three distinct layers:

1. **Authored resource events** change file material or folder membership and advance that resource’s head.
2. **Private composition observations** are encrypted, globally ordered journal envelopes that describe settled selection and logical workspace state without entering authored resource traces or advancing any authored resource head.
3. **Composition Timeline** is a disposable deterministic projection over a verified journal prefix.

Neither UI geometry nor a compiled timeline is authoritative. Rebuilding from the same verified prefix and compiler version produces the same timeline.

### Authored event families

File traces contain:

- `text.insert`
- `text.delete`
- `text.replace`
- `history.undo`
- `history.redo`
- attributed model material actions through the same text primitives

Folder traces contain typed structural events:

- membership insert
- membership remove
- membership move
- membership reorder
- child rename where the folder’s authored arrangement changes

The exact event names may follow current workspace-journal conventions, but they must be expressible as resource-local ordered events that the shared compiler can consume. Folder events do not masquerade as text records.

### Private observation families

The separate observation lane contains:

- `selection.settled`: resource, stable anchor range, direction, and bound authored frontier;
- `tab.opened` and `tab.closed`;
- `tab.activated`;
- `tab.moved`: source column and destination column;
- `column.opened` and `column.closed`;
- `column.focus.settled`; and
- optional observation-unavailable markers when capture was disabled or interrupted.

Observations receive the same durable global journal sequence as every other journal envelope and bind the exact authored resource heads visible when captured. They do not carry raw duration. Repeated transient events coalesce before persistence.

Selection capture occurs:

- after a short quiet interval when the anchored selection remains unchanged;
- immediately before the next authored edit, model dispatch, resource switch, tab move, or column transition; and
- after IME composition has committed, never from provisional composition text.

Direction is retained because anchor-to-focus order communicates how a selection was made. Pixel rectangles are derived at render time and never stored.

### Observation epochs, baseline, and lifecycle

Logical workspace replay is divided into capture epochs. Every epoch begins with a mandatory `workspace.snapshot` journal envelope bound to the current verified journal head. The snapshot contains logical column IDs and order, tab IDs and in-scope resource bindings, the active tab per column, settled focused column, and any settled selection. It contains no dimensions or scroll state.

An epoch ends with `workspace.capture.ended`. Normal shutdown records `reason: disabled | workspace_closed`; recovery after a crash begins the next epoch with `workspace.capture.interrupted`, pointing to the last valid observation sequence. A gap between epochs is always rendered as unavailable rather than reconstructed from current UI state.

V1 capture is enabled for the private authoring vault while Composition Replay is enabled. Observation envelopes:

- use the journal’s existing authenticated encryption and integrity chain;
- are included in encrypted local recovery archives and verified snapshots;
- are excluded from search indexing, model context, publication, relay Commit construction, and content exports;
- are retained until the user explicitly erases them; and
- are removed only through the existing authored-redaction mechanism, leaving an explicit unavailable interval in replay.

Disabling capture closes the current epoch. Re-enabling it starts a new baseline snapshot. Clearing observations is a destructive privacy action requiring explicit confirmation; it never deletes authored material events.

Settled selection capture uses a V1 quiet interval of 500ms. The UI creates an in-memory ordering token synchronously with the stable selection anchors. Quiet-interval observations persist asynchronously. Immediately before an authored edit, the pending observation and authored event are appended in one atomic journal transaction, giving the observation the preceding global sequence without an additional input-path fsync. A crash may lose an unpersisted quiet-only selection, but it cannot reorder a boundary selection relative to the authored action; the next epoch marks any interruption.

### Normative V1 schema

The following is the minimum semantic shape. Concrete repository brands may refine the scalar types but may not omit these fields.

```ts
type GlobalSequence = number;
type TimelineCursor = { sequence: GlobalSequence; eventId: EventId };

interface TimelineManifestV1 {
  schema: "zine-composition-timeline-v1";
  scopeResourceId: ResourceId;
  scopeKind: "file" | "folder";
  verifiedJournalHead: EventId;
  verifiedJournalSequence: GlobalSequence;
  compilerConfigHash: string;
  initialScopeFrontier: ScopeFrontierV1;
  frames: readonly TimelineFrameV1[];
  keyframeIndex: readonly TimelineKeyframeRefV1[];
}

interface TimelineFrameV1 {
  cursor: TimelineCursor;
  source: { eventId: EventId; resourceId: ResourceId | null; kind: string };
  before: ScopeFrontierV1;
  after: ScopeFrontierV1;
  authoredAction: AuthoredReplayActionV1 | null;
  observation: WorkspaceObservationV1 | null;
  mapEvidence: readonly MapEvidenceV1[];
  normalizedDurationMs: number;
  unavailable: UnavailableReasonV1 | null;
}

type UnavailableReasonV1 =
  | "capture_disabled"
  | "capture_interrupted"
  | "damaged_journal"
  | "missing_material_dependency"
  | "missing_membership_dependency"
  | "redacted_evidence"
  | "unmappable_position"
  | "outside_scope";

interface WorkspaceSnapshotV1 {
  epochId: string;
  columns: readonly {
    columnId: string;
    order: number;
    tabs: readonly { tabId: string; resourceId: ResourceId; active: boolean }[];
  }[];
  focusedColumnId: string | null;
  selections: readonly SettledSelectionV1[];
}
```

Canonical timeline serialization uses the repository’s pinned canonical JSON rules: fixed field order, UTF-8, no insignificant whitespace, ordered arrays, and lowercase hexadecimal hashes. The `compilerConfigHash` binds every semantic classifier default, coalescing rule, duration rule, and schema version. Cache placement, keyframe compression, and LRU state are excluded because they cannot change semantic frames.

### Cursor and frame semantics

- Cursor zero is the scope’s initial reconstructed state before the first participating replay-worthy envelope.
- Cursor `N` names exactly one journal envelope by global sequence and event ID.
- The stable state at cursor `N` is the **post-event** `after` frontier.
- Animation for cursor `N` interpolates only between its `before` and `after` projections; event application is atomic.
- Previous and Next move by one replay-worthy envelope. They never target an ambiguous coalesced group.
- Scrubbing resolves to one exact cursor. Visual coalescing may animate consecutive events together during Play, but the selected cursor and source event remain singular.
- Switching Map ↔ Exact Replay preserves the cursor.
- Map keeps current material fixed and renders evidence associated with the selected cursor; Exact Replay renders the post-event historical state at that cursor.

### Deterministic V1 classifiers

- **Visible deletion floor:** at least one Unicode letter/number run or 12 Unicode scalars.
- **Deletion survival:** the deletion remains effective through 10 subsequent authored actions in that file or through the verified head, whichever comes first. Undo before that boundary classifies it `reverted`.
- **Late insertion:** both surviving neighbor lineages predate the insertion by at least 50 authored actions in the same file. Start/end sentinels qualify only when the other neighbor meets the threshold.
- **Replacement:** comes only from an atomic `text.replace`; the compiler never infers replace from unrelated delete/insert events.
- **Cross-file recurrence:** the exact case-sensitive normalized scalar sequence of a visible deletion appears in another in-scope file after the deletion. It is labeled `recurs in`, never `moved`, and carries no intent claim.
- **Retried:** deferred from V1. Repeated removals remain separate deletion evidence until a later classifier specification defines it.
- **Display coalescing:** consecutive global envelopes may share one Play animation only when they are the same action kind, resource, voice, and input transaction, and no authored, structural, or observation envelope intervenes.
- **Mappability:** `exact_range` when every referenced surviving position is present; `honest_anchor` when at least one stable boundary lineage survives; `margin_only` otherwise. No fuzzy-text fallback exists.
- **Normalized duration:** insert/delete 280ms, replace 420ms, selection/workspace observation 220ms, resource switch 360ms, membership transition 500ms. Play-speed controls multiply these values without altering the manifest.

These values are V1 defaults, not hidden heuristics. Any later tuning produces a new configuration hash and reproducible recompile.

### CompositionTimelineV1

The compiler emits an immutable intermediate representation containing:

- scope resource and scope kind;
- compiler and schema versions;
- verified journal boundary;
- ordered frames with global sequence and resource-local identity;
- exact affected resource frontier before and after each authored action;
- material action kind and stable position lifetimes;
- folder membership state at each structural frontier;
- logical columns, tab lists, active tabs, and settled focused column;
- settled selection episodes;
- normalized display duration and coalescing group;
- derived lineage: inserted, deleted, replaced, moved, retried, or late-added;
- current-text mappability and explicit unavailable reason;
- keyframes for bounded seeking; and
- provenance back to source event IDs.

Derived labels such as “late insertion” or Ghost disposition are projection results, not stamped authored facts. Changing a threshold recompiles the timeline without rewriting the journal.

### Normative miniature fixture

Given folder `F`, file `N` (`notes.md`), and file `D` (`draft.md`), the verified journal contains:

| Global sequence | Envelope |
|---:|---|
| 10 | atomic membership insert `N` into `F` |
| 11 | workspace epoch snapshot: column `left`, active tab `N` |
| 12 | `N` inserts `A sentence worth keeping.` |
| 13 | atomic membership insert `D` into `F` |
| 14 | tab `D` opens and activates in column `right` |
| 15 | `N` deletes `worth keeping` using exact scalar IDs |
| 16 | `D` inserts the same normalized scalar sequence with new resource-local IDs |

The compiled folder output must have:

- cursor zero: `F` before sequence 10;
- cursor 10: `N` is a member and appears with its entry-frontier state;
- cursor 12: Exact Replay shows the post-insert `N`; Map keeps current `N` material and highlights the source insertion identities;
- cursor 14: Exact Replay reconstructs two columns with `D` active on the right;
- cursor 15: Map places the exact deleted payload at `N`’s last honest anchor;
- cursor 16: Map emits `recurs in draft.md`, not `moved`, because the records do not prove intent or transfer; and
- manifest heads: the verified journal boundary, `F` membership head, and exact heads of `N` and `D`.

Compiling file scope `N` from the same prefix omits membership and `D` events, retains cursor identities 12 and 15, and produces the same post-event `N` text at those cursors. This fixture becomes a canonical compiler golden alongside damaged-membership, interrupted-observation, undo/redo, IME, and redaction fixtures.

### Indexed compilation and seeking

Compilation has two deterministic phases:

1. **Index:** scan verified envelope metadata to build per-resource event ranges, historical membership intervals, observation epochs, and global-sequence lookup. For every resource that enters scope after its creation, record the prefix frontier needed to reconstruct its entry state.
2. **Compile:** reduce only the required resource prefixes, merge participating envelopes by global journal sequence, and emit semantic frames. Existing verified material checkpoints may seed resource reduction, but their placement never changes semantic output.

V1 uses a fixed keyframe rule included in compiler configuration: one delta keyframe every 256 replay-worthy events and at every resource-entry membership transition. Keyframes use immutable per-resource states and structural sharing; they do not copy the whole folder. A bounded LRU retains decoded resource states and evicts by byte cost.

Seeking starts from the nearest prior semantic keyframe. Performance caches and eviction state are not serialized into `CompositionTimelineV1` and cannot affect byte-identical semantic output. The activity drawer virtualizes rows and filters without changing the underlying chronology.

## Folder Federation

A folder replay is a projection over the vault’s historically reconstructed resource tree and its resource-local traces, not a new trace.

The topology contract is the existing exclusive ordered-containment model:

- every authored file or folder has exactly one parent folder at a valid frontier;
- the vault root has no parent and is not an authored replay scope;
- cycles and self/descendant moves are invalid;
- links or aliases do not create containment and are not descendants for replay;
- membership insert/remove on both affected parents and the child parent pointer occur in one atomic transaction;
- same-folder reorder is one typed atomic reorder event;
- deleting or moving a resource to Oblivion ends its participation after that membership frontier but does not erase prior folder replay;
- restoring it creates a new membership interval; and
- a concurrent or partial membership transaction makes containment unavailable from that frontier until an authored resolution exists.

For each journal event, the compiler reconstructs historical containment. An authored or observation event participates in folder scope only if its resource was a descendant of that folder at that global journal sequence.

Consequences:

- A file moved into the folder appears at the move frontier with its then-current exact state. Its earlier actions do not appear as though they happened inside the destination folder.
- Subsequent actions participate until the file moves out.
- A nested folder moved in brings its current descendant states at that frontier; later descendant actions participate normally.
- The folder’s own membership event remains the only authoritative move record.
- No descendant event is copied into the folder trace.
- Moving a resource later cannot rewrite the history of where earlier actions occurred.

Global journal sequence supplies the cross-resource replay order. Resource-local causality remains authoritative within each trace. If the verified prefix cannot establish an unambiguous order or containment state, folder replay stops at the last complete frontier and says why.

Resource-local selection observations participate when their resource was in scope at their global sequence. Workspace-wide observations are projected, not included wholesale:

- a tab event participates only for an in-scope resource;
- column creation, closure, or settled focus participates only when the column contains an in-scope tab immediately before or after the event;
- out-of-scope tabs and their titles are omitted rather than leaked;
- when an omitted tab was active, the scoped projection records `focus outside scope` instead of activating a different tab; and
- a mixed workspace snapshot is deterministically filtered to in-scope tabs while preserving column IDs and relative order.

Thus Exact Replay is exact for the selected folder scope, not a covert replay of the whole vault.

## Removing Step Completely

Automatic checkpoints do not justify Step removal by themselves; they solve different physical problems. Step removal is safe because every acknowledged authored event already creates an exact frontier, and each former Step responsibility has a direct replacement.

| Former Step responsibility | Replacement |
|---|---|
| User-visible playback landmark | Ordered authored actions plus derived, non-semantic keyframes |
| Previous / Next | Previous / next compiled action or coalesced display frame |
| Stable current state | Exact acknowledged resource head |
| Folder stable state | Frozen scope frontier manifest of membership head plus descendant resource heads |
| Ghost settlement | Survival through a configurable number of subsequent authored actions, undo/redo state, and disposition |
| Search scope | Latest durable head or an explicitly selected resource/scope frontier |
| Citation target | Resource frontier plus stable position boundaries |
| Publication target | Explicitly frozen file or folder scope frontier |
| Relay promotion / witness target | Signed frontier or publication manifest, not a semantic Step event |
| Recovery accelerator | Existing technical material checkpoints and journal-verified snapshots |

Required removal includes:

- `StepId`, `StepLandmark`, `currentStepId`, `stepCount`, and `step` trace records;
- Step creation commands and buttons;
- Step-aware reducer and writer invariants;
- Step encoding and Step-indexed fields inside material checkpoints, fixtures, and recovery validation;
- Step-driven Ghost survival;
- Step-bound search, citation, publication, and retry logic;
- signed Step promotion and Gate 2 object classes; and
- Step terminology in UI, documentation, tests, and protocol claims.

This is a schema break. Startup and import deterministically reject any Step-bearing trace with `unsupported_step_schema`; nothing is silently discarded or reinterpreted. Because there is no legacy-support requirement, the only recovery path is an explicit developer-authorized new-vault reset after any desired raw export. Technical Commit, material checkpoint, and journal-snapshot mechanisms remain, but they lose every Step-indexed field and remain invisible physical persistence mechanisms rather than authored history.

### Scope frontiers

A file frontier is its exact resource head.

A folder scope frontier is a deterministic manifest binding:

- folder resource ID and membership head;
- ordered direct membership at the frontier;
- recursively included descendant resource IDs and their exact heads;
- conflict or unavailable status for every included resource;
- visibility and disclosure policy; and
- a domain-separated hash of the canonical manifest.

Publication, folder citations, and signed durability bind this manifest. They do not require a user to create a landmark before acting.

## Composition Map

Composition Map is the default Read presentation and the quiet history layer in Write.

### Write

- The current edit or recent action may leave a short active trail.
- The trail settles quickly into the faint static map.
- Writing input never waits for map compilation or animation.
- The author can hide the history layer without disabling capture.

### Read

- The full current text is the default.
- Play and scrub animate the history layer over that text.
- Exact Replay is one switch away.
- The map remains a projection over present geometry and labels its limits.

### Visual grammar

- Surviving insertions: fine underlines, gutter-to-range leaders, or brief illumination.
- Late insertions: the same stable mapping with a distinct restrained cadence or mark, never a permanent alarm color.
- Deletions: faded exact removed text in the nearest honest margin lane.
- Replacements: paired removed and inserted evidence.
- Settled selections: translucent envelopes over surviving ranges.
- Unmappable selections: margin brackets with the unavailable reason.
- Cross-file recurrence: an explicitly inferred leader stating that the same normalized scalar sequence appears elsewhere; it never claims a proven move unless a future atomic transfer event provides that evidence.

The first aesthetic pass may start from the external review’s “palimpsest desk” palette:

- warm paper `#F4F0E7`;
- manuscript ink `#24221F`;
- graphite `#777169`;
- rules `#D8D1C5`;
- active history ultramarine `#526FA8`;
- deletion oxide `#A9695B`; and
- selection wash `#DCE3EF`.

This palette is a design-review starting point, not an approved replacement for the repository’s existing visual system. The approved decision is the interaction hierarchy and manuscript-first restraint.

## Exact Replay

Exact Replay advances through compiled frames and reconstructs:

- exact material bytes for every visible file;
- folder membership at that frontier;
- logical column count and order;
- each column’s tab list and active tab;
- the settled focused column;
- settled selections; and
- the action currently being applied.

It deliberately does not reconstruct:

- historical line wrapping or page coordinates;
- scroll positions;
- window size;
- panel widths;
- cursor motion between settled selections;
- time between keystrokes; or
- the user’s attention or intention.

If more logical columns existed than the current viewport can legibly display, replay preserves column identity and order while adapting presentation through horizontal access or temporary compression. It must say that geometry has been reflowed rather than silently implying a pixel-perfect recording.

### Mode behavior matrix

| Scope and mode | Material shown | Membership shown | Tabs and columns | Selection | Editing |
|---|---|---|---|---|---|
| File · Map · Live | Current file | Current parent context only | Current workspace | Selected historical evidence over current text | Enabled |
| File · Map · Pinned | Current file | Current parent context only | Current workspace | Evidence at exact cursor over current text | Read-only until Return to Live |
| File · Exact Replay | Post-event historical file state | Historical parent label when available | Scoped recorded workspace | Historical settled selection | Read-only |
| Folder · Map · Live | Current material of currently open in-scope files | Current folder tree | Current in-scope workspace | Selected event evidence over current text | Enabled |
| Folder · Map · Pinned | Current material remains fixed | Current folder tree remains fixed | Current workspace remains fixed; affected resource is emphasized, not opened automatically | Evidence at exact cursor | Read-only until Return to Live |
| Folder · Exact Replay | Post-event historical states of visible in-scope files | Historical folder tree | Recorded scoped columns, tabs, and settled focus | Historical settled selections | Read-only |

Map never mutates current workspace arrangement while scrubbing. Exact Replay is the only mode that reconstructs historical membership and logical workspace state.

### Live updates and editing

Replay compilation binds one `verifiedJournalHead` and sequence. At Live, newly acknowledged events extend the timeline asynchronously and the Map remains editable. At any earlier cursor or in Exact Replay, the boundary is pinned and the editor is read-only.

If new events arrive while pinned, the transport shows `new actions available`; it does not rebase the cursor or alter compiled frames. **Return to Live** recompiles or extends through the newest verified head. Any attempt to type while pinned first requires Return to Live; Zine never branches silently from a historical frame. Starting an edit while Play is running pauses Play, returns to Live, and only then enables input.

## Transport and Activity Drawer

The folder transport remains roughly 72–76px and never gains one permanent row per descendant.

It contains:

- Previous, Play/Pause, and Next;
- one aggregate folder chronology;
- playhead and action index;
- current action kind and resource path;
- normalized-playback disclosure;
- `Map | Exact Replay`; and
- one **Activity** control.

The aggregate rail may show density, resource switches, structural events, and unavailable gaps without assigning a permanent color to every file.

Activity opens a searchable, virtualized drawer over the lower workspace. The drawer exposes the folder tree and detailed resource lanes. Selecting a resource filters emphasis or seeks; it does not create a different chronology. Closing the drawer returns the full editor height immediately.

## Failure and Unavailable States

Truthful replay must fail visibly rather than interpolate.

- **Partial journal prefix:** replay stops at the last verified sequence.
- **Damaged resource trace:** that resource becomes unavailable; folder replay may continue only where the missing state cannot affect containment or visible material, otherwise it stops.
- **Missing observation range:** authored replay continues with `workspace state unavailable` for the gap.
- **Unmappable current-text evidence:** evidence remains in the margin lane with its exact payload and reason.
- **Redacted evidence:** the frame preserves the authored absence and chain position but never reconstructs destroyed content.
- **Capture disabled:** the timeline marks the interval rather than implying nothing happened.
- **Reduced motion:** all information remains available through static state changes and labels.

## Open Questions

These do not change the V1 evidence or replay contracts:

1. Should hold-to-expose-lineage enter the first lake or follow after the basic Map is trustworthy?
2. Which current Zine typography and color tokens should absorb the palimpsest-desk direction during design review?

## Success Criteria

### Evidence correctness

- Recompiling the same verified prefix produces byte-identical timeline output for a pinned compiler version.
- Every Map mark and Exact Replay frame links to the same source event identity.
- Text at every replay frontier equals direct core reduction of the same resource prefix.
- Folder replay order equals global journal order and respects historical containment.
- No event is duplicated into an ancestor trace.
- Undo and redo preserve original position identities and lineage.
- IME composition creates no observation or authored record from provisional text.

### Selection and workspace correctness

- Settled selections use stable anchors and direction.
- Tab, column, and settled-focus replay reproduces the recorded logical state.
- Missing observation ranges are explicit.
- No pixels, scroll offsets, panel widths, viewport dimensions, or raw timing enter persistence.

### Product behavior

- Composition Map opens on the full current text by default.
- Exact Replay is reachable with one switch and no separate forensic workspace.
- Deleted or unmappable evidence is never fuzzily placed.
- The transport height is unchanged for one, one hundred, or one thousand descendants.
- The activity drawer remains responsive through virtualized large-folder fixtures.
- Reduced-motion mode communicates the same evidence without moving trails.

### Step removal

- New trace schemas contain no Step record or Step foreign key.
- Search, Ghost classification, citations, publication, recovery, and Gate 2 designs compile and test without Step.
- No user-facing Step command, count, label, or playback dependency remains.
- Development-era Step traces are rejected with `unsupported_step_schema`; reset is explicit and never part of open or import.

### First authentic use

- Peter completes a real **Writing Under Observation** folder cycle in Zine.
- He can identify at least one late insertion, cross-file recurrence, replacement path, deletion, and settled selection from the Map without the UI claiming that recurrence proves movement.
- Exact Replay reconstructs the multi-file sequence without coaching.
- The Map answers a composition question faster than stepping through historical states alone.

## Distribution Plan

This remains a private local personal-instrument feature for the first lake.

- No public composition replay.
- No observation-stream relay sync or export.
- No sharing of selection or workspace state.
- No reader analytics.
- No claim that the trace proves attention, intent, or human authorship.

If composition history is later disclosed, it requires a separate product decision and an explicit disclosure manifest. The private default is structural, not a checkbox that can be accidentally inherited from material publication.

## Next Steps

### Assignment

Use one authentic **Writing Under Observation** folder to prove exact action replay, logical multi-column workspace replay, settled-selection capture, and the default Composition Map from one shared Step-less timeline.

### Execution sequence

1. **Remove Step from the target model.** Define frontier replacements across core trace, checkpoints, Ghosts, search, citations, publication, and Gate 2 before building replay UI.
2. **Define resource and observation schemas.** Add typed folder trace events and a private logical-workspace observation lane without mixing observations into material causality.
3. **Build the pure compiler.** Emit `CompositionTimelineV1`, keyframes, mappability, normalized frames, and deterministic fixtures in the projections layer.
4. **Capture one authentic file.** Record settled selection and produce both Map and Exact Replay for the main essay.
5. **Federate the authentic folder.** Add historical containment, multi-column workspace reconstruction, and movement across supporting files.
6. **Build the fixed transport and drawer.** Reuse the approved wireframe hierarchy and large-resource virtualization.
7. **Dogfood and correct claims.** Compare Map against direct replay, inspect unavailable states, and remove any visual element that implies geometry, timing, attention, or intent the trace does not hold.
8. **Run architecture, design, privacy, and performance review.** Only then treat the first lake as complete.

## What I Noticed About How You Think

You repeatedly widened the object only when the narrower model stopped matching lived composition. Deletion history became composition history because late insertion and selection were equally revealing. File replay became folder replay because the work actually happens across notes, drafts, and conversations. The playbar lost permanent rows because the real folder will not remain a demo-sized folder.

You also preferred conceptual subtraction over compatibility layering. Once exact action frontiers made Steps redundant, you chose to remove the mechanic rather than hide it or preserve an internal fossil. That is a strong signal about the instrument: fewer concepts, each with one honest job.

The durable design instinct here is not “record everything.” It is **record the smallest logical state that makes composition truthful**. That is why settled selections belong, while pixels, scrolling, raw timing, and transient focus do not.
