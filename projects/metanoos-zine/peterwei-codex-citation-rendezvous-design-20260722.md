# Design: Citation Rendezvous Alongside Co-Mint

Generated on 2026-07-22

Branch: `codex/citation-rendezvous-design`

Repository: `metanoos/zine`

Status: DRAFT

## Problem

The merged system treats co-mint as the primary rendezvous signal: two writers
independently mint canonically equivalent content. An older, uncommitted design
instead replaced co-mint with co-citation restricted to valid Coins. That
replacement is wrong in two ways:

1. co-mint and co-citation express different facts and should coexist;
2. co-citation should work for any published zine, not only Coins.

The desired product progression is:

1. small, immutable Coins generate the earliest and most frequent matches;
2. writers meet through those matches and begin citing larger zines;
3. shared citations gather writers around ongoing posts and conversations;
4. authors, downstream citers, and co-authors can discover one another through
   the signed citation graph.

## Vocabulary

Use lowercase `mint`, `coin`, `citation`, `co-mint`, and `co-citation` in prose.
Capitalize only exact UI labels or formally named commands.

### Co-mint

Two writers independently complete mints whose verified Coin bodies share the
same canonical coordinate `H`. Their Coin node ids may differ.

Signal: independently shared salience.

### Co-citation

Two published zines carry active social `q` citations to the same published
target.

Signal: shared reference or shared context. It does not imply agreement,
endorsement, trust, or common sentiment.

### Cited-author rendezvous

A published zine cites another writer's published target. The target's author
and the citing writer become a candidate introduction pair.

Signal: a writer has carried another writer's work into a new trace.

### Conversation

A client-derived projection over published zines connected by verified
citation, reply, and fork relationships. Conversation is not a new mutable
server-owned protocol object.

## Core Model

```text
Rendezvous evidence
├── co-mint
│   └── independent Coins share canonical H
└── citation rendezvous
    ├── cited-author
    │   └── a writer cites another writer's published zine
    └── co-citation
        └── two writers cite the same published target
```

All three relationships produce candidates, not admissions. Existing process
vetting and explicit human approval remain downstream.

## Citation Identity

The signed citation continues to pin one exact target Step. This is the
portable evidence of what the citing writer actually saw or used.

Discovery may additionally resolve the target through its verified trace chain
and group citations by trace identity. Therefore:

- two citations to the same exact Step are exact co-citations;
- citations to different Steps of the same verified trace may enter the same
  conversation projection;
- the interface must retain and show each exact cited Step;
- trace-family grouping never rewrites or weakens the signed citation edge.

For Coins, co-mint continues to match canonical `H`, not exact Coin id. For
ordinary zines, citation rendezvous begins with exact target or verified trace
identity rather than whole-document content hashing.

## Product Behavior

Every surfaced candidate must say why it exists:

- "You both minted equivalent words."
- "You both cited this exact Step."
- "You cited different revisions of the same zine."
- "They cited your zine."
- "You share both a mint and a citation."

Citation language must remain neutral. Never render a citation match as
agreement, endorsement, friendship, or trust.

Popular targets will create noisy candidate sets. Ranking may use rarity,
recency, repeated independent matches, and the existing vet, but those are
local policy inputs rather than protocol truth.

## Phase 1: Local Mutual-Peer Discovery

Ship additive discovery without changing wire formats or DHT records.

1. Preserve the merged `co-mint.ts` implementation and all PR #18 hardening.
2. Port the old co-citation algorithm onto current provenance utilities rather
   than restoring stale files.
3. Generalize the admission boundary from "valid Coin targets only" to any
   cryptographically valid, published zine target.
4. Keep structural folder membership and LLM-scope `q` edges excluded.
5. Read active citations from current heads so removed citations do not remain
   socially active.
6. Derive cited-author candidates from verified target ownership.
7. Share bounded peer reads, cancellation, concurrency limits, and sweep-wide
   byte/time budgets across detectors.
8. Return typed evidence so the UI and vet never conflate the relationships.

Illustrative local type, not yet a wire commitment:

