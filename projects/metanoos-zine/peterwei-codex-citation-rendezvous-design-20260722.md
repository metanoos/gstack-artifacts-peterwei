# Design: Citation rendezvous alongside co-mint

Generated on 2026-07-22

Branch: `codex/citation-rendezvous-design`

Repository: `metanoos/zine`

Status: DRAFT — independent review in progress

Review: revised after two independent review rounds

## Problem

The merged system treats co-mint as one rendezvous signal: two writers
independently mint canonically equivalent content. An older, uncommitted design
instead replaced co-mint with co-citation restricted to valid coins. That
replacement is wrong in two ways:

1. co-mint and co-citation express different facts and should coexist;
2. co-citation should work for any published zine, not only coins.

The desired product progression is:

1. small, immutable coins generate the earliest and most frequent matches;
2. writers meet through those matches and begin citing larger zines;
3. shared citations gather writers around ongoing posts and conversations;
4. authors and downstream citers discover one another through the signed
   citation graph;
5. later conversation views may include verified and clearly labeled asserted
   contributors without changing who owns a trace.

## Settled Premises

1. Preserve the merged co-mint implementation, canonical `H` matching, DHT
   meaning, recovery behavior, cancellation, resource bounds, and tests.
2. Add citation rendezvous; never restore the stale provenance tree wholesale.
3. A citation remains one signed lowercase `q` edge to an exact target Step.
4. Co-citation applies to any eligible published zine target.
5. Citation is neutral composition, not endorsement, agreement, friendship, or
   trust.
6. Exact Step evidence remains immutable. Verified trace identity is a derived
   grouping key for discovery.
7. Phase 1 is local and additive. It changes no wire format or DHT record.
8. Conversations are client-derived projections, not server-owned protocol
   objects.

## Vocabulary

Use lowercase `mint`, `coin`, `citation`, `co-mint`, and
`co-citation` in prose. Capitalize only exact UI labels, source identifiers,
or formally named commands.

### Co-mint

Two writers independently complete mints whose verified coin bodies share the
same canonical coordinate `H`. Their coin node ids may differ.

Signal: independently shared salience.

### Exact-step co-citation

Two active published citation edges target the same exact Step.

Signal: both writers referred to the same signed version.

### Same-trace co-citation

Two active published citation edges target different Steps that resolve to the
same verified trace genesis and fixed trace owner.

Signal: both writers referred to the same evolving zine, but not necessarily
the same text.

A fork has a distinct trace genesis. It does not qualify as same-trace
co-citation in Phase 1. Fork relationships may connect conversations in
Phase 2.

### Cited-author rendezvous

An active published citation edge targets a zine owned by another writer. The
verified fixed trace owner and the citing owner become a candidate pair.

Phase 1 uses the trace owner proven by the target chain back to genesis. It does
not treat textual contributors, asserted run attribution, `q` owner hints, or
folder membership as target ownership. Self-citations are suppressed.

Phase 1 emits this candidate only when the verified target owner is the local
owner or an already admitted readable peer. A target owned by an unknown writer
may still support co-citation between admitted citing writers, but it does not
create a reachable cited-author candidate yet. Discovering and admitting a
previously unknown target owner requires the Phase 3 global design; a relay
locator for public evidence is not a peer-admission endpoint.

Signal: a writer carried another writer's work into a new trace.

### Conversation

A bounded client-derived projection over published zines connected by verified
citation, reply, and fork relationships. Conversation is not a new mutable
server-owned protocol object.

## Core Model

```text
Rendezvous evidence
├── co-mint
│   └── independent coins share canonical H
└── citation rendezvous
    ├── cited-author
    │   └── a writer cites another writer's published zine
    └── co-citation
        ├── exact-step: both cite the same Step
        └── same-trace: both cite Steps in the same verified trace
```

All relationships produce candidates, not admissions. Existing process vetting
and explicit human approval remain downstream.

## Participant Scope

