---
name: upstream-fork-sync
description: Use when maintaining a long-lived fork or secondary-development branch that must keep syncing upstream without losing branch-specific features. Trigger for requests about "sync upstream", "rebuild on top of upstream", "preserve our custom features", "secondary development", "长期维护 fork", "二次开发同步上游", "不要漏上游更新", or when a project needs a repeatable workflow for upstream rebase/rebuild with explicit feature inventory and review checkpoints.
---

# Upstream Fork Sync

Use this skill for projects that maintain a long-lived fork, branded edition, customer variant, or secondary-development branch on top of an upstream repository.

The goal is not "merge until it compiles". The goal is:

- keep the upstream codebase as the implementation base;
- preserve intentional fork-only behavior;
- avoid leaking fork customization into upstream-owned paths, except for explicitly approved minimal patches;
- make future upstream syncs safer instead of harder.

## When This Skill Fits

Use this skill when the project has all or most of these traits:

- there is a clear upstream repository, branch, tag, or release baseline;
- the fork carries real product or business behavior beyond a few local patches;
- upstream continues to evolve and must be re-synced regularly;
- the team wants to avoid missed upstream fixes and avoid losing fork features.

Do not use this skill for a tiny one-off patch on top of upstream unless the user explicitly wants to establish a reusable sync workflow.

## Fast Start

Use this sequence when applying the workflow to a repo for the first time:

1. Read `references/adoption-checklist.md` and confirm the repo really has a recurring upstream-sync problem.
2. Create one machine-readable feature inventory from `references/inventory-template.yaml`.
3. Define the overlay/path and record checks using `references/verification-checklist.md`.
4. For the first broad upstream sync, generate one review worksheet from `references/sync-review-template.md`.

If you need concrete examples before choosing the workflow shape, read `references/examples.md`.

## Core Model

Treat the project as three layers:

1. Upstream baseline
   This is the code truth for upstream-owned files.
2. Feature inventory
   This is the machine-readable contract for what the fork adds, where it lives, what upstream files it depends on, and how to verify it.
3. Sync review record
   This is the human decision log for each broad upstream sync: keep, adapt, drop, or split.

If the project also keeps a narrative changelog, treat it as optional history, not as the primary sync mechanism.

## Bundled References

Load these references only when needed:

- `references/adoption-checklist.md`
  Use when introducing this workflow into a repo for the first time.
- `references/inventory-template.yaml`
  Use when creating or reshaping the machine-readable feature inventory.
- `references/sync-review-template.md`
  Use when running a broad upstream sync and recording keep/adapt/drop decisions.
- `references/verification-checklist.md`
  Use when defining overlay checks, closeout gates, and final merge-back verification.
- `references/examples.md`
  Use when deciding whether a repo is a good fit, how to split feature groups, or whether a task is a narrow direct edit versus a broad upstream sync.
- `references/example-inventory.yaml`
  Use when you want a concrete machine-readable example instead of a blank template.
- `references/example-sync-review.md`
  Use when you want to see what a completed broad-sync review worksheet looks like in practice.

## Required Workflow

### 1. Freeze the upstream baseline

Pick an exact branch, tag, release, or commit and freeze it for the whole sync run.

Always record:

- upstream remote/ref;
- frozen commit hash;
- why that baseline was chosen if multiple candidates exist.

### 2. Build or refresh a feature inventory

Create one machine-readable inventory file that records, per feature group:

- feature id and title;
- layer (`build`, `frontend`, `backend`, `backend+frontend`, etc.);
- summary of the fork-only contract;
- branch-owned paths;
- approved minimal upstream patch paths, if any;
- upstream touchpoints to re-review on sync;
- API/settings/data contracts that must survive;
- verification hooks;
- human review checklist/questions.

The inventory is the primary defense against missing upstream changes later.

If the repo does not already have this structure, start from `references/inventory-template.yaml`.

### 3. Use an upstream-based rebuild workspace for broad syncs

For a broad upstream update:

- create a temporary branch or worktree from the chosen upstream baseline;
- keep the full repository identical to upstream first, except for explicitly replayed fork deltas;
- only then re-apply fork behavior feature by feature.

Do not broad-sync by patching the existing fork branch in place.

### 4. Recreate derived frontend variants from upstream base

If the fork has a derived frontend theme/app variant:

