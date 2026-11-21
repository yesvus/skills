---
name: github-app-browser-bootstrap
description: Register, install, scope, and verify GitHub Apps through an owner's browser, with least-privilege permission separation and secret-safe key escrow to an approved vault.
---

# GitHub App browser bootstrap

Use this skill when an authorized repository owner needs GitHub App registration or first-time installation, which may require the owner's authenticated GitHub browser session. Pair with `browser-mcp-ui-operations` for accessibility snapshots and keyboard fallback.

## Decide the trust model before clicking

- **Operator App:** repository `Administration: read/write`, `Metadata: read`. This is a high-impact identity: it can edit rulesets, including potential bypass settings. Do not give it `Pull requests: write`, `Contents: write`, `Workflows: write`, or secrets permissions merely for convenience.
- **Worker App:** only the code and PR permissions needed for its job, e.g. `Contents: read/write`, `Pull requests: read/write`, `Actions: read`, `Issues: read`, `Metadata: read`. Avoid workflows/admin/secrets permissions unless separately justified.
- **Reviewer App:** independent identity, e.g. `Pull requests: read/write`, `Contents: read`, `Checks: read`, `Metadata: read`. Do not grant content-writing or administration.
- Repository permissions are assigned to the **App**; install scope controls **which repositories** it can access. Distinct installations of one App do not have different permission types.
- Choose `All repositories` only with the owner's explicit approval, because this includes present and future repositories. Otherwise select specific repositories and verify each selection.
- A reviewer identity that is technically separate is not proof of a sound review policy: test real `APPROVED` reviews, last-push rules, check runs, and bypass settings before activating a strict ruleset.

## Registration in GitHub

1. Open the owner's GitHub **Developer settings → GitHub Apps → New GitHub App**. The browser route is generally `https://github.com/settings/apps/new`.
2. Fill a unique App name, a legitimate HTTPS homepage, and a truthful description. Do not invent callback/webhook endpoints.
3. If the App uses installation tokens only and needs no inbound events, leave OAuth/user authorization disabled and disable webhook delivery rather than inserting a bogus webhook URL. Keep installability scoped to the owner's intended account.
4. Expand **Repository permissions**, assign the exact approved roles, and leave every other permission at `No access` except GitHub's automatic metadata permission. Confirm permissions before creation.
5. Submit **Create GitHub App**. Verify successful settings page. Record **App ID**, slug, and requested permission map; App IDs are not secrets.
6. Re-open **Permissions & events** to verify the saved permission types rather than trusting the draft form.

## Installation

1. In the App's settings choose **Install App** and select the intended account.
2. On the permission confirmation screen choose `All repositories` only if that exact scope was authorized; otherwise choose `Only select repositories` and select the target repos.
3. Verify radio `[checked]` state and the permission summary before **Install**.
4. If Playwright semantic clicks time out waiting for stability, focus the correct controls with `Tab`, use arrow keys for a radio choice, then `Enter` on the focused Install button. Re-snapshot between actions. Never click a broader default scope just to work around a broken selector.
5. Confirm the resulting URL follows `/settings/installations/<installation-id>`; record the installation ID and verify actual repository access in **Configure**.

## Private keys and vault escrow

An App can be registered/installed while still lacking a usable private key. Installing it is **not** proof that worker credentials are operational.

- Agree on a restricted vault secret path, field schema, and who may write/read it *before* creating or downloading the key.
- Prefer owner-initiated key generation in **App settings → Credentials / Private keys** with direct entry into the authenticated, approved secrets manager UI. A common schema uses fields like `app-id`, `private-key`, `slug`, `html-url`, and `created-at`, but defer to the team's actual schema.
- Never read, print, screenshot, paste into chat, commit to a repository, log in a tool output, or expose the private key to a general-purpose agent. If browser-download-to-vault transfer cannot be done without agent exposure, stop and request a trusted manual/local transfer.
- Check vault authentication and least-privilege ACLs first. A vault UI that displays a login page is not an authenticated, writable escrow channel. Do not assume OIDC has succeeded because a login button was clicked.
- Confirm escrow using metadata / write success only, without retrieving the private-key value. Remove temporary downloaded key files through the owner's approved local process; do not claim deletion without verification.
- The trusted token issuer may mint short-lived installation tokens for runtime workers. Do not expose a long-lived private key to the worker environment. Test an actual API call with the restricted installation token before declaring readiness.

## Safeguards and acceptance

- GitHub workflow files under `.github/workflows/` require appropriate workflow-write capability in addition to ordinary contents access. Do not widen a broadly installed operator App to workflow-write just to place one bootstrap file. Prefer a narrowly authorized owner action.
- On repositories requiring checks, compare the branch ruleset's `required_status_checks[].context` to the **actual job check name** emitted by CI. A workflow display name and a job key can differ; mismatches may block merges indefinitely.
- Before enforcing review rules, verify there is a functioning independent approver and a green required check. Start in evaluation mode when approval enforcement would lock out merges.
- Avoid an operator App being added to a ruleset bypass list. Use independent, read-only ruleset drift detection; treat admin-write capability as an explicit residual risk.
- No acceptance claim until: App registered, installation succeeded with correct scope, vault key escrow confirmed, short-lived token minted, allowed API action succeeded, denied API action failed, and review/CI gates were exercised.
- Record App ID, installation ID, scoped permissions, completion status, and any Owner-only manual actions. Never record key material or session cookies.

## Idempotence / recovery

Before registering an App, check whether it already exists under Developer settings. Before installing it, check **Installations**. After a timeout, verify the state before retrying to avoid duplicate Apps or installations. If the browser MCP is disconnected, reconnect and re-check account, URL, and saved state before continuing.
