# Upstream Fork Sync Review

- Generated: 2026-06-07 14:30 UTC
- Inventory: `docs/fork-feature-inventory.yaml`
- Frozen baseline in inventory: `upstream/main@5f3e8c17b8a7c4eab7f24ab2c1f0d3d3a4d88c77`
- Review base: `5f3e8c17b8a7c4eab7f24ab2c1f0d3d3a4d88c77`
- Review target: `upstream/main@9a1fd0d9d3f90c93da9f4ee8900b3c0cb87c51e4`
- Target selection reason: `Chosen because it is the latest upstream main commit that passed the fork's dependency compatibility smoke run.`
- Upstream changed files: `84`
- Matched feature groups: `4`
- Unmatched upstream files: `3`

## Operating Rules

- Complete the decision block for every matched feature before merge-back.
- Classify every unmatched upstream file before treating the sync as finished.
- Keep the final code shape as `upstream base + explicit fork deltas`.

## Global Checklist

- [x] Recreate the enterprise frontend from the target upstream frontend base before replaying fork-specific frontend behavior.
- [x] Keep upstream-owned paths identical to the target upstream after replay, except for explicitly approved minimal upstream patches in `package.json`, `controller/oauth.go`, and `model/subscription.go`.
- [x] Run overlay/path verification during replay and again before merge-back.
- [x] Run focused verification for each feature group that was replayed.
- [x] Record keep/adapt/drop/split decisions in this worksheet before merge-back.

## Feature Review Queue

### Build and sync tooling

- Feature ID: `build-and-sync-tooling`
- Layer: `build`
- Keep strategy: keep upstream as the repository base and re-apply only the minimum build and governance wiring required by the enterprise variant.
- Review trigger: `always`
- Must keep: `true`
- Branch contract:
  The branch still builds `web/enterprise`, keeps the inventory/review workflow,
  and verifies overlay plus record coverage before merge-back.

Changed upstream touchpoints:
- `Dockerfile`
- `package.json`
- `web/default/package.json`

Public APIs / contracts to preserve:
- none

Settings / DTO keys to re-check:
- none

Data / migration contracts to re-check:
- none

Human review checklist:
- [x] Confirm the enterprise frontend still builds without moving branch logic into upstream-owned frontend paths.
- [x] Confirm the sync scripts still match the actual maintenance workflow.

Review questions:
- [x] Did upstream change the build layout enough that part of the fork wiring can now be deleted?
- [x] Should any guard or script become shared rather than fork-only?

Verification hooks:
- `bash scripts/fork-sync/verify-records.sh --worktree`
- `bash scripts/fork-sync/verify-overlay.sh`
- `bun run --filter acme-enterprise-web build`

Decision:
- Status: `adapt`
- Replay plan: keep upstream root workspace files, then re-apply only the enterprise workspace entry and verification script wiring.
- Notes: upstream changed the frontend workspace layout; the fork kept the upstream package graph and re-added only one enterprise workspace package plus guard scripts.
- Verification result: `passed`

### Auth and enterprise SSO

- Feature ID: `auth-and-enterprise-sso`
- Layer: `backend+frontend`
- Keep strategy: preserve upstream auth behavior first, then add the minimum SSO and organization-aware fork behavior.
- Review trigger: `always`
- Must keep: `true`
- Branch contract:
  Enterprise users can still sign in through SAML/OIDC and be provisioned into the correct organization context without replacing the upstream auth flow.

Changed upstream touchpoints:
- `controller/oauth.go`
- `model/user.go`
- `web/default/src/features/auth/sign-in-form.tsx`

Public APIs / contracts to preserve:
- SSO callback flow
- organization-aware provisioning

Settings / DTO keys to re-check:
- `SamlEnabled`
- `OidcIssuer`

Data / migration contracts to re-check:
- identity-provider mapping records remain compatible with existing enterprise tenants

Human review checklist:
- [x] Re-check SSO callbacks and provisioning against the new upstream auth behavior.
- [x] Keep upstream route and DTO shape as the base unless the enterprise contract truly requires additive fields.

Review questions:
- [x] Did upstream add equivalent auth behavior that lets the fork code shrink?
- [x] Did upstream change sign-in DTOs or routes enough to require adapters?

Verification hooks:
- `go test ./controller ./model ./service ./oauth`
- `smoke enterprise sign-in flow`

