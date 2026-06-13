<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.11.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **afc163--surge-preview/v1.11.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The `surge_token` input in action.yml has a hardcoded literal default value `6973bdb764f0d5fd07c910de27e2d7d0`, which appears to be a real Surge.sh API token embedded directly in the action definition. Any workflow that uses this action without explicitly supplying a `surge_token` input will authenticate to Surge.sh using this shared, publicly-exposed token. The value should be removed and callers should be required to supply their own token via a GitHub Actions secret (e.g., `${{ secrets.SURGE_TOKEN }}`).

Locations:

- `action.yml:7`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials

**Notes:**

Removed the hardcoded Surge.sh API token ('6973bdb764f0d5fd07c910de27e2d7d0') from the 'surge_token' input default value in action.yml. The input is now marked as 'required: true' with an updated description instructing callers to supply the token via '${{ secrets.SURGE_TOKEN }}'. This prevents the publicly-exposed shared token from being used by any workflow that omits the input.

