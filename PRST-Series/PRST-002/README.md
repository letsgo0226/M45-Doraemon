# PRST-002 — ST15 Fault-Injection Review

**Status: DRAFT / NOT APPROVED / NOT DEPLOYED**

This series is independent of Doraemon ST10–ST15 and PRST-001. It reviews the *offline* ST15 fixed-leader durable-quorum educational model. It does not modify the ST15 baseline or assert full Raft/Paxos consensus.

## Reproduction procedure

1. Obtain the independently reviewed `Doraemon_ST15.zip` and record its SHA-256 and origin; do not trust filename alone.
2. Run `python3 -m unittest -v test_st15.py` in an isolated Python 3 environment.
3. Run crash-point tests at: before prepare, after one durable prepare, after quorum prepare but before commit, during commit fan-out, and after leader restart.
4. For every test, record replica logs, commit indices, term, acknowledged client result and recovery outcome.
5. Explicitly mark unimplemented injection points as **NOT TESTED**; never report them as passing.

## Initial source review findings

- `City.submit(value, crash_after_prepare=True)` provides one *coarse* pre-commit interruption, but does not inject crashes at arbitrary prepare/commit steps.
- `Node.prepare()` persists term and entry; `Node.commit()` persists a commit index. Both use atomic file replacement and fsync in this local model.
- The coordinator invokes `commit()` for every acknowledged node and then returns `True` without independently confirming each commit returned success. A real process crash mid-fan-out is not simulated.
- `City.recover()` copies from a fixed leader and rejects conflicting committed prefixes; this is not a leader-election or partition-tolerant recovery protocol.
- SHA-256 detects accidental changes to entries but is not an authentication mechanism against an adversary who can rewrite both content and hashes.

## Safety properties under review

- **Committed-prefix agreement**: two replicas must not expose incompatible committed entries at the same index.
- **Durable acknowledgment**: an acknowledged operation must survive crashes under the specified failure model.
- **Quorum gate**: a single surviving replica must not acknowledge a new commit in a 3-replica configuration.
- **No unsafe repair**: recovery must not overwrite a conflicting committed prefix.

## Review gates

- Import the ST15 source only after provenance and checksums are verified.
- Add deterministic fault-injection hooks in a separate experimental adapter, without silently changing the ST15 baseline.
- Run adversarial tests and report failures as findings.
- Obtain independent review before merge or staging.

No external network connections, secret access, production deployment, automated merge, or CI approval are authorized by this draft.
