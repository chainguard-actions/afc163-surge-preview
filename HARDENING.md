<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.11.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **afc163--surge-preview/v1.11.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The `surge_token` input in action.yml has a hardcoded literal API token `6973bdb764f0d5fd07c910de27e2d7d0` set as its default value. This is a real surge.sh credential embedded directly in the action definition, not a GitHub Actions secret expression. Any user of this action who does not supply their own token will silently use this shared credential, and the token is publicly exposed to anyone reading the action source.

Locations:

- `action.yml:7`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials

**Notes:**

Removed the hardcoded surge.sh API token `6973bdb764f0d5fd07c910de27e2d7d0` from the `surge_token` input's default value in action.yml (line 7). Replaced the `default:` field with `required: true` so that users must explicitly provide their own token via a GitHub Actions secret (e.g., `${{ secrets.SURGE_TOKEN }}`). This eliminates the publicly exposed shared credential and prevents silent use of a compromised token.