The local press acts as introducer `C` over the local owner and the bounded set
of peers whose published traces it can read.

Phase 1 emits both:

1. **local-to-peer candidates**, where the local owner is one participant;
2. **peer-to-peer candidates**, where the local owner may broker an
   introduction between two admitted peers.

The evidence object always names the actual two participants. UI copy changes
with viewpoint; it never says "you" when the local owner is only the
introducer.

Every Phase 1 candidate participant must therefore be either the local owner or
one of the bounded admitted readable peers. Publicly fetchable evidence may
refer to other owners, but does not silently expand the participant set.

## Publication Evidence

`Send` changes reachability but is not itself a signed publication marker.
Phase 1 therefore distinguishes cryptographic validity from relay-observed
publication.

An eligible carrying head and exact target must:

1. pass normal id, signature, kind, chain, and fixed-owner verification;
2. be fetchable from at least one configured stranger-readable, non-loopback
   publication relay;
3. be observed through a bounded, cancellable read;
4. retain the relay locator used for later re-verification.

Relay fetchability is an observation, not timeless protocol truth. A later
unavailable target does not make its historical citation false, but it is
suppressed from active rendezvous and may appear as withdrawn or unavailable
only in later conversation history.

Private, local-only, ACL-only, and unsent citation edges never enter citation
rendezvous.

## Eligible Carriers and Targets

### Carrier

An active citation may originate only from a current, non-deleted, published
file-zine head carrying an ordinary social `q` edge.

The following never become social citation evidence:

- folder membership edges;
- LLM-scope or prompt-dependency `q` edges;
- historical superseded heads;
- deleted carrying heads;
- malformed or unknown scope encodings that cannot safely separate structural
  and social targets.

### Target

The exact target may be any valid published file, folder, or coin zine. It must
resolve through its verified chain to one fixed trace id and owner.

A target whose current trace head is deleted or whose publication proof is no
longer observable is suppressed from active rendezvous. The signed historical
citation remains inspectable in Replay and later conversation history.

A valid same-author NIP-09 kind-5 removal request also withdraws a carrier or
target from active rendezvous, even if a relay still serves the referenced
event. The request must be signed by the event or replaceable-address owner and
must reference the relevant event id or trace-head address. NIP-09 changes the
active discovery state, not the cryptographic integrity of cached history, and
does not imply that every relay erased the bytes.

## Citation Identity

The signed citation pins one exact target Step. That is the portable evidence
of what the citing writer actually saw or used.

Discovery may resolve that target through its verified chain and group by trace
identity:

- same exact target node id: `exact-step`;
- different target node ids with the same verified genesis and owner:
  `same-trace`;
- different genesis, including a fork: not same-trace in Phase 1.

The interface retains and shows both exact cited Steps. Trace grouping never
rewrites or weakens either signed citation edge.

For coins, co-mint continues to match canonical `H`, not exact coin id. For
ordinary zines, citation rendezvous uses exact target and verified trace
identity rather than whole-document content hashing.

## Local Evidence Model

These types describe local derived evidence, not a wire commitment:

```ts
type CitationEvidence = {
  citingOwner: string;
  citingTraceId: string;
  carryingHeadNodeId: string;
  carryingRelayUrl: string;
  exactTargetNodeId: string;
  targetRelayUrl: string;
  targetTraceId: string;
  targetOwner: string;
  verifiedAt: number;
  publicationObservedAt: number;
};

type CoCitationEvidence = {
  kind: "co-citation";
  match: "exact-step" | "same-trace";
  left: CitationEvidence;
  right: CitationEvidence;
};

type CitedAuthorEvidence = {
  kind: "cited-author";
  citation: CitationEvidence;
};

type RendezvousEvidence =
  | ExistingCoMintEvidence
  | CoCitationEvidence
  | CitedAuthorEvidence;
```

This retains both carrying current heads and both exact targets. Every reason
can be independently re-verified after aggregation.

