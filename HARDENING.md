<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.10.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **afc163--surge-preview/v1.10.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The `surge_token` input in action.yml has a hardcoded literal default value `6973bdb764f0d5fd07c910de27e2d7d0`. This is a real Surge.sh token embedded directly in the action definition, exposing it to anyone who reads the repository. It should be removed and replaced with a required secret (no default).

Locations:

- `action.yml:6`

### script-injection (severity: high)

Two `run:` steps in preview.yml directly interpolate `${{ steps.*.outputs.preview_url }}` (a `steps.*.outputs.*` context value) into shell commands via template substitution before the shell ever sees the string. Sub-rule (a) violation: any `${{ ... }}` expression inside a `run:` block is a script-injection risk. Offending lines:
- Line 24: `run: echo "url => ${{ steps.preview_step.outputs.preview_url }}"`
- Line 40: `run: echo "url => ${{ steps.preview_action.outputs.preview_url }}"`
These should be moved to an `env:` block and the env var double-quoted in the shell command.

Locations:

- `.github/workflows/preview.yml:24`
- `.github/workflows/preview.yml:40`

### missing-permissions (severity: medium)

Neither `preview.yml` nor `test.yml` declares a top-level `permissions:` key, and no individual job within either file declares its own `permissions:` block. Without explicit permissions, workflows run with the repository's default token permissions, which may be overly broad (e.g. write access to contents). Both files should declare minimal required permissions.

Locations:

- `.github/workflows/preview.yml:1`
- `.github/workflows/test.yml:1`

### unpinned-uses (severity: high)

All `uses:` references in the workflow files pin to a mutable version tag (`@v4`) rather than an immutable 40-character commit SHA. A compromised or force-pushed tag could silently substitute malicious code. Affected references:
- `actions/checkout@v4` (preview.yml lines 11 and 28)
- `actions/checkout@v4` (test.yml line 15)
These should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/preview.yml:11`
- `.github/workflows/preview.yml:28`
- `.github/workflows/test.yml:15`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, script-injection, missing-permissions, unpinned-uses

**Notes:**

Fixed all four findings: (1) Removed hardcoded surge token default '6973bdb764f0d5fd07c910de27e2d7d0' from action.yml surge_token input and made it required: true. (2) Moved both ${{ steps.*.outputs.preview_url }} expressions in preview.yml into env: blocks (PREVIEW_URL) and referenced as $PREVIEW_URL in shell. (3) Added permissions blocks: preview.yml gets 'contents: read, pull-requests: write, statuses: write' (needed for PR preview functionality); test.yml gets 'contents: read' (only needs checkout). (4) Pinned all three actions/checkout@v4 references to SHA 11d5960a326750d5838078e36cf38b85af677262 # v4.

