<!-- markdownlint-disable -->

# Hardening Report: Azure--setup-kubectl/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Azure--setup-kubectl/v5.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: The env var PR_BASE_REF is sourced from the untrusted value `${{ github.event.pull_request.base.ref }}` and then expanded unquoted inside a `run:` block. Unquoted shell variable expansion allows an attacker to inject shell metacharacters via the pull request base ref. Offending lines: `echo $PR_BASE_REF` and `if [[ $PR_BASE_REF != releases/* ]]; then`. These should be `echo "$PR_BASE_REF"` and `if [[ "$PR_BASE_REF" != releases/* ]]; then`.

Locations:

- `.github/workflows/integration-tests.yml:26`
- `.github/workflows/integration-tests.yml:27`

### unpinned-uses (severity: high)

The workflow uses a mutable tag reference `@v1` instead of a full 40-character commit SHA for `Azure/action-release-workflows/.github/workflows/release_js_project.yaml@v1`. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling supply-chain attacks.

Locations:

- `.github/workflows/release-pr.yml:16`

### missing-permissions (severity: medium)

These workflow files have no top-level `permissions:` block and no job-level `permissions:` block on any of their jobs. Without explicit permissions, the GITHUB_TOKEN is granted its default (potentially broad) permissions. Each workflow should declare minimal required permissions.

Locations:

- `.github/workflows/defaultLabels.yml:1`
- `.github/workflows/integration-tests.yml:1`
- `.github/workflows/prettify-code.yml:1`
- `.github/workflows/unit-tests.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three finding types across five workflow files: (1) script-injection in integration-tests.yml — quoted $PR_BASE_REF in both echo and if-statement to prevent shell metacharacter injection; (2) unpinned-uses in release-pr.yml — pinned Azure/action-release-workflows to full SHA 3c677ba5ab58f5c5c1a6f0cfb176b333b1f27405 with # v1 comment; (3) missing-permissions — added top-level `permissions: {}` to integration-tests.yml, defaultLabels.yml, prettify-code.yml, and unit-tests.yml (release-pr.yml already had job-level permissions).

