<!-- markdownlint-disable -->

# Hardening Report: azure--setup-kubectl/v3.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **azure--setup-kubectl/v3.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of full 40-character SHA commit digests, making them vulnerable to supply-chain attacks. Failing references: defaultLabels.yml: actions/stale@v3 (×2); integration-tests.yml: actions/checkout@v2, actions/setup-python@v2; prettify-code.yml: actions/checkout@v2, actionsx/prettier@v2; release-pr.yml: OliverMKing/javascript-release-workflow/.github/workflows/release-pr.yml@main; tag-and-draft.yml: OliverMKing/javascript-release-workflow/.github/workflows/tag-and-release.yml@main; unit-tests.yml: actions/checkout@v1.

Locations:

- `.github/workflows/defaultLabels.yml:9`
- `.github/workflows/defaultLabels.yml:19`
- `.github/workflows/integration-tests.yml:18`
- `.github/workflows/integration-tests.yml:29`
- `.github/workflows/prettify-code.yml:10`
- `.github/workflows/prettify-code.yml:13`
- `.github/workflows/release-pr.yml:12`
- `.github/workflows/tag-and-draft.yml:7`
- `.github/workflows/unit-tests.yml:13`

### missing-permissions (severity: medium)

None of the workflow files define a top-level or job-level 'permissions:' block. Without explicit permissions, workflows inherit the default (often write-all) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/defaultLabels.yml:1`
- `.github/workflows/integration-tests.yml:1`
- `.github/workflows/prettify-code.yml:1`
- `.github/workflows/release-pr.yml:1`
- `.github/workflows/tag-and-draft.yml:1`
- `.github/workflows/unit-tests.yml:1`

### script-injection (severity: high)

Rule (b) violation: In integration-tests.yml, the env var PR_BASE_REF is set from the attacker-controllable value '${{ github.event.pull_request.base.ref }}' and then used unquoted in a run: block — both as 'echo $PR_BASE_REF' and in 'if [[ $PR_BASE_REF != releases/* ]]'. Unquoted expansion allows shell metacharacter injection (semicolons, pipes, command substitution, glob characters, etc.) from a PR author-controlled value.

Locations:

- `.github/workflows/integration-tests.yml:24`
- `.github/workflows/integration-tests.yml:25`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 6 workflow files:
1. defaultLabels.yml: Pinned actions/stale@v3 to SHA 98ed4cb500039dbcccf4bd9bedada4d0187f2757 (×2); added permissions: {issues: write, pull-requests: write}.
2. integration-tests.yml: Pinned actions/checkout@v2 to SHA ee0669bd1cc54295c223e0bb666b733df41de1c5 and actions/setup-python@v2 to SHA e9aba2c848f5ebd159c070c61ea2c4e2b122355e; added permissions: {contents: read}; fixed script injection by moving PR_BASE_REF from job-level env to step-level env and quoting all uses of $PR_BASE_REF in the run block.
3. prettify-code.yml: Pinned actions/checkout@v2 to SHA ee0669bd1cc54295c223e0bb666b733df41de1c5 and actionsx/prettier@v2 to SHA 8b6d14bd1241e743fa9a6112b43d691d1890a8e9; added permissions: {contents: read}.
4. release-pr.yml: Pinned OliverMKing/javascript-release-workflow reusable workflow @main to SHA a2f171c6ca04fea2de31f4dbb6dc576140395512; added permissions: {}.
5. tag-and-draft.yml: Pinned OliverMKing/javascript-release-workflow reusable workflow @main to SHA a2f171c6ca04fea2de31f4dbb6dc576140395512; added permissions: {}.
6. unit-tests.yml: Pinned actions/checkout@v1 to SHA 50fbc622fc4ef5163becd7fab6573eac35f8462e; added permissions: {contents: read}.

