# PRST Series — PRST-001

**Status:** Draft research review; not approved for production, deployment, or merge.

## Purpose
Separate **PRST Series (Pull Request Safety & Testing)** from the existing **Doraemon ST Series**. PRST-001 reviews the offline ST15 Clockwork City durable-quorum educational prototype without replacing its baseline.

## Local source audit (2026-11-20)
- Source artifact: `Doraemon_ST15.zip` from the conversation workspace.
- Files inspected: `st15.py`, `test_st15.py`, `README.md`, `example_report.json`.
- Command: `python3 -m unittest -v test_st15.py`.
- Observed outcome: **12 tests passed** locally. GitHub CI has not been independently verified.

## Boundaries
- Three simulated replicas with distinct local state files on one host, not separate TCP processes or failure domains.
- Fixed coordinator; recorded term but no leader election.
- Majority prepared log entries, fsync and hash-chain consistency checks.
- No general distributed consensus safety proof: concurrent coordinators, leader crash during commit, network partition and malicious replicas remain out of scope.
- No internet scanning, remote execution, live networking, credential access, external deployment, or autonomous merge.
- Morphogenetic field is an information-network analogy, not an established physical field.

## Review gates for the next PRST stages
1. Reproduce ST15's tests and document environment/hash of the reviewed source.
2. Add adversarial crash-point and split-brain safety tests (expected gaps must be recorded, not concealed).
3. Assess term changes, durable commit certificates and recovery semantics.
4. Obtain independent review before approving any merge or staging deployment.

This PR is intentionally documentation-only until the independently reviewed ST15 source can be brought in with provenance and checksums.
