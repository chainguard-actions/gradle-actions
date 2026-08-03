<!-- markdownlint-disable -->

# Hardening Report: gradle--actions/v6.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **gradle--actions/v6.3.0** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): `${{ needs.*.result }}` expressions are interpolated directly inside a `run:` shell script. Lines like `echo "  build-distribution: ${{ needs.build-distribution.result }}"` inject the expression value through YAML template substitution before the shell ever sees it. Although `needs.*.result` values are GitHub-controlled, any `${{ ... }}` directly inside a `run:` block is a script-injection finding.

Locations:

- `.github/workflows/ci-integ-test.yml:75`

### script-injection (severity: high)

Sub-rule (b): The env var `ALL_CHANGED_FILES` (sourced from `${{ steps.changed-files.outputs.all_changed_files }}`) is expanded unquoted in a `for` loop: `for file in ${ALL_CHANGED_FILES}; do`. An unquoted expansion allows the shell to parse metacharacters (glob chars, whitespace splitting, etc.) out of the value. The variable must be double-quoted: `for file in "${ALL_CHANGED_FILES}"; do`.

Locations:

- `.github/workflows/ci-check-no-dist-update.yml:32`

### script-injection (severity: high)

Sub-rule (a): `${{ steps.*.outputs.dependency-graph-file }}` expressions are interpolated directly inside `run:` shell scripts in multiple steps. Examples include `if [ ! -e "${{ steps.gradle-assemble.outputs.dependency-graph-file }}" ]` and `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}`. Additionally, `${{ github.workspace }}` is used directly in a `run:` block: `ls -A "${{ github.workspace }}/downloaded-dependency-graphs"`. All `${{ ... }}` inside `run:` blocks are script-injection risks.

Locations:

- `.github/workflows/integ-test-dependency-graph.yml:97`
- `.github/workflows/integ-test-dependency-graph.yml:130`
- `.github/workflows/integ-test-dependency-graph.yml:185`

### script-injection (severity: high)

Sub-rule (a): `${{ steps.*.outputs.dependency-graph-file }}` and `${{ github.workspace }}` expressions are interpolated directly inside `run:` shell scripts in multiple steps. Examples include `if [ ! -e "${{ steps.kotlin-dsl.outputs.dependency-graph-file }}" ]`, `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}*`, `echo "report file: ${{ steps.dependency-graph.outputs.dependency-graph-file }}"`, and `ls -A "${{ github.workspace }}/custom/report-dir"`. All `${{ ... }}` inside `run:` blocks are script-injection risks.

Locations:

- `.github/workflows/integ-test-dependency-submission.yml:140`
- `.github/workflows/integ-test-dependency-submission.yml:196`
- `.github/workflows/integ-test-dependency-submission.yml:280`
- `.github/workflows/integ-test-dependency-submission.yml:355`
- `.github/workflows/integ-test-dependency-submission.yml:415`
- `.github/workflows/integ-test-dependency-submission.yml:490`

### script-injection (severity: high)

Sub-rule (a): `${{ steps.setup-gradle.outcome}}` is interpolated directly inside a `run:` shell script: `if [ "${{ steps.setup-gradle.outcome}}" != "failure" ] ; then`. The expression value is substituted by the YAML template engine before the shell parses the command, making it a script-injection risk.

Locations:

- `.github/workflows/integ-test-wrapper-validation.yml:36`

### script-injection (severity: high)

Sub-rule (a): `${{matrix.gradle}}` is interpolated directly inside a `run:` shell command: `run: gradle help "-DgradleVersionCheck=${{matrix.gradle}}"`. Matrix values flow through YAML template substitution before the shell sees them, making this a script-injection risk.

Locations:

- `.github/workflows/integ-test-provision-gradle-versions.yml:113`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 6 script-injection findings across 6 workflow files:

1. **ci-integ-test.yml** (line 75): Moved `${{ needs.build-distribution.result }}`, `${{ needs.smoke-tests.result }}`, `${{ needs.caching-integ-tests.result }}`, `${{ needs.other-integ-tests.result }}`, and `${{ needs.dependency-submission-integ-tests.result }}` into env vars (`RESULT_BUILD_DISTRIBUTION`, `RESULT_SMOKE_TESTS`, etc.) and referenced them as plain shell variables.

2. **ci-check-no-dist-update.yml** (line 32): Fixed unquoted `${ALL_CHANGED_FILES}` in `for` loop to `"${ALL_CHANGED_FILES}"` to prevent shell metacharacter interpretation.

3. **integ-test-dependency-graph.yml** (lines 97, 130, 185): Moved `${{ steps.gradle-assemble.outputs.dependency-graph-file }}`, `${{ steps.gradle-build.outputs.dependency-graph-file }}`, `${{ steps.gradle-build-again.outputs.dependency-graph-file }}`, `${{ steps.config-cache-store.outputs.dependency-graph-file }}`, and `${{ github.workspace }}` into env vars and referenced them as plain shell variables. Also properly quoted the `rm` argument.

4. **integ-test-dependency-submission.yml** (lines 140, 196, 280, 355, 415, 490): Moved all `${{ steps.*.outputs.dependency-graph-file }}` and `${{ github.workspace }}` expressions into env vars across 6 steps and referenced them as plain shell variables. Also properly quoted `rm` arguments.

5. **integ-test-wrapper-validation.yml** (line 36): Moved `${{ steps.setup-gradle.outcome }}` into env var `SETUP_GRADLE_OUTCOME` and referenced it as a plain shell variable.

6. **integ-test-provision-gradle-versions.yml** (line 113): Moved `${{matrix.gradle}}` into env var `MATRIX_GRADLE` and referenced it as a plain shell variable in the `gradle help` command.

