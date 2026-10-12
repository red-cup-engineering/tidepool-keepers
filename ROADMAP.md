# tidepool-keepers roadmap (revision 3)

Spec: master specification revision 3, §2.7–2.9, §3 A2, A3, A4, A7, B4. Conventions: ESM, `node --test` with RPC fakes, one keeper = one script with `--dry-run` default, journaling each attempt to `data/journal/<keeper>/<date>.jsonl`, deduplicated across runs. Every keeper call is one any member could make; keepers hold no privileged role except the v0.1 relayer grant (`tellHead`, `witnessForeignRelease`, `witnessForeignSettle`), whose blast radius is bounded by §2.6 (provisional tellings never move money). Signing through `key-management-services-section`'s `sign-with-wallet` under a bounded mandate (allowed contracts and selectors, zero native value); paper intents until armed. **Note rule:** every task's acceptance includes a note at `tidepool-engineering-services-section/content/text/documents/articles/notes/YYYY/MM/DD/YYYY-MM-DD-note-on-<task-id>-<slug>.md`; a task without its note is unfinished.

## Phase 1 (v0.1)

### TP-KEEP-001 `v0.1` Cycle keeper: close and commit
- Goal: `scripts/close-cycle.mjs --site <slug>`: when `now ≥ openedAt + cycleLength` and the uptime feed is up, submit `closeCycle()`; then, with TP-REC-005's sorted list, submit `commitSection(cycle, sortedCells)`; `src/mandate.mjs` shared (selector allowlist exactly `closeCycle()`, `commitSection(uint64,bytes32[])`).
- Files: `scripts/close-cycle.mjs`, `src/mandate.mjs`, `test/close-cycle.test.mjs`, `deploy/tidepool-close-cycle@.{service,timer}`.
- Acceptance: no intent before the window; the two calls after it, in order; a wrong sorted list is caught locally before submission; the mandate refuses any other selector.
- Depends: TP-EVM-014, TP-REC-005, TP-ENG-001.

### TP-KEEP-002 `v0.1` Lifecycle keeper: overdue, void, quiesce, execute, grant, claim
- Goal: `scripts/lifecycle.mjs`: every `Open` draw with `now > dueDate + relayGraceSeconds` and the uptime check satisfied → `markOverdue`; every foreign release past `releaseWindowSeconds` without a witness → `voidUnreleased`; every question past `closesAt` → `quiesce` then, for `Kind.Cycle`, `execute`; every reseed question quiesced → `grant`; every `claimable > 0` recipient that opted in (a `claimFor` list in the keeper config) → `claim(to)`.
- Files: `scripts/lifecycle.mjs`, `test/lifecycle.test.mjs`.
- Acceptance: fixture events produce exactly the due calls, none early (relay grace respected, uptime respected), none twice.
- Depends: TP-EVM-014, TP-REC-001.

### TP-KEEP-003 `v0.1` Relay keeper: heads and Hive witnesses
- Goal: `scripts/relay.mjs`: submit `tellHead` for each (target, source) pair along registered edges only (spokes to the hub, hub to spokes; never spoke to spoke), with the §2.9 envelope signed by the relayer key; submit `witnessForeignRelease`/`witnessForeignSettle` from `tidepool-hive`'s `witnessTransfer` outputs; monotone `(cycle, height)` enforced locally first.
- Files: `scripts/relay.mjs`, `test/relay.test.mjs`.
- Acceptance: a two-site fixture emits one `tellHead` per registered edge direction; a replayed envelope is dropped locally; the mandate refuses anything but `tellHead`, `witnessForeignRelease`, `witnessForeignSettle`.
- Depends: TP-REC-005, TP-EVM-014, TP-HIVE-002.

### TP-KEEP-004 `v0.1` Timers and receipts
- Goal: systemd user units per keeper per site (10-minute cadence), receipts through `semiotic-eavt-services-section`.
- Acceptance: `systemd-analyze verify` passes; one receipt per attempt.
- Depends: TP-KEEP-001, TP-KEEP-002, TP-KEEP-003.

## Phase 2 (v0.2)

- TP-KEEP-005 worklist and H¹ submitters; TP-KEEP-006 attestation submitter; TP-KEEP-007 the keeper as one of n relayers (k-of-n).

## Capability-ledger citations

`tp-mandate-execution` (the signing path, zero-value selectors only); `tp-protocol-clerk-agency` (dormant → the keepers are the Clerk's duties; the office record is updated by TP-OPS-006).