Decision:
- Status: `adapt`
- Replay plan: start from upstream auth handlers and forms, then re-add only the enterprise SSO buttons, callback wiring, and organization-provisioning branch behavior.
- Notes: upstream changed the sign-in form layout and user bootstrap flow; old fork files were not reused as the base.
- Verification result: `passed`

### Billing and entitlements

- Feature ID: `billing-and-entitlements`
- Layer: `backend+frontend`
- Keep strategy: keep upstream billing logic as the base and re-apply only the enterprise-specific entitlement and reporting layer.
- Review trigger: `always`
- Must keep: `true`
- Branch contract:
  Contract-aware billing, entitlement summaries, and invoice export must survive without forking the upstream billing engine wholesale.

Changed upstream touchpoints:
- `controller/subscription.go`
- `model/subscription.go`
- `service/quota.go`

Public APIs / contracts to preserve:
- entitlement summary endpoint
- contract-aware billing payloads

Settings / DTO keys to re-check:
- `EnterpriseBillingEnabled`
- `SeatEnforcementMode`

Data / migration contracts to re-check:
- subscription and entitlement records remain backward compatible across the sync
- invoice export schema stays readable for existing downstream consumers

Human review checklist:
- [x] Re-check seat enforcement, invoice reporting, and entitlement summaries against the new upstream billing flow.
- [x] Confirm new upstream pricing behavior is adopted first before re-adding fork specifics.

Review questions:
- [x] Did upstream cover part of the reporting or subscription behavior already?
- [x] Can any enterprise-only DTO now become an additive field on the upstream payload?

Verification hooks:
- `go test ./controller ./model ./service`
- `smoke billing admin pages`

Decision:
- Status: `split`
- Replay plan: keep upstream subscription settlement unchanged, re-apply enterprise entitlement summaries as additive service/controller logic, and drop one obsolete legacy invoice formatter.
- Notes: upstream now covers one part of the old reporting flow, so that code was deleted instead of replayed.
- Verification result: `passed`

### Branded frontend and navigation

- Feature ID: `branded-frontend-and-navigation`
- Layer: `frontend`
- Keep strategy: copy the upstream frontend base first, then replay only the intentional enterprise navigation and branded-product deltas.
- Review trigger: `always`
- Must keep: `true`
- Branch contract:
  The enterprise frontend still has branded landing pages, a custom navigation structure, and compatibility redirects, while shared UI infrastructure stays identical to upstream by default.

Changed upstream touchpoints:
- `web/default/src/routes/__root.tsx`
- `web/default/src/components/app-sidebar.tsx`
- `web/default/src/features/home/**`

Public APIs / contracts to preserve:
- enterprise route structure
- compatibility redirects

Settings / DTO keys to re-check:
- none

Data / migration contracts to re-check:
- none

Human review checklist:
- [x] Confirm shared frontend infrastructure still follows upstream instead of preserving old fork drift.
- [x] Re-check homepage, navigation, and compatibility routes after upstream shell changes.

Review questions:
- [x] Did upstream add shared UI that makes any fork-only component unnecessary?
- [x] Are any current route differences still product requirements, or only historical drift?

Verification hooks:
- `bun run --filter acme-enterprise-web build`
- `smoke homepage, navigation, and admin routes`

Decision:
- Status: `keep`
- Replay plan: recreate `web/enterprise` from upstream `web/default`, then re-apply only branded pages, navigation composition, and compatibility routes.
- Notes: two old sidebar overrides were dropped because upstream now provides the shared behavior directly.
- Verification result: `passed`

## Unmatched Upstream Files

- `docs/changelog.md`
- `scripts/dev/benchmark.sh`
- `web/default/src/features/experiments/mock-data.ts`

Unmatched-file resolution:
- [x] `docs/changelog.md` is irrelevant to fork behavior and needs no inventory mapping.
- [x] `scripts/dev/benchmark.sh` is tooling-only upstream drift with no fork contract impact.
- [x] `web/default/src/features/experiments/mock-data.ts` is a shared frontend touchpoint candidate; add it to the inventory only if the fork later depends on that feature area.

## Final Sign-Off

- [x] Upstream-owned path diff to target upstream is empty except for explicitly approved minimal upstream patches.
- [x] Remaining fork diff is limited to audited fork-owned files and explicitly approved minimal upstream patch files.
- [x] Inventory still reflects the real fork contract after replay.
- [x] This worksheet records what was kept, adapted, dropped, or split.
- [x] Frozen baseline metadata was updated only after verification passed.
