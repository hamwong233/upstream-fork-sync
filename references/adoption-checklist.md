# Adoption Checklist

Use this checklist when introducing the upstream-fork-sync workflow into a repository for the first time.

## 1. Confirm the repo really has an upstream-fork problem

- Identify the authoritative upstream remote, branch, tag, or release stream.
- Confirm the fork is expected to keep syncing upstream more than once.
- Confirm the fork has intentional behavior worth preserving, not just a tiny one-off patch set.

## 2. Define the ownership boundary

- Decide which paths must always stay identical to upstream by default.
- Decide which paths may carry fork-only behavior.
- Decide whether any upstream-owned file is allowed to carry a minimal fork patch, and require those cases to be explicitly inventoried.
- Decide whether the fork has one derived frontend/app variant or several.
- Write down the direct-edit exception: when is in-place editing acceptable, and when must maintainers switch to an upstream-based rebuild workspace?

## 3. Create the machine-readable inventory

- Create one inventory file for fork feature groups.
- Start with a few stable feature groups, not dozens of micro-features.
- Record:
  - branch-owned paths
  - upstream touchpoints
  - API/settings/data contracts
  - tests or smoke checks
  - human review checklist/questions

## 4. Create the sync review record format

- Choose one directory for broad-sync review records.
- Use one review worksheet per broad upstream sync round.
- Make sure the worksheet records:
  - old frozen upstream baseline
  - new upstream target
  - why that sync target was chosen
  - touched feature groups
  - unmatched upstream files
  - keep/adapt/drop/split decisions
  - verification outcome

## 5. Add guardrails

- Add a path/overlay verification command.
- Add a record/inventory verification command.
- Add a closeout command or checklist for local worktree audits.
- If the repo uses local hooks or CI, wire the same checks into commit/push or PR validation.

## 6. Define the rebuild flow

- Require broad upstream syncs to start from a clean upstream worktree or branch.
- Require derived frontend/app variants to be recreated from the upstream base first.
- Require feature-oriented replay, not giant directory overlays.
- Require merge-back only after verification passes.

## 7. Test the workflow before trusting it

- Run one dry-run audit against the current upstream gap.
- Generate one real review worksheet.
- Make sure the workflow catches:
  - forbidden drift in upstream-owned paths
  - upstream-owned diffs that were not explicitly approved as minimal patches
  - changed files missing from inventory coverage
  - missing human review records on baseline changes

## 8. Keep the process minimal

- Do not add three different ledgers for the same fact.
- Prefer one inventory plus one review-record layer over sprawling narrative logs.
- Keep the workflow strict where mistakes are costly and lightweight where maintenance would otherwise become theater.