Co-citation also requires distinct writers:
`left.citingOwner !== right.citingOwner`. Multiple carrying heads owned by one
writer may contribute evidence to that writer's history, but never manufacture
a rendezvous pair with themself.

## Bounded Sweep and Aggregation

Preserve co-mint behavior, API compatibility, and hardening while factoring a
shared bounded sweep orchestrator for reusable peer reads.

Phase 1 limits:

- at most 64 peers per sweep;
- at most 100 current-head candidates per peer;
- at most 128 social targets per carrier;
- at most 512 active social targets per peer;
- at most 1,024 unique exact targets resolved per sweep;
- at most 32 retained reasons per peer pair;
- at most 128 surfaced candidate pairs per sweep;
- existing sweep-wide byte, deadline, cancellation, event-size, tag-count, and
  concurrency limits remain mandatory.

Within one sweep:

1. resolve each exact target once;
2. cache verified trace identity, fixed owner, current deletion state, verified
   same-owner NIP-09 removal state, and publication observation;
3. aggregate by canonical peer pair;
4. preserve separate co-mint, exact-step, same-trace, and cited-author reasons;
5. deduplicate identical reasons, then reserve one retained reason from every
   present kind/subtype before filling the remaining slots in deterministic
   order by reason kind, target trace id, exact target id, and carrying head id;
6. record the observed count and truncation flag for each kind/subtype so a
   dense bucket cannot erase a different evidence kind at the 32-reason cap.

Candidate ranking is local policy:

1. peer pairs with more than one independent reason kind;
2. co-mint;
3. exact-step co-citation;
4. same-trace co-citation;
5. cited-author alone;
6. number of distinct verified targets;
7. rarer local targets before ubiquitous targets;
8. canonical peer-pair bytes as the final deterministic tie-breaker.

The ranking communicates evidence density, not reputation or trust.

## Product Behavior

The first Phase 1 surface is an **Introduction Inbox**. Conversation views are
Phase 2.

Local-to-peer copy may say:

- "You both minted equivalent words."
- "You both cited this exact Step."
- "You cited different revisions of the same zine."
- "They cited your zine."
- "You share both a mint and a citation."

Peer-to-peer broker copy may say:

- "A and B both cited this exact Step."
- "A and B cited different revisions of the same zine."
- "B cited A's zine."
- "A and B share both a mint and a citation."

The interface shows exact targets, evidence status, and the local user's role:
participant or introducer. It never renders citation as agreement,
endorsement, friendship, or trust.

## Phase 1: Local Citation Rendezvous

Ship additive discovery without changing wire formats or DHT records.

1. Preserve merged `co-mint.ts` behavior and all PR #18 hardening.
2. Port ideas and tests from the old co-citation module onto current provenance
   utilities rather than restoring stale files.
3. Load bounded current heads for the local owner and readable peers.
4. Retain current, non-deleted, published file carriers.
5. Extract only active social `q` edges.
6. Resolve each unique target, trace identity, owner, deletion status, and
   publication observation once.
7. Emit exact-step and same-trace evidence only for distinct citing owners, and
   emit cited-author evidence only when its target owner is the local owner or
   an admitted readable peer.
8. Run existing co-mint detection without changing its result semantics.
9. Aggregate by peer pair while preserving every typed reason.
10. Render the Introduction Inbox and pass candidates through existing vetting
    and explicit admission.

Phase 1 emits same-trace rendezvous evidence. It does not yet construct a wider
conversation graph.

## Phase 2: Conversation Projection

Build a bounded local graph from Phase 1 evidence plus verified published
relationships:

- exact citation;
- same-trace citation;
- reply;
- fork;
- verified authorship;
- clearly labeled asserted attribution.

Forks connect conversations but do not collapse trace identity. Contributors
may appear in the conversation view, but they do not change fixed-owner
cited-author semantics.

