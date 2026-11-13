<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.11.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **afc163--surge-preview/v1.11.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The `surge_token` input has a hardcoded literal credential as its default value: `default: '6973bdb764f0d5fd07c910de27e2d7d0'`. This is a non-expression literal assigned to a token-named input. Remove the default and require callers to pass it from a secret, e.g. `${{ secrets.SURGE_TOKEN }}`.

Locations:

- `action.yml:7`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index e39d52e..5cbedf3 100644
--- a/action.yml
+++ b/action.yml
@@ -4,7 +4,7 @@ author: 'afc163'
 inputs:
   surge_token:
     description: 'surge.sh token'
-    default: '6973bdb764f0d5fd07c910de27e2d7d0'
+    required: true
   github_token:
     description: 'github token'
     required: true
@@ -29,6 +29,7 @@ outputs:
   preview_url:
     description: 'The url for the related PR preview'
 runs:
   using: node20
   main: 'dist/index.js'
 branding:

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, prior-fix

**Notes:**

Removed hardcoded surge_token default from action.yml and marked input required: true (re-applies prior harden patch). Callers must now pass the token from a secret, e.g. ${{ secrets.SURGE_TOKEN }}.

