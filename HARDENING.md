<!-- markdownlint-disable -->

# Hardening Report: afc163--surge-preview/v1.13.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **afc163--surge-preview/v1.13.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

In action.yml, the input `surge_token` has a hardcoded literal token as its default value: `default: '6973bdb764f0d5fd07c910de27e2d7d0'`. This is a credential committed in plain text. Remove the default and require callers to pass it from a secret, e.g. `${{ secrets.SURGE_TOKEN }}`.

Locations:

- `action.yml:7`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 83c310b..06d2b52 100644
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
@@ -41,6 +41,7 @@ outputs:
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

Removed hardcoded surge_token default from action.yml and marked input required: true (same as previous harden). Callers must pass the token from a secret, e.g. ${{ secrets.SURGE_TOKEN }}.

