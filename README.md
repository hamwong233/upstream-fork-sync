# Upstream Fork Sync

A reusable workflow for teams that maintain a long-lived fork, branded edition, customer variant, or secondary-development branch on top of an active upstream project.

This package is designed for the hard case:

- upstream keeps changing;
- your fork has product behavior that must not be lost;
- "merge until it compiles" is not reliable enough;
- you want a repeatable process that does not quietly miss upstream fixes.

It is not a generic workflow for every repository. It is best for projects that have both:

- a real upstream relationship; and
- meaningful fork-only behavior that must survive repeated upstream syncs.

## What This Package Contains

- `SKILL.md`
  The agent-facing entrypoint. This is the operational instruction set used by Codex.
- `agents/openai.yaml`
  UI metadata for skill discovery.
- `references/adoption-checklist.md`
  First-time setup checklist for bringing the workflow into a repository.
- `references/inventory-template.yaml`
  Machine-readable feature inventory template.
- `references/sync-review-template.md`
  Human review worksheet template for broad upstream syncs.
- `references/verification-checklist.md`
  Checklist for overlay, record, and closeout verification.
- `references/examples.md`
  Practical examples for white-label forks, enterprise branches, frontend variants, and narrow-fix versus broad-sync decisions.
- `references/example-inventory.yaml`
  A filled example inventory for a hypothetical long-lived product fork.
- `references/example-sync-review.md`
  A filled example review worksheet showing keep/adapt/drop decisions on a broad upstream sync.

## Core Idea

Treat a maintained fork as three layers:

1. Upstream baseline
   The authoritative base for upstream-owned files.
2. Feature inventory
   The machine-readable record of what the fork adds, where it lives, what upstream files can affect it, and how to verify it.
3. Sync review record
   The human decision log for each broad upstream sync: keep, adapt, drop, or split.

That combination is what prevents two common failure modes:

- missing upstream changes because the fork drifted too far from the base;
- losing fork-only behavior because nobody had a precise inventory of what needed to survive.

## When To Use It

Good fit:

- white-label products;
- enterprise editions;
- customer-specific variants;
- alternate themed frontends derived from a shared upstream app;
- internal distributions of active upstream OSS projects.

Poor fit:

- short-lived spike branches;
- one-off patches with no recurring sync plan;
- repos that do not really depend on an external upstream moving over time.

## The Workflow In One Page

1. Freeze an exact upstream baseline.
2. Keep upstream code as the implementation base.
3. Inventory fork-only behavior by feature group.
4. For broad syncs, create a temporary workspace from upstream, not from the fork.
5. Rebuild any derived frontend/app from the upstream frontend base first.
6. Replay fork behavior feature by feature, not by raw directory overlay.
7. Verify continuously:
   - upstream-owned paths stay upstream-shaped;
   - fork drift stays in approved paths;
   - every changed fork file is covered by the inventory;
   - targeted tests and smoke checks still pass.
8. Merge back only after the replay and review record are complete.

## Quick Start

If you are adopting this workflow in a repo for the first time:

1. Read `references/adoption-checklist.md`.
2. Create one feature inventory from `references/inventory-template.yaml`.
3. Decide which paths must stay upstream-shaped and which paths may carry fork-specific behavior.
4. Add an overlay/path verification command and a record/inventory verification command.
5. Create a review-record directory using `references/sync-review-template.md`.
6. Dry-run the workflow against your current upstream gap before trusting it.

If you prefer starting from something concrete instead of blank templates:

- copy `references/example-inventory.yaml` and adapt it to your repo;
- copy `references/example-sync-review.md` for your first broad sync review record.

## Important Rules

- Do not treat the old fork tree as the implementation base for a broad upstream sync.
- Do not copy the whole fork over upstream and call that a rebuild.
- Do not preserve drift just because it already existed.
- Keep upstream files as upstream whenever possible, then add only the minimum fork delta.
- Use direct edits on the live fork branch only for narrow changes where the final result is still clearly `upstream + minimal fork delta`.

## Suggested Target Repo Layout

This package does not require a single fixed layout, but a simple starting point is:

```text
your-repo/
├── AGENTS.md
├── docs/
│   ├── fork-feature-inventory.yaml
│   └── fork-sync-reviews/
├── scripts/
│   └── fork-sync/
│       ├── verify-overlay.sh
│       ├── verify-records.sh
│       └── close-session.sh
└── .agents/
    └── skills/
        └── upstream-fork-sync/
```

If your repo already has an established docs/scripts layout, keep that layout and adapt the inventory paths, review paths, and verification commands instead of forcing this exact tree.

## How To Adapt This Package To Another Repo

Minimum changes:

1. Rename any repository-specific file paths in the inventory and review templates.
2. Replace the example feature groups with your real fork feature groups.
3. Point the inventory at your real upstream remote/ref and frozen commit.
4. Wire the verification commands into your local scripts, hooks, or CI.
5. Update `agents/openai.yaml` if you want different discovery text or a different default prompt.

Good first pass:

- keep one inventory file;
- keep one review-record directory;
- start with 4-8 stable feature groups;
- add stricter automation only after the inventory is already useful.

Avoid overfitting the first version. The goal is to make future upstream syncs safer, not to model every historical detail on day one.

## Directory Structure

```text
upstream-fork-sync/
├── README.md
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── adoption-checklist.md
    ├── example-inventory.yaml
    ├── example-sync-review.md
    ├── examples.md
    ├── inventory-template.yaml
    ├── sync-review-template.md
    └── verification-checklist.md
```

## How README And SKILL.md Relate

- `README.md` is the human-facing overview for maintainers evaluating or adopting the workflow.
- `SKILL.md` is the agent-facing operational entrypoint.

If you use this package inside Codex, `SKILL.md` is the source of execution behavior. If you use the package outside Codex, the `references/` files still work as a lightweight fork-maintenance playbook.

## Using This Package Outside Codex

The workflow itself is not Codex-specific.

Outside Codex, you can still use this package as:

- a maintainer playbook;
- a set of inventory and review templates;
- a checklist for designing verification scripts and closeout gates.

The only Codex-specific pieces are:

- `SKILL.md` as an agent entrypoint;
- `agents/openai.yaml` as discovery metadata.

Everything under `references/` is intentionally written so a human team can reuse it even without Codex.

## What A Good First Adoption Looks Like

After one successful setup round, a maintainer should be able to answer:

- What is fork-only behavior here?
- Which upstream files can invalidate each fork feature?
- When do we use a direct edit versus an upstream-based rebuild workspace?
- Did this upstream sync keep, adapt, drop, or split each fork feature?
- Did the final diff stay inside approved fork-owned paths?

## Why This Helps

Teams usually fail fork maintenance in one of two ways:

- they keep rebasing or merging without a durable record of fork-only behavior;
- they protect fork features by letting fork files become their own source of truth, which makes upstream fixes easier to miss over time.

This workflow is designed to avoid both.
