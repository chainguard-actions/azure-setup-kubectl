<!-- markdownlint-disable -->

# Hardening Report: azure--setup-kubectl/v3.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **azure--setup-kubectl/v3.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags or branch names instead of pinned full 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced action is compromised or altered.

- defaultLabels.yml: `actions/stale@v3` (used twice)
- integration-tests.yml: `actions/checkout@v2`, `actions/setup-python@v2`
- prettify-code.yml: `actions/checkout@v2`, `actionsx/prettier@v2`
- release-pr.yml: `OliverMKing/javascript-release-workflow/.github/workflows/release-pr.yml@main`
- tag-and-draft.yml: `OliverMKing/javascript-release-workflow/.github/workflows/tag-and-release.yml@main`
- unit-tests.yml: `actions/checkout@v1`

Locations:

- `.github/workflows/defaultLabels.yml:10`
- `.github/workflows/defaultLabels.yml:20`
- `.github/workflows/integration-tests.yml:19`
- `.github/workflows/integration-tests.yml:31`
- `.github/workflows/prettify-code.yml:10`
- `.github/workflows/prettify-code.yml:13`
- `.github/workflows/release-pr.yml:13`
- `.github/workflows/tag-and-draft.yml:8`
- `.github/workflows/unit-tests.yml:13`

### permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no individual jobs define job-level `permissions:` keys. This means all jobs run with the default (broad) token permissions, violating the principle of least privilege. Each file should declare minimal required permissions.

Locations:

- `.github/workflows/defaultLabels.yml:1`
- `.github/workflows/integration-tests.yml:1`
- `.github/workflows/prettify-code.yml:1`
- `.github/workflows/release-pr.yml:1`
- `.github/workflows/tag-and-draft.yml:1`
- `.github/workflows/unit-tests.yml:1`

### script-injection (severity: high)

Sub-rule (b) violation in integration-tests.yml: The env var `PR_BASE_REF` is populated from the attacker-controllable expression `${{ github.event.pull_request.base.ref }}` (set at job-level `env:`), then used **unquoted** inside the `run:` shell script on two lines:
  - `echo $PR_BASE_REF` (line 24)
  - `if [[ $PR_BASE_REF != releases/* ]]; then` (line 25)

An attacker who controls the PR base ref could inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) that the shell would interpret before quoting takes effect. The fix is to double-quote all expansions: `echo "$PR_BASE_REF"` and `if [[ "$PR_BASE_REF" != releases/* ]]; then`.

Locations:

- `.github/workflows/integration-tests.yml:24`
- `.github/workflows/integration-tests.yml:25`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, script-injection

**Notes:**

Fixed all three findings across six workflow files:

1. unpinned-uses: Pinned all action references to full 40-char SHAs with tag comments preserved:
   - actions/stale@v3 → @98ed4cb500039dbcccf4bd9bedada4d0187f2757 (defaultLabels.yml x2)
   - actions/checkout@v2 → @ee0669bd1cc54295c223e0bb666b733df41de1c5 (integration-tests.yml, prettify-code.yml)
   - actions/setup-python@v2 → @e9aba2c848f5ebd159c070c61ea2c4e2b122355e (integration-tests.yml)
   - actionsx/prettier@v2 → @8b6d14bd1241e743fa9a6112b43d691d1890a8e9 (prettify-code.yml)
   - OliverMKing/javascript-release-workflow/...@main → @a2f171c6ca04fea2de31f4dbb6dc576140395512 (release-pr.yml, tag-and-draft.yml)
   - actions/checkout@v1 → @50fbc622fc4ef5163becd7fab6573eac35f8462e (unit-tests.yml)

2. permissions: Added top-level `permissions: {}` to all six workflow files, plus minimal job-level permissions (contents: read for checkout; issues: write + pull-requests: write for stale action).

3. script-injection: Double-quoted $PR_BASE_REF in integration-tests.yml run script: `echo "$PR_BASE_REF"` and `if [[ "$PR_BASE_REF" != releases/* ]]; then`.

