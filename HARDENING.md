<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.12.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **afc163--surge-preview/v1.12.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

A hardcoded surge.sh API token ('6973bdb764f0d5fd07c910de27e2d7d0') is set as the default value for the 'surge_token' input in action.yml. Any workflow that calls this action without explicitly providing a surge_token will silently use this literal credential, exposing it to all users and potentially allowing unauthorized deployments to the associated surge.sh account. The value should be removed and callers should be required to supply their own token via a GitHub Actions secret (e.g., ${{ secrets.SURGE_TOKEN }}).

Locations:

- `action.yml:7`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials

**Notes:**

Removed the hardcoded surge.sh API token ('6973bdb764f0d5fd07c910de27e2d7d0') from the 'surge_token' input's default value in action.yml. Changed the input to 'required: true' so callers must explicitly supply their own token via a GitHub Actions secret (e.g., ${{ secrets.SURGE_TOKEN }}). Updated the description to guide users accordingly.

