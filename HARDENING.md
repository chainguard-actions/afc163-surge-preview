<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.11.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **afc163--surge-preview/v1.11.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The `surge_token` input in action.yml has a hardcoded literal default value `6973bdb764f0d5fd07c910de27e2d7d0`, which appears to be a real Surge.sh API token. Hardcoding credentials as default input values exposes them to anyone who uses or forks this action. The default should be removed and users should be required to supply their own token via a secret reference.

Locations:

- `action.yml:6`

### script-injection (severity: high)

Two `run:` steps in preview.yml directly interpolate `${{ steps.*.outputs.preview_url }}` expressions inside shell commands. Sub-rule (a): any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted by the GitHub Actions template engine before the shell parses the command, allowing an attacker who can influence the output value to inject arbitrary shell commands. Offending lines:
  - `run: echo "url => ${{ steps.preview_step.outputs.preview_url }}"`
  - `run: echo "url => ${{ steps.preview_action.outputs.preview_url }}"`
Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `echo "url => $PREVIEW_URL"`.

Locations:

- `.github/workflows/preview.yml:23`
- `.github/workflows/preview.yml:40`

### unpinned-uses (severity: high)

Both workflow files reference `actions/checkout@v4` using a mutable tag instead of a full 40-character commit SHA. Mutable tags can be moved by the upstream repository owner (or an attacker who compromises it), enabling a supply-chain attack. Pin each reference to a specific commit SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/preview.yml:11`
- `.github/workflows/preview.yml:27`
- `.github/workflows/test.yml:14`

### missing-permissions (severity: medium)

Neither `preview.yml` nor `test.yml` declares a `permissions:` block at the top level or on any individual job. Without explicit permissions, workflows inherit the repository's default token permissions (which may be `write-all` for older repositories). Explicitly setting minimal permissions (e.g. `permissions: read-all` or scoped per-job) reduces the blast radius if the workflow is compromised.

Locations:

- `.github/workflows/preview.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all four findings: (1) Removed hardcoded surge token default '6973bdb764f0d5fd07c910de27e2d7d0' from action.yml, replacing with 'required: true'. (2) Moved ${{ steps.*.outputs.preview_url }} expressions in preview.yml into env: blocks as PREVIEW_URL, referencing as $PREVIEW_URL in shell. (3) Pinned all actions/checkout@v4 references to SHA 34e114876b0b11c390a56381ad16ebd13914f8d5 # v4 in both preview.yml and test.yml. (4) Added permissions blocks: 'contents: read' + 'pull-requests: write' to preview.yml, and 'contents: read' to test.yml.

