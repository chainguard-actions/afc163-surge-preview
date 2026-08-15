<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.11.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **afc163--surge-preview/v1.11.1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The `surge_token` input in action.yml has a hardcoded literal default value `6973bdb764f0d5fd07c910de27e2d7d0`, which appears to be a real Surge.sh API token. Hardcoded credentials in action definitions are exposed to anyone who reads the repository and can be abused to deploy to the associated Surge.sh account. The default should be removed or replaced with a secrets expression.

Locations:

- `action.yml:6`

### script-injection (severity: high)

Two `run:` steps in preview.yml directly interpolate `${{ steps.*.outputs.preview_url }}` inside shell command strings (sub-rule a). All `${{ ... }}` expressions are substituted by the Actions template engine before the shell executes them, meaning a maliciously crafted output value could inject arbitrary shell commands. The value should be passed via an `env:` variable and referenced as `"$ENV_VAR"` instead. Offending lines: `run: echo "url => ${{ steps.preview_step.outputs.preview_url }}"` and `run: echo "url => ${{ steps.preview_action.outputs.preview_url }}"`

Locations:

- `.github/workflows/preview.yml:22`
- `.github/workflows/preview.yml:36`

### unpinned-uses (severity: high)

Both workflow files reference `actions/checkout@v4` using a mutable version tag instead of a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, creating a supply-chain risk. Pin each reference to a specific SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/preview.yml:10`
- `.github/workflows/preview.yml:27`
- `.github/workflows/test.yml:14`

### missing-permissions (severity: medium)

Neither `preview.yml` nor `test.yml` declares a top-level `permissions:` key, and none of their individual jobs declare job-level `permissions:` keys. Without explicit permissions, workflows inherit the repository default token permissions (often write-all), granting jobs more access than needed. Add a minimal `permissions:` block at the top level or each job level.

Locations:

- `.github/workflows/preview.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all four findings: (1) Removed hardcoded surge token '6973bdb764f0d5fd07c910de27e2d7d0' from action.yml surge_token default, replacing with required: true. (2) Moved both ${{ steps.*.outputs.preview_url }} expressions in preview.yml into env: blocks as PREVIEW_URL, referencing $PREVIEW_URL in the run: shell commands. (3) Pinned all three actions/checkout@v4 references to SHA 11d5960a326750d5838078e36cf38b85af677262 # v4. (4) Added permissions: contents: read to test.yml and permissions: contents: read, pull-requests: write to preview.yml (pull-requests write needed for the surge-preview action to post PR comments).

