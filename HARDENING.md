<!-- markdownlint-disable -->

# Hardening Report: gradle--actions/v6.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **gradle--actions/v6.0.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b): The env var `ALL_CHANGED_FILES` is populated from `${{ steps.changed-files.outputs.all_changed_files }}` (a workflow-controllable `steps.*` output) and then expanded unquoted as `${ALL_CHANGED_FILES}` inside a `for` loop in the `run:` block. An attacker-controlled filename containing shell metacharacters (spaces, globs, semicolons, etc.) could break out of the loop and execute arbitrary commands. The fix is to double-quote the expansion: `for file in "${ALL_CHANGED_FILES}"`.

Locations:

- `.github/workflows/ci-check-no-dist-update.yml:30`

### script-injection (severity: high)

Sub-rule (a): The expression `${{matrix.gradle}}` is interpolated directly inside a `run:` shell command string: `run: gradle help "-DgradleVersionCheck=${{matrix.gradle}}"`. The `matrix.*` context is workflow-controllable and is substituted into the shell command before the shell parses it, enabling script injection. The value should be passed via an `env:` variable and double-quoted in the shell.

Locations:

- `.github/workflows/integ-test-provision-gradle-versions.yml:130`

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks directly interpolate `${{ steps.*.outputs.dependency-graph-file }}` expressions (e.g., `echo "gradle-assemble report file: ${{ steps.gradle-assemble.outputs.dependency-graph-file }}"`, `if [ ! -e "${{ steps.config-cache-store.outputs.dependency-graph-file }}" ]`, `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}`). The `steps.*` context is workflow-controllable and is substituted into the shell before parsing, enabling script injection via a crafted output value. These values should be passed through `env:` variables and double-quoted.

Locations:

- `.github/workflows/integ-test-dependency-graph.yml:88`
- `.github/workflows/integ-test-dependency-graph.yml:120`

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks directly interpolate `${{ steps.*.outputs.dependency-graph-file }}` and `${{ github.workspace }}` expressions (e.g., `echo "kotlin-dsl report file: ${{ steps.kotlin-dsl.outputs.dependency-graph-file }}"`, `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}*`, `ls -A "${{ github.workspace }}/custom/report-dir"`). The `steps.*` and `github.*` contexts are workflow-controllable and are substituted into the shell before parsing, enabling script injection. These values should be passed through `env:` variables and double-quoted.

Locations:

- `.github/workflows/integ-test-dependency-submission.yml:120`
- `.github/workflows/integ-test-dependency-submission.yml:185`
- `.github/workflows/integ-test-dependency-submission.yml:265`
- `.github/workflows/integ-test-dependency-submission.yml:310`
- `.github/workflows/integ-test-dependency-submission.yml:355`
- `.github/workflows/integ-test-dependency-submission.yml:415`

### script-injection (severity: high)

Sub-rule (a): The expression `${{ steps.setup-gradle.outcome }}` is interpolated directly inside a `run:` shell command string: `if [ "${{ steps.setup-gradle.outcome}}" != "failure" ]`. The `steps.*` context is workflow-controllable and is substituted into the shell before parsing, enabling script injection. The value should be passed via an `env:` variable and double-quoted.

Locations:

- `.github/workflows/integ-test-wrapper-validation.yml:37`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 5 script injection findings across 5 workflow files:

1. ci-check-no-dist-update.yml: Double-quoted `${ALL_CHANGED_FILES}` in the for loop to prevent word splitting on attacker-controlled filenames.

2. integ-test-provision-gradle-versions.yml: Moved `${{matrix.gradle}}` to an `env:` block as `GRADLE_VERSION` and referenced it safely as `$GRADLE_VERSION` in the shell command.

3. integ-test-dependency-graph.yml: Moved all `${{ steps.*.outputs.dependency-graph-file }}` and `${{ github.workspace }}` expressions out of run: blocks into env: blocks, referencing them as properly-quoted shell variables.

4. integ-test-dependency-submission.yml: Fixed 6 locations - moved all `${{ steps.*.outputs.dependency-graph-file }}` and `${{ github.workspace }}` expressions to env: blocks across the dependency-submission-multiple-builds, dependency-submission-config-cache, dependency-submission-with-setup-gradle, dependency-submission-with-includes-and-excludes, dependency-submission-custom-report-dir-submit, and dependency-submission-custom-report-dir-download-and-submit jobs.

5. integ-test-wrapper-validation.yml: Moved `${{ steps.setup-gradle.outcome }}` to an env: block as `SETUP_GRADLE_OUTCOME` and referenced it safely in the shell script.