```ts
type RendezvousEvidence =
  | {
      kind: "co-mint";
      coordinate: string;
      peerA: string;
      peerB: string;
      coinNodeIds: string[];
    }
  | {
      kind: "co-citation";
      peerA: string;
      peerB: string;
      targetNodeId: string;
      targetTraceId: string;
      exact: boolean;
    }
  | {
      kind: "cited-author";
      citingPeer: string;
      targetAuthor: string;
      targetNodeId: string;
      targetTraceId: string;
    };
```

## Phase 2: Conversation Projection

Build a bounded local graph from verified published edges:

- exact citation;
- verified trace-family citation;
- reply;
- fork;
- verified authorship and explicitly labeled asserted attribution.

The client may show a conversation and its participants without publishing a
membership list or granting network access. A candidate still passes through
vetting and explicit peer admission.

## Phase 3: Global Rendezvous

Global citation rendezvous requires a separate protocol decision.

The existing co-mint DHT record means "completed mint available under `H`."
Do not reinterpret that record as a citation pointer. Either:

1. create separate mint and citation namespaces; or
2. introduce a versioned typed record whose value states which proof path a
   verifier must execute.

Publishing a citation record must remain gated by the carrying zine's Send
boundary and the Coins/rendezvous opt-in. Private draft citations never enter
global discovery.

## Security and Privacy Constraints

- A citation is not an endorsement.
- A candidate is not an admitted peer.
- Never index private or unsent citation edges.
- Verify ids, signatures, target chain, current-head status, deletion status,
  and ownership before surfacing a relationship.
- Bound total peers, events, targets, bytes, time, concurrency, and relay work.
- Preserve native cancellation across Tauri network calls.
- Do not infer verified co-authorship from asserted run attribution; label the
  epistemic status.
- Keep the rendezvous DHT discovery-only. It never grants read access.

## Regression Coverage

Phase 1 must cover:

- two writers citing the same ordinary published zine;
- a writer citing another writer's zine;
- two citations to different Steps in the same verified trace;
- exact Step evidence retained after trace-family grouping;
- citation removal and deleted heads producing no active signal;
- structural and LLM-scope `q` edges excluded;
- disagreement remaining valid citation evidence;
- forged, malformed, foreign-owner, or incomplete targets rejected;
- hostile fan-out stopped by shared byte/time budgets;
- caller and native cancellation closing in-flight work;
- co-mint behavior and tests remaining unchanged;
- combined co-mint and co-citation evidence deduplicating one peer pair without
  erasing either reason.

## Alternatives

### A. Exact-target local co-citation only

Smallest implementation. Match mutual peers only when active citations point
to the same exact Step. Low wire risk, but different revisions of one evolving
post fragment the conversation.

### B. Layered local citation rendezvous

Recommended. Preserve exact Step evidence, add verified trace-family grouping,
and surface co-mint, co-citation, and cited-author as typed reasons. No wire
change in the first release.

### C. Global conversation index immediately

Index all Sent citations and derive public conversation groups through the DHT.
This reaches strangers sooner, but forces wire, privacy, spam, and multi-writer
record decisions before local behavior has been observed.

## Recommendation

Choose B. It captures the intended social progression without replacing
co-mint or changing the current wire protocol. It also creates observable local
behavior that can inform the later global design.

## Open Questions

1. Should trace-family grouping require the same trace owner, or may a verified
   fork participate in the same conversation family?
2. Should cited-author rendezvous surface only the target signer, or also
   verified seam contributors and clearly labeled asserted contributors?
3. What local threshold prevents one popular post from producing an unusable
   candidate flood?
4. Does global citation indexing happen for every Sent citation when Coins are
   enabled, or require a separate per-Send rendezvous choice?
5. Should the first UI be an introduction inbox, a conversation view, or both?

## Next Implementation Boundary

After this design is approved, create one implementation branch from clean
`main` for Phase 1 only. Do not copy the stale provenance tree. Port individual
ideas and tests onto the current Co-Mint and recovery foundations, then run the
full client, Rust, and real-relay verification required for provenance and
networking changes.
