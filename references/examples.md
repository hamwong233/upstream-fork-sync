# Examples

Use these examples to adapt the workflow to a real repository without copying any single layout blindly.

## Example 1: White-label SaaS fork

Situation:
- upstream ships a shared product;
- the fork adds branding, billing modules, and a custom dashboard;
- upstream frontend keeps changing and must be pulled regularly.

Recommended shape:
- keep `web/default` or equivalent upstream frontend unchanged;
- create one derived fork frontend/app from the upstream frontend base;
- keep branch-only pages, routes, pricing modules, and branding in fork-owned paths;
- inventory feature groups such as `build-and-sync-tooling`, `auth-and-onboarding`, `billing-and-wallet`, `analytics`, and `fork-homepage`.

Why this workflow fits:
- the fork has visible product behavior, not just a few patches;
- upstream UI drift can easily hide missed fixes if the fork frontend becomes its own base.

## Example 2: Enterprise edition branch

Situation:
- upstream open-source project provides the base product;
- the fork adds enterprise-only backend APIs, admin settings, and SSO providers;
- upstream backend logic changes often, especially auth and billing paths.

Recommended shape:
- treat upstream backend files as the implementation base;
- keep enterprise-only APIs and settings in explicit feature groups;
- record upstream touchpoints for auth, billing, migrations, and admin controllers;
- require focused review whenever upstream changed those touchpoints.

Why this workflow fits:
- the risky part is not the amount of code, but the chance of silently losing enterprise behavior while merging upstream fixes.

## Example 3: Frontend theme variant

Situation:
- upstream app has one main frontend;
- the fork adds a second theme, route composition, or branded landing page;
- most shared tables, forms, hooks, and route shells should stay aligned with upstream.

Recommended shape:
- recreate the fork frontend from the upstream frontend base for broad syncs;
- replay only intentional theme, route, and product-surface deltas;
- drop old shared-component drift if the difference only exists because the fork missed upstream updates.

Why this workflow fits:
- frontend forks drift fastest when maintainers treat old themed files as the new source of truth.

## Example 4: Narrow fix versus broad sync

Use a narrow direct edit when:
- the touched file's upstream base is already known;
- the change is small and local, such as a branch-only route fix, i18n key, copy change, or a tiny adapter on top of an upstream file;
- the final file still clearly reads as `upstream + minimal fork delta`.

Switch to a broad upstream-worktree replay when:
- the task refreshes a shared frontend base, workspace/build layout, or backend baseline;
- upstream and fork both changed the same shared area and the diff is no longer obvious;
- the team is catching up a version jump or auditing many touched feature groups at once.

Rule of thumb:
- if you would need to explain the change with a long exception list, it is probably no longer a narrow fix.

## Example 5: First adoption session

Goal:
- make the workflow operational in one pass instead of writing process docs that nobody can enforce.

Suggested order:
1. Freeze the current upstream baseline.
2. Mark upstream-owned paths versus fork-owned paths.
3. Create one inventory with 4-8 stable feature groups.
4. Add overlay/path verification.
5. Add record/inventory verification.
6. Create one review-record template for broad syncs.
7. Dry-run the checks against the current fork diff and fix whatever they reveal.

Expected output:
- maintainers can now answer `what is fork-only`, `what changed upstream that matters`, and `did this sync forget anything` without relying on memory.
