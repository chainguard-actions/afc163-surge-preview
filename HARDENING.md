<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.12.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **afc163--surge-preview/v1.12.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The `surge_token` input in action.yml has a hardcoded literal default value `6973bdb764f0d5fd07c910de27e2d7d0` — a 32-character hex string that appears to be a real Surge.sh API token. This should be removed; callers must supply the token via `${{ secrets.SURGE_TOKEN }}` rather than relying on a baked-in default.

Locations:

- `action.yml:7`

### script-injection (severity: high)

Two `run:` steps in preview.yml directly interpolate `${{ ... }}` expressions into shell commands (sub-rule a). Line 24: `run: echo "url => ${{ steps.preview_step.outputs.preview_url }}"` and line 38: `run: echo "url => ${{ steps.preview_action.outputs.preview_url }}". Any ${{ }} expression inside a run: block is subject to template substitution before the shell sees it, enabling script injection. These values should be passed via an env: variable and then referenced as a quoted shell variable instead.

Locations:

- `.github/workflows/preview.yml:24`
- `.github/workflows/preview.yml:38`

### unpinned-uses (severity: high)

All `uses:` references in the workflow files use mutable tag refs (`@v4`) instead of pinned 40-character commit SHAs. This exposes the workflows to supply-chain attacks if the referenced action tag is moved or compromised. Affected references: `actions/checkout@v4` in preview.yml (lines 10 and 27) and test.yml (line 14).

Locations:

- `.github/workflows/preview.yml:10`
- `.github/workflows/preview.yml:27`
- `.github/workflows/test.yml:14`

### missing-permissions (severity: medium)

Neither `.github/workflows/preview.yml` nor `.github/workflows/test.yml` declares a top-level `permissions:` block, and no individual job within either file declares its own `permissions:` block. Without explicit permissions, workflows run with the repository's default token permissions (which may be `write-all`). Minimal required permissions should be declared explicitly.

Locations:

- `.github/workflows/preview.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all four findings: (1) Removed hardcoded surge token default '6973bdb764f0d5fd07c910de27e2d7d0' from action.yml, replacing it with 'required: true'. (2) Moved both ${{ steps.*.outputs.preview_url }} expressions in preview.yml into env: blocks as PREVIEW_URL, referenced as $PREVIEW_URL in shell. (3) Pinned all three actions/checkout@v4 references to SHA 11d5960a326750d5838078e36cf38b85af677262 with # v4 comment. (4) Added 'permissions: contents: read, pull-requests: write' to preview.yml and 'permissions: contents: read' to test.yml.

