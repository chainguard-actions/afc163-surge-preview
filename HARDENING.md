<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.10.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **afc163--surge-preview/v1.10.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The `surge_token` input in action.yml has a hardcoded literal default value `6973bdb764f0d5fd07c910de27e2d7d0`, which appears to be a real surge.sh API token. This is not a GitHub Actions secret expression — it is a plain alphanumeric string embedded directly in the action definition. Any workflow that uses this action without explicitly supplying a `surge_token` input will silently use this hardcoded credential, exposing it to all users and logs.

Locations:

- `action.yml:7`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials

**Notes:**

Removed the hardcoded surge.sh API token ('6973bdb764f0d5fd07c910de27e2d7d0') from the default value of the surge_token input in action.yml. Replaced the default with `required: true` so that callers must explicitly supply their own token via a GitHub Actions secret (e.g., ${{ secrets.SURGE_TOKEN }}). This prevents the credential from being silently used by any workflow that omits the input.

