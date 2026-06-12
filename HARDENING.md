<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.13.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **afc163--surge-preview/v1.13.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The `surge_token` input in action.yml has a hardcoded literal default value `6973bdb764f0d5fd07c910de27e2d7d0`, which appears to be a real surge.sh API token. This is a non-expression literal credential (not a `${{ secrets.* }}` reference) embedded directly in the action definition. Any workflow using this action without explicitly providing a `surge_token` input will silently use this hardcoded token, potentially allowing unauthorized deployments to the token owner's surge.sh account and exposing the credential to all users of the action.

Locations:

- `action.yml:7`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials

**Notes:**

Removed the hardcoded surge.sh API token '6973bdb764f0d5fd07c910de27e2d7d0' from the `surge_token` input's default value in action.yml. Replaced the `default: '6973bdb764f0d5fd07c910de27e2d7d0'` with `required: true` so that users must explicitly provide their own surge.sh token via secrets (e.g., `${{ secrets.SURGE_TOKEN }}`). This prevents unauthorized deployments using the hardcoded token and eliminates the credential exposure risk.

