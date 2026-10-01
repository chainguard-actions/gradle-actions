<!-- markdownlint-disable -->

# Hardening Report: gradle--actions/v3.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **gradle--actions/v3.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action '.github/actions/build-dist/action.yml' references 'actions/setup-node@v4' (line 6) and 'actions/upload-artifact@v4' (line 23) using mutable version tags instead of full 40-character SHA commit digests. These references can be silently updated by the upstream repository, enabling supply-chain attacks.

Locations:

- `.github/actions/build-dist/action.yml:6`
- `.github/actions/build-dist/action.yml:23`

### unpinned-uses (severity: high)

The composite action '.github/actions/init-integ-test/action.yml' references 'actions/setup-java@v4' (line 7) and 'actions/download-artifact@v4' (line 15) using mutable version tags instead of full 40-character SHA commit digests. These references can be silently updated by the upstream repository, enabling supply-chain attacks.

Locations:

- `.github/actions/init-integ-test/action.yml:7`
- `.github/actions/init-integ-test/action.yml:15`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all four unpinned action references to full SHA digests:
- .github/actions/build-dist/action.yml: actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020 # v4; actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02 # v4
- .github/actions/init-integ-test/action.yml: actions/setup-java@v4 → @cf277c60eb25467037889841efdb72551f06f6c3 # v4; actions/download-artifact@v4 → @d3f86a106a0bac45b974a628896c90dbdf5c8093 # v4
All original version tags preserved as inline comments for readability.

