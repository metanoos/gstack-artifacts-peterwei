# QA plan: focus-bound authoring and MODEL context onboarding

Generated on 2026-07-16
Branch: codex/headless-press
Parent plan: peterwei-codexheadless-press-eng-reviewed-plan-20260716-214203.md
Status: APPROVED

## Coverage target

The current suite has reusable foundations for roughly 5 of these 51 behavior
groups, but not complete assertions for the reviewed contract. Implementation
must finish with all 51 groups automated or explicitly blocked by a documented
manual provider gate. Source-regex UI tests may remain as fast smoke checks, but
they do not satisfy an interaction group by themselves.

    Current reusable baseline  [#####..............................................]  5/51
    Planned coverage           [###################################################] 51/51

## Test layers

- Pure Node tests: reducers, transitions, canonical hashing, budgets,
  invalidation, migration planning, and result validation.
- Component/browser tests: real click, modifier, keyboard, focus, ARIA, modal,
  replay-tab, onboarding, and crash/reload flows in Playwright.
- Tauri mock-runtime tests: desktop capability gates, Stronghold adapter,
  locked/unlocked behavior, and migration coordination without real secrets.
- Rust tests: plugin initialization helpers, safe path/salt handling, and any
  command boundary added for the vault.
- Recording integrations: canonical approved messages versus transport bytes,
  protocol-specific headers, cancellation, and stale response behavior.
- Relay integration: complete/incomplete replay, exact event recovery, Send,
  Attest, and result provenance.
- Live eval: locked prompts and rubric for all five operations against both an
  OpenAI-compatible and Anthropic provider.

## Affected pages and surfaces

- Press workspace — directory activation/selection, panels/tabs, author palette,
  mount/shield controls, token estimate, and focused MODEL operations.
- Models — provider profile, vault gating, neutral Test, and assignment to the
  MODEL voice.
- Prompt Inspector — exact context/request disclosure, budget breakdown,
  approval, invalidation, and invocation handoff.
- Trace player and replay tabs — file/folder/Coin loading, historical projection,
  exact-version Send/Attest, and live-focus preservation.
- First-run and Help onboarding — optional MODEL lesson, dismiss, restart,
  reconciliation, resume, and cleanup.
- Desktop shell — Stronghold initialization, vault unlock, migration, and
  authoring capability gates.

Zine is a single-page desktop/web client rather than a URL-routed product, so QA
should enter these surfaces through their visible navigation controls.

## Critical paths

- Focus a file, independently multi-select directory rows, then prove Step,
  player, and MODEL remain on the focused file.
- Unlock/migrate a legacy desktop profile, sign and Send a Step, invoke a MODEL,
  restart, and prove no plaintext secret remains.
- Focus, mount, shield, inspect, approve, invoke, receive a matching result,
  explicitly Step later, and replay the exact MODEL span.
- Change a request dependency after approval and prove the request cannot send.
- Change the target head while a provider response is in flight and prove no
  document mutation occurs.
- Replay an exact historical node and use local Send/Attest without moving focus
  or creating a Step.

## 1. Focus and directory selection — 15 groups

| ID | Behavior | Primary layer |
| --- | --- | --- |
| F01 | Primary file click opens/activates it, sets singular focus, clears stale operation selection, and does not select the row | Playwright |
| F02 | Primary folder click opens a real folder tab and focuses it; chevron expand/collapse remains separate | Playwright |
| F03 | Primary Coin click focuses the immutable node and opens/activates its trace tab | Playwright + unit |
| F04 | Gold directory-left bar and gold tab-top border always identify the same focus; duplicate tabs show one gold tab | Playwright |
| F05 | Cmd/Ctrl-click toggles neutral operation selection without focus, player, or author-target movement | Playwright |
| F06 | Shift-click extends from the selection anchor without moving focus | Playwright + unit |
| F07 | Escape clears directory selection without moving focus | Playwright |
| F08 | Right-click selected item preserves the set; right-click unselected item creates a one-item operation set; neither focuses | Playwright |
| F09 | Dragging a selected item drags the set; dragging an unselected item drags only it; drag start does not focus | Playwright |
| F10 | Clicking live tabs, including duplicate trace tabs, updates exact tab locus and active panel deterministically | Playwright + unit |
| F11 | Close focused tab chooses same-panel fallback, then nearest live panel, then null focus | Unit + Playwright |
| F12 | Rename, move, and delete rebase or fall back focus without stale paths | Unit + integration |
| F13 | Workspace restore validates active panel/tab, restores focus, and starts with empty directory selection | Unit + browser reload |
| F14 | Step button, Cmd/Ctrl+S, Send, Attest, MODEL target, and player resolve the same focused subject and never read directory selection | Unit + Playwright |
| F15 | UI focus changes and mount/shield clicks do not mint protocol Steps; mount/shield clicks do not focus/select rows | Integration |

## 2. Replay and exact historical actions — 8 groups

| ID | Behavior | Primary layer |
| --- | --- | --- |
| R01 | Focus change stops playback, aborts prior load, resets playhead, and loads the new file/folder/Coin shape while gold focus remains stable | Unit + Playwright |
| R02 | Folder replay keeps the folder focused while the existing engine renders virtual read-only historical child panels outside live tab state | Integration |
| R03 | Clicking a projected child explicitly exits folder replay and performs an ordinary live child focus transition | Playwright |
| R04 | Scrubbing changes displayedNodeId but not UiFocus; returning to head restores live authoring | Unit + Playwright |
| R05 | Global palette is disabled in historical projection and remains routed to live focus; replay-tab buttons target displayedNodeId directly | Playwright |
| R06 | Send this version republishes only an exactly recovered locally owned signed node, creates no Step, and does not move focus/selection | Relay integration |
| R07 | Attest this version endorses the exact reachable local or foreign node, creates no Step, and inherits existing anchor semantics without redefining NIP-03 | Relay integration |
| R08 | Send & Attest orders exact Send before Attest, reports partial failure honestly, and never claims success when Attest fails | Recording relay + Playwright |

## 3. Secure storage and migration — 10 groups

| ID | Behavior | Primary layer |
| --- | --- | --- |
| S01 | ProviderProfile and KeyProfile serialization contains references and public metadata only; no apiKey or secretHex | Unit |
| S02 | Desktop Stronghold adapter get/set/delete/list behavior matches memory test store and respects narrow Tauri capability | Tauri mock + Rust |
| S03 | Locked vault permits read-only/model-free startup but gates signing, key derivation, Send, Attest, and MODEL execution | Playwright + Tauri mock |
| S04 | Raw provider secret resolves only inside transport and is discarded after success, error, and abort | Recording integration |
| S05 | Raw signing secret resolves only around sign/derive and never enters React state, logs, profile JSON, or errors | Unit + integration |
| S06 | Migration discovers zine.keys, zine.voice.secretHex, and stored provider apiKey variants without deleting them | Unit fixture matrix |
| S07 | Successful migration writes, reads back, verifies public identity/digest, persists refs, verifies resolution, then removes plaintext | Tauri mock integration |
| S08 | Failure at every migration phase leaves all legacy plaintext intact, records only nonsecret retry state, and blocks secure authoring | Fault-injection integration |
| S09 | Existing Keys, identity, MODEL voice, Doors, Networking/onion, provenance, workspace, Send, and Attest flows resolve the correct profile | Regression integration |
| S10 | Browser runtime uses session memory only and cannot enable persistent signing or MODEL authoring; capability copy is explicit | Browser E2E |

## 4. MODEL trust, preparation, and execution — 12 groups

| ID | Behavior | Primary layer |
| --- | --- | --- |
| M01 | ContextSnapshot canonicalizes target, ordered mounted inputs, heads, bodies, citations, delta logs, shields, completeness, contributions, and fingerprint | Unit golden vectors |
| M02 | Bounded parallel gather preserves deterministic ordering, memoizes per chain, coalesces identical work, and aborts superseded work | Unit with fake clock/relay |
| M03 | Any missing/invalid/aborted source produces incomplete state that is neither cached, approved, nor sent | Unit + recording transport |
| M04 | Dirty target or any deliberately unstepped mounted input blocks preparation and lists exact paths | Unit + Playwright |
| M05 | Hard total request budget includes all prompt layers and context categories; rejection shows an accurate per-source breakdown | Unit + Playwright |
| M06 | Neutral provider probe sends only fixed synthetic content and no workspace, provider-card, voice, lens, or context layer | Recording transport |
| M07 | PreparedOperation is deeply immutable, secret-free, and contains complete provenance/fingerprint data before network start | Unit |
| M08 | Estimate, Inspector, approval, and execution share one snapshot; approved canonical messages and transmitted canonical messages are byte-identical | Recording integration |
| M09 | Every request-affecting change invalidates approval: focus, head/content, mount, shield, provider/model/config, voice, lens, op, inputs, prompt version, or completeness | Parameterized unit + Playwright |
| M10 | Extend, Settle, Stir, Reply, and Receive all use the same prepare/execute boundary, including explicit immutable batches for multi-call work | Parameterized integration |
| M11 | Complete response on matching revision applies atomically with MODEL attribution, remains unstepped, and survives crash/reopen as dirty work | Integration + browser reload |
| M12 | Failure, empty response, cancellation, late response, or stale target produces no mutation; stale complete output is recoverable for inspect/copy/retry | Integration |

## 5. Optional MODEL onboarding — 6 groups

| ID | Behavior | Primary layer |
| --- | --- | --- |
| O01 | Model-free onboarding remains complete; optional MODEL chapter can be declined, dismissed at every stage, or started from Help | Playwright |
| O02 | Provider setup advances only after a current-session neutral probe bound to provider/model/config fingerprint; removal or mutation moves backward | Reducer + Playwright |
| O03 | Deterministic lesson creation is collision-safe, never overwrites user work, and validates exact manifests/hashes on reuse | Unit + integration |
| O04 | Real focus, mount, and shield controls guard progression independently; exact before/after allowlists exclude the private note and unrelated Root sentinels from bodies, metadata, and delta logs | Playwright + recording transport |
| O05 | Inspector approval, explicit invocation, matching atomic result, and later deliberate Step are distinct; completion waits for exact result node/span replay | Playwright + relay integration |
| O06 | Resume persists safe identifiers only, reconciles against artifacts, never autoopens sensitive setup, can move backward, and deletes lesson material through ordinary deletion semantics | Reducer + reload E2E |

## Live prompt evaluation

Lock a small deterministic corpus before implementation:

- one Extend continuation,
- one Settle cleanup with multiple loose segments,
- one Stir transformation,
- one Reply using cited context,
- one Receive synthesis.

For each case, capture the current baseline, then run the reviewed path against:

- one OpenAI-compatible provider/model;
- one Anthropic provider/model.

Score:

1. instruction adherence;
2. preservation of source facts and citations;
3. absence of shielded sentinel content;
4. operation-specific quality;
5. stable provenance/request metadata;
6. no unexpected prompt-layer duplication;
7. prepared/transmitted message equality.

Release requires both transports to complete all five cases, no shield leak, no
metadata mismatch, and no material rubric regression from baseline. Provider
credentials must never be written to fixtures or logs.

## Performance assertions

- Cold gather with N independent sources demonstrates bounded concurrency rather
  than N serial round trips.
- Warm identical estimate/Inspector/execution requests perform one gather.
- Result ordering and request hash are identical across randomized completion
  order.
- Focus or dependency change aborts superseded work and ignores late results.
- Incomplete work is absent from cache.
- Oversize snapshots fail before provider resolution.

## Failure-injection matrix

Inject failure at relay fetch, context parse, budget calculation, secure-store
write/read/delete, profile persistence, provider DNS/connect/stream/end,
cancellation, local head mutation, relay head mutation, replay fetch, Send, and
Attest. Each failure must assert:

- whether any durable state changed;
- whether any secret or content was logged;
- whether approval remains valid;
- whether a Step was created;
- whether focus or selection moved;
- what recovery action the UI offers.

## Command order

    cd apps/client && npm test
    cd apps/client && npm run build
    cd apps/client/src-tauri && cargo test
    cd apps/client && npx playwright test
    cd apps/client && npm run eval:prompts
    npm run verify:relay
    npm run check
    npm run verify

Run focused test files during implementation before the broad sequence. A
failure in secret migration, context completeness, request equality, stale
response rejection, or historical exact-node targeting is release-blocking.
