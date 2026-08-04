# QA plan: durable encrypted authored vaults

Generated on 2026-08-03
Branch: main
Parent plan: peterwei-main-vault-persistence-eng-reviewed-plan-20260803-191153.md
Status: APPROVED

## Test layers

- Rust core unit tests: deterministic event reduction and exhaustive snapshot
  invariants.
- Rust native integration tests: password/key lifecycle, catalog reconstruction,
  encrypted journal envelopes, idempotent transactions, tamper/truncation,
  recovery, permissions, lock, reopen, and deletion.
- TypeScript workspace tests: queue order, exact-head progression, save-state
  publication, duplicate acknowledgement, retry/readback, recovery freeze, and
  structural versus optimistic editing behavior.
- UI integration tests: create/open/lock/delete forms, no-recovery copy,
  password-confirmed deletion, durable resource mutations, save/error display,
  and non-desktop session fallback.
- Boundary/build tests: Tauri imports remain desktop-only; all authoring surfaces
  compile; Rust format, Clippy, and workspace tests pass.
- Real desktop smoke: create, edit, apparatus, renderer reload, full restart,
  wrong-password rejection, correct-password replay, and irreversible deletion.

## Critical paths

| ID | Behavior | Primary layer |
| --- | --- | --- |
| V01 | First launch lists an empty valid catalog without fabricating a vault | Rust native + UI |
| V02 | Create derives independent verifier/wrapping material, wraps a random key, initializes the journal, and returns a verified empty snapshot | Rust native |
| V03 | Renderer reload restores the process-unlocked active vault and its exact snapshot without another password | Desktop smoke |
| V04 | Full process restart lists the vault locked; correct password replays it; wrong password changes nothing | Rust native + desktop smoke |
| V05 | Create/rename/move/trash/restore resource transactions replay identically | Core + TypeScript + UI |
| V06 | Every CodeMirror transaction updates optimistically, reports saving, and reports saved only after its durable receipt | Workspace + UI |
| V07 | Material identity and apparatus annotations remain anchored after edit, reload, and reopen | Core + UI + desktop smoke |
| V08 | Lock drains writes, clears the native key, and rejects later snapshot/commit commands | Rust native + workspace |
| V09 | Delete requires correct password and exact name, destroys the access record, reconciles the catalog, and cannot reopen leftover ciphertext | Rust native + UI |

## Crypto and storage matrix

- Correct password, wrong password, empty password, maximum accepted password,
  and oversized password.
- Independent vaults created with the same password have distinct salts, nonces,
  wrapped keys, and journal ciphertext.
- Access verifier tamper, wrap salt tamper, wrap nonce tamper, wrapped-key tamper,
  and vault-id/AAD substitution all fail closed.
- Event nonce/ciphertext/header/prior-digest tamper, row deletion, row reorder,
  duplicate id, and truncated final transaction all fail verified replay.
- Catalog missing, malformed, wrong version, duplicate entry, stale entry, and
  valid access-record scan reconstruction.
- Explicit Unix assertions for 0700 directories and 0600 catalog/access/database
  files where supported.
- WAL mode and synchronous=FULL are queried back after open rather than assumed.

## Retry and fallback behavior

Retry and fallback paths are release-blocking because a fallback to React state
would recreate the original data-loss bug.

- Commit succeeds but transport reports error: read-ack finds the transaction,
  reload returns its committed snapshot, and the event is not duplicated.
- Commit fails before append: read-ack is absent, reload returns the prior head,
  and an explicit retry uses a new transaction built against that head.
- Read-ack fails but reload succeeds: local state is replaced by replayed
  authority and mutation remains blocked until the caller chooses a new action.
- Read-ack and reload both fail: workspace enters blocked error state and refuses
  every mutation; it never labels content saved or continues in memory-only mode.
- Duplicate transaction id with identical content returns the original receipt;
  reuse with different content is rejected.
- Expected-head conflict reloads authority and rejects the original transaction;
  there is no automatic retarget.
- Catalog recovery never falls back to an empty catalog on parse error; it scans
  valid access records or reports a blocking error.
- Native unavailability on web/mobile uses the explicit session host selected at
  bootstrap, not a silent runtime fallback after native failure.

## Performance assertions

- A sustained sequence of at least 500 small document edits preserves order,
  produces 500 distinct acknowledgements, and ends at the expected head.
- Queue depth drains to zero and `saved` is not published between an edit and
  its acknowledgement.
- Concurrent structural and text submissions serialize without busy-looping or
  head conflicts.
- Checkpoint work occurs after commit acknowledgement and cannot change receipt
  correctness.
- Replay rejects configured size limits before excessive allocation.

## Real desktop smoke procedure

1. Start the real Tauri desktop target and create a named vault with a test-only
   password; confirm the no-recovery warning.
2. Create a folder and document, rename and move them, enter distinctive text,
   add one apparatus note, and wait for `saved`.
3. Reload only the renderer. Confirm the vault remains open and all authored
   state is identical.
4. Quit the full process and restart. Confirm the catalog entry remains but the
   vault is locked.
5. Attempt a wrong password, then open with the correct password and verify the
   complete replay.
6. Lock and prove further native reads fail until reopen.
7. Attempt deletion with wrong name/password, then delete with both correct;
   restart and confirm the vault does not return.

## Command order

    pnpm --filter @zine/host-capabilities test
    pnpm --filter @zine/workspace test
    pnpm --filter @zine/ui test
    cargo test -p zine-core
    cargo test -p zine-desktop
    pnpm typecheck
    pnpm test
    pnpm build
    cargo fmt --all -- --check
    cargo clippy --workspace --all-targets -- -D warnings
    cargo test --workspace

The reported create/edit/reload/restart path, wrong-password handling, tamper
rejection, ambiguous-write recovery, and deletion-key destruction are all
release-blocking.
