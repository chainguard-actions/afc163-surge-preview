<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.13.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **afc163--surge-preview/v1.13.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The `surge_token` input in action.yml has a hardcoded literal default value `6973bdb764f0d5fd07c910de27e2d7d0` — a real surge.sh API token embedded directly in the action definition. This is not a GitHub Actions secret expression and will be exposed to anyone who uses the action without supplying their own token.

Locations:

- `action.yml:6`

### script-injection (severity: high)

Two `run:` steps in preview.yml directly interpolate `${{ steps.*.outputs.preview_url }}` inside shell commands (sub-rule a). Any `${{ ... }}` expression is substituted by the Actions runner before the shell sees the string, allowing a maliciously crafted output value to inject arbitrary shell commands. Offending lines: `run: echo "url => ${{ steps.preview_step.outputs.preview_url }}"` (line 34) and `run: echo "url => ${{ steps.preview_action.outputs.preview_url }}"` (line 57).

Locations:

- `.github/workflows/preview.yml:34`
- `.github/workflows/preview.yml:57`

### unpinned-uses (severity: high)

Both workflow files reference `actions/checkout@v4` using a mutable version tag instead of a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling a supply-chain attack. All `uses:` references must be pinned to a full SHA (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `.github/workflows/preview.yml:15`
- `.github/workflows/preview.yml:38`
- `.github/workflows/test.yml:14`

### missing-permissions (severity: medium)

The workflow file test.yml has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g. write access to contents). A minimal explicit `permissions:` block should be added.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, script-injection, unpinned-uses, missing-permissions

**Notes:**

1. action.yml: Removed hardcoded surge.sh API token '6973bdb764f0d5fd07c910de27e2d7d0' from surge_token default; marked input as required: true instead.
2. preview.yml: Fixed two script-injection issues by moving ${{ steps.*.outputs.preview_url }} expressions into env: blocks (PREVIEW_URL) and referencing $PREVIEW_URL in the shell run commands. Also pinned both actions/checkout@v4 references to SHA 11d5960a326750d5838078e36cf38b85af677262 # v4.
3. test.yml: Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262 # v4. Added top-level permissions: contents: read block.

