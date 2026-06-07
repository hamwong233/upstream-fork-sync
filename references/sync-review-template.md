# Upstream Fork Sync Review

- Generated:
- Inventory:
- Frozen baseline in inventory:
- Review base:
- Review target:
- Target selection reason:
- Upstream changed files:
- Matched feature groups:
- Unmatched upstream files:

## Operating Rules

- Complete the decision block for every matched feature before merge-back.
- Classify every unmatched upstream file before treating the sync as finished.
- Keep the final code shape as `upstream base + explicit fork deltas`.

## Global Checklist

- [ ] Recreate any derived frontend/app variant from the target upstream base before replaying fork-specific frontend behavior.
- [ ] Keep upstream-owned paths identical to the target upstream after replay, except for explicitly approved minimal upstream patches.
- [ ] Run overlay/path verification during replay and again before merge-back.
- [ ] Run focused verification for each feature group that was replayed.
- [ ] Record keep/adapt/drop/split decisions in this worksheet before merge-back.

## Feature Review Queue

### Example Feature Title

- Feature ID:
- Layer:
- Keep strategy:
- Review trigger:
- Must keep:
- Branch contract:

Changed upstream touchpoints:
- `path/to/upstream/file`

Public APIs / contracts to preserve:
- Describe fork-visible API/DTO/setting behavior here.

Settings / DTO keys to re-check:
- `OptionalKey`

Data / migration contracts to re-check:
- Describe persisted data, migration, or compatibility requirements here.

Human review checklist:
- [ ] Re-check the fork-only contract against the new upstream implementation.
- [ ] Keep upstream as the base and re-apply only the minimum fork delta.

Review questions:
- [ ] Did upstream cover part of this behavior already?
- [ ] Should the fork keep, adapt, drop, or split this behavior?

Verification hooks:
- `your-test-or-smoke-check`

Decision:
- Status: `TODO keep | adapt | drop | split`
- Replay plan: `TODO describe the minimum fork delta to keep`
- Notes: `TODO record the human decision and tradeoffs`
- Verification result: `TODO`

## Unmatched Upstream Files

- `path/to/unmatched/file`

Unmatched-file resolution:
- [ ] Confirm each file is irrelevant to the fork, or
- [ ] add the file to an existing feature's upstream touchpoints, or
- [ ] create a new feature entry before merge-back.

## Final Sign-Off

- [ ] Upstream-owned path diff to target upstream is empty except for explicitly approved minimal upstream patches.
- [ ] Remaining fork diff is limited to audited fork-owned files and explicitly approved minimal upstream patch files.
- [ ] Inventory still reflects the real fork contract after replay.
- [ ] This worksheet records what was kept, adapted, dropped, or split.
- [ ] Frozen baseline metadata was updated only after verification passed.
