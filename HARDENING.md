<!-- markdownlint-disable -->

# Hardening Report: Azure--setup-kubectl/vv4.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Azure--setup-kubectl/vv4.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In integration-tests.yml, the env var `PR_BASE_REF` is populated from the attacker-controllable value `${{ github.event.pull_request.base.ref }}` and then used **unquoted** inside a `run:` block: `echo $PR_BASE_REF` and `if [[ $PR_BASE_REF != releases/* ]]`. Unquoted expansion allows shell metacharacters in the branch ref to be interpreted by the shell, enabling command injection. The fix is to double-quote all uses: `echo "$PR_BASE_REF"` and `if [[ "$PR_BASE_REF" != releases/* ]]`.

Locations:

- `.github/workflows/integration-tests.yml:24`
- `.github/workflows/integration-tests.yml:25`

### unpinned-uses (severity: high)

release-pr.yml references `Azure/action-release-workflows/.github/workflows/release_js_project.yaml@v1` using a mutable tag (`@v1`) instead of a pinned 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling a supply-chain attack.

Locations:

- `.github/workflows/release-pr.yml:16`

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (which may be `read-all` or `write-all` depending on org settings), violating the principle of least privilege. Affected files: defaultLabels.yml (job: label-issues), integration-tests.yml (job: run-integration-test), prettify-code.yml (job: prettier), unit-tests.yml (job: build).

Locations:

- `.github/workflows/defaultLabels.yml:1`
- `.github/workflows/integration-tests.yml:1`
- `.github/workflows/prettify-code.yml:1`
- `.github/workflows/unit-tests.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed three security findings: (1) script-injection in integration-tests.yml by double-quoting all uses of $PR_BASE_REF in the run block; (2) unpinned-uses in release-pr.yml by pinning Azure/action-release-workflows to commit SHA 3c677ba5ab58f5c5c1a6f0cfb176b333b1f27405 with # v1 comment; (3) missing-permissions by adding job-level permissions blocks to all four affected workflow files — integration-tests.yml (contents: read), defaultLabels.yml (issues: write, pull-requests: write for the stale action), prettify-code.yml (contents: read), and unit-tests.yml (contents: read).

