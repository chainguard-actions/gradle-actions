<!-- markdownlint-disable -->

# Hardening Report: gradle--actions/v3.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **gradle--actions/v3.4.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite actions under .github/actions/ reference external actions by mutable version tags instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the upstream tag is moved.

.github/actions/build-dist/action.yml:
  - uses: actions/setup-node@v4  (tag, not SHA)
  - uses: actions/upload-artifact@v4  (tag, not SHA)

.github/actions/init-integ-test/action.yml:
  - uses: actions/setup-java@v4  (tag, not SHA)
  - uses: actions/download-artifact@v4  (tag, not SHA)

Each should be pinned to a full SHA, e.g. actions/setup-node@11bd71901bbe5b1630ceea73d27597364c9af683 # v4

Locations:

- `.github/actions/build-dist/action.yml:6`
- `.github/actions/build-dist/action.yml:20`
- `.github/actions/init-integ-test/action.yml:6`
- `.github/actions/init-integ-test/action.yml:13`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all four mutable tag references to full commit SHAs:
- .github/actions/build-dist/action.yml: actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020 # v4
- .github/actions/build-dist/action.yml: actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02 # v4
- .github/actions/init-integ-test/action.yml: actions/setup-java@v4 → @cf277c60eb25467037889841efdb72551f06f6c3 # v4
- .github/actions/init-integ-test/action.yml: actions/download-artifact@v4 → @d3f86a106a0bac45b974a628896c90dbdf5c8093 # v4
All version tags preserved as inline comments for readability.

