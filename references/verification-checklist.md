# Verification Checklist

Use this checklist when defining or auditing the fork-maintenance guardrails.

## Overlay / path verification

The overlay check should answer:

- Did upstream-owned paths stay upstream-shaped?
- Are current fork differences confined to approved fork paths?
- Is every changed fork file covered by an inventoried feature group?

Good candidates:

- a CLI command
- a script wrapper
- CI enforcement
- a local closeout command that checks the working tree, not just committed history

## Record verification

The record check should answer:

- Did a change alter feature coverage, touchpoints, contracts, tests, or workflow behavior?
- If yes, was the inventory updated?
- If the frozen upstream baseline changed, was a sync review worksheet added?

Do not require maintainers to update redundant narrative docs just to satisfy a hook.

## Broad-sync verification

Before merge-back, confirm:

- upstream base rebuild workspace was used
- feature-oriented replay happened group by group
- review worksheet decisions were completed
- targeted tests/builds passed
- admin-only or sensitive fork diagnostics still do not leak incorrectly

## Narrow-fix verification

For in-place branch fixes, confirm:

- the upstream base for the touched file is known
- the final file still reads as `upstream + minimal fork delta`
- any inventory change that was truly required was made in the same turn

## Closeout output

A good closeout command should tell maintainers clearly:

- whether hooks are active
- whether working-tree record checks passed
- whether overlay/path checks passed
- what status should be repeated in the final handoff