- rebuild that variant from the upstream frontend base first;
- then layer back only intentional fork deltas.

Do not use the old fork frontend tree as the implementation base.

### 5. Replay by feature group, not raw directory overlay

Use feature-oriented replay:

- identify the real fork feature behavior;
- locate the upstream files that changed underneath it;
- re-apply the smallest valid fork delta on top of upstream files;
- drop fork code when upstream now provides the same behavior.

Prefer:

- reviewed cherry-picks;
- small manual ports;
- narrow adapters;
- branch-only route/page/module files where appropriate.

Avoid:

- giant directory copies;
- treating old fork files as the base;
- preserving drift only because it existed before.

### 6. Create a sync review worksheet for every broad upstream sync

For each broad sync round, generate or maintain one review document that lists:

- upstream files changed since the old frozen baseline;
- matched feature groups;
- unmatched upstream files;
- decision for each feature: `keep`, `adapt`, `drop`, or `split`;
- verification outcome.

This is the human judgment layer that automation cannot replace safely.

If the repo does not already have a review format, start from `references/sync-review-template.md`.

### 7. Verify continuously

During replay and before merge-back, check:

- upstream-owned paths stay identical to upstream unless the inventory explicitly approves a minimal patch there;
- fork differences stay inside approved fork paths or approved minimal upstream patch paths;
- every changed fork file is covered by inventory;
- feature-group verification still passes.

### 8. Merge back only after verification

Bring the rebuild result back to the real fork branch only after:

- overlay/path validation passes;
- feature coverage is inventoried;
- required human review records exist;
- any remaining upstream-owned diffs are explicitly approved as minimal patches in inventory;
- targeted tests/builds pass.

## Direct-Edit Exception

Direct edits on the live fork branch are acceptable only for narrow changes when:

- the upstream base for the touched file is already known;
- the final file can still be explained as `upstream + minimal fork delta`;
- the change does not amount to a broad sync, shared-infra refresh, or baseline rewrite.

If that proof is not easy, switch back to the upstream-worktree replay workflow.

## Inventory Design Guidance

A good inventory should be stable and reviewable.

Per feature group, capture:

- `branch_paths`: where fork-owned changes live;
- `approved_upstream_patch_paths`: narrow upstream-owned files that may carry a documented minimal fork patch;
- `upstream_touchpoints`: upstream files that can invalidate this feature on sync;
- `api_contracts`: public API/DTO/route/setting contracts to preserve;
- `data_contracts`: persisted data, migration, seed, or compatibility contracts to preserve;
- `tests`: commands or smoke checks;
- `review.keep_strategy`: how to preserve the feature on top of upstream;
- `review.checklist`: what must be re-checked by a human;
- `review.questions`: what tradeoffs or upstream overlap must be judged.

Keep the inventory concise but specific enough that another maintainer could run the sync months later without relying on chat history.

## Anti-Patterns

Do not:

- sync by copying the whole fork tree over upstream;
- move fork customization into upstream-owned paths just to reduce diff count;
- treat old fork code as the base because it feels easier;
- rely on a giant diff as the primary implementation method;
- keep every historical difference "just in case";
- depend on memory instead of an inventory/review trail.

## What Makes This Generalizable

This method works well for:

- white-label products;
- enterprise/customer variants;
- long-lived product forks;
- re-skinned dashboards or alternate frontend editions;
- internal distributions of active upstream OSS projects.

It is less useful for:

- short-lived spike branches;
- single-patch forks with no product surface;
- repos without a real ongoing upstream relationship.

## Default Adoption Shape

When no stronger project-specific structure exists yet, start with:

- one inventory file for fork features;
- one directory for broad-sync review records;
- one overlay/path verification command;
- one record/inventory verification command;
- one closeout command or checklist;
- one documented direct-edit exception.

This is usually enough structure to prevent missed upstream changes without turning the workflow into ceremony.

## Recommended Deliverables

When introducing this workflow into a project, aim to leave behind:

- one inventory file;
- one sync workflow doc;
- one review-record directory/template;
- one overlay/record verification path;
- one closeout command or checklist;
- one agreed rule for when direct edits are acceptable.

That set is usually enough to make future upstream syncs repeatable without over-documenting the repo.

For the first adoption pass, use `references/adoption-checklist.md` and `references/verification-checklist.md` to make sure the workflow is operational rather than merely documented.