The client may show a conversation and its participants without publishing a
membership list or granting network access. Every new peer relationship still
passes through vetting and explicit admission.

## Phase 3: Global Rendezvous

Global citation rendezvous requires a separate protocol decision.

The existing co-mint DHT record means "completed mint available under `H`."
Do not reinterpret that record as a citation pointer. Either:

1. create separate mint and citation namespaces; or
2. introduce a versioned typed record whose value states which proof path a
   verifier must execute.

Publishing a citation record must remain gated by the carrying zine's Send
boundary and the coins/rendezvous opt-in. Private draft citations never enter
global discovery.

## Security and Privacy Constraints

- A citation is not an endorsement.
- A candidate is not an admitted peer.
- Never index private, unsent, ACL-only, or loopback-only citation edges.
- Verify ids, signatures, target chain, current-head status, deletion status,
  same-owner NIP-09 removal state, fixed owner, and relay fetchability before
  surfacing a relationship.
- Suppress self-citation from cited-author candidates.
- Require distinct citing owners for every co-citation candidate.
- Do not turn an unknown target owner into a participant merely because their
  public evidence has a relay locator.
- Bound total peers, events, targets, reasons, candidate pairs, bytes, time,
  concurrency, and relay work.
- Preserve native cancellation across Tauri network calls.
- Do not infer verified co-authorship from asserted run attribution.
- Keep the rendezvous DHT discovery-only. It never grants read access.

## Regression Coverage

Phase 1 must cover:

- local-to-peer and peer-to-peer participant scopes;
- two writers citing the same ordinary published Step;
- two citations to different Steps in the same verified trace;
- exact target evidence retained for both sides after same-trace grouping;
- a writer citing another writer's zine;
- cited-author self-citation suppression;
- carrier deletion removing outgoing active evidence;
- target deletion or unavailable publication suppressing active rendezvous
  without erasing historical Replay evidence;
- a valid same-owner NIP-09 removal request suppressing an otherwise fetchable
  carrier or target, while a forged or foreign-owner request has no effect;
- two heads from one citing owner never producing a co-citation pair;
- an unknown target owner supporting admitted-peer co-citation without becoming
  a Phase 1 cited-author participant;
- file carriers and file, folder, and coin targets;
- structural and LLM-scope `q` edges excluded;
- malformed scope encodings failing closed;
- disagreement remaining valid citation evidence;
- forged, malformed, foreign-owner, or incomplete chains rejected;
- publication fetchability distinguished from cryptographic validity;
- unique targets resolved once per sweep;
- deterministic reason and candidate caps;
- every present reason kind/subtype retaining one representative at the
  32-reason cap, with observed counts and truncation disclosed;
- hostile fan-out stopped by shared byte/time budgets;
- caller and native cancellation closing in-flight work;
- co-mint behavior and tests remaining unchanged;
- combined co-mint and citation reasons deduplicating one peer pair without
  erasing either reason;
- viewpoint-correct participant versus introducer copy.

## Alternatives

### A. Exact-target local co-citation only

Smallest implementation. Match only when active citations point to the same
exact Step, and intentionally omit same-trace and cited-author rendezvous. Low
wire risk, but different revisions of one evolving post fragment the
conversation and direct writer-to-author introductions are absent.

### B. Layered local citation rendezvous

Recommended. Preserve exact Step evidence, add verified same-trace grouping,
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

## Deferred Global Questions

1. Should global citation rendezvous use separate namespaces or a versioned
   typed record?
2. Does global citation indexing happen for every Sent citation when coins are
   enabled, or require a separate per-Send rendezvous choice?
3. What public-network spam and density evidence is required before global
   citation rendezvous is enabled by default?

## Next Implementation Boundary

After this design is approved, create one implementation branch from clean
`main` for Phase 1 only. Do not copy the stale provenance tree. Port individual
ideas and tests onto the current co-mint and recovery foundations, then run the
full client, Rust, and real-relay verification required for provenance and
networking changes.
