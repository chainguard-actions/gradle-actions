<!-- markdownlint-disable -->

# Hardening Report: gradle--actions/v3.4.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **gradle--actions/v3.4.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Composite action steps in .github/actions/ use mutable tag-based references (@v4) instead of pinned full 40-character SHA commit hashes. This exposes the action to supply-chain attacks if the referenced action tags are moved or compromised. Failing references: actions/setup-node@v4, actions/upload-artifact@v4 (build-dist/action.yml); actions/setup-java@v4, actions/download-artifact@v4 (init-integ-test/action.yml).

Locations:

- `.github/actions/build-dist/action.yml:6`
- `.github/actions/build-dist/action.yml:20`
- `.github/actions/init-integ-test/action.yml:7`
- `.github/actions/init-integ-test/action.yml:14`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all four mutable tag-based action references to full commit SHAs:
- .github/actions/build-dist/action.yml: actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020, actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02
- .github/actions/init-integ-test/action.yml: actions/setup-java@v4 → @cf277c60eb25467037889841efdb72551f06f6c3, actions/download-artifact@v4 → @d3f86a106a0bac45b974a628896c90dbdf5c8093
Original tag preserved as inline comment (# v4) for readability.

