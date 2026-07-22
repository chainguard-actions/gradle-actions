<!-- markdownlint-disable -->

# Hardening Report: gradle--actions/v6.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **gradle--actions/v6.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): The step sets env var ALL_CHANGED_FILES from the workflow-controllable value `${{ steps.changed-files.outputs.all_changed_files }}`, then expands it unquoted inside a for loop: `for file in ${ALL_CHANGED_FILES}; do`. An attacker who controls a file path containing shell metacharacters (spaces, globs, semicolons, etc.) could cause word-splitting or glob expansion. The variable must be double-quoted: `for file in "${ALL_CHANGED_FILES}"; do`.

Locations:

- `.github/workflows/ci-check-no-dist-update.yml:33`

### script-injection (severity: high)

Rule (a): The run: block directly interpolates `${{matrix.gradle}}` (a matrix context value) into a shell command: `run: gradle help "-DgradleVersionCheck=${{matrix.gradle}}". Any ${{ ... }} expression is substituted by the YAML template engine before the shell sees the string, bypassing shell quoting. This should be moved to an env: variable and referenced as a quoted shell variable instead.

Locations:

- `.github/workflows/integ-test-provision-gradle-versions.yml:116`

### script-injection (severity: high)

Rule (a): Multiple run: blocks in this file directly interpolate ${{ steps.*.outputs.dependency-graph-file }} and ${{ github.workspace }} expressions into shell commands (echo, if [ ! -e "...", rm, ls -A). For example: `echo "gradle-assemble report file: ${{ steps.gradle-assemble.outputs.dependency-graph-file }}"`, `if [ ! -e "${{ steps.gradle-assemble.outputs.dependency-graph-file }}" ]`, `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}`, and `ls -A "${{ github.workspace }}/downloaded-dependency-graphs"`. All ${{ ... }} expressions are substituted before the shell parses the command. These values should be passed via env: variables and referenced as quoted shell variables.

Locations:

- `.github/workflows/integ-test-dependency-graph.yml:80`
- `.github/workflows/integ-test-dependency-graph.yml:81`
- `.github/workflows/integ-test-dependency-graph.yml:82`
- `.github/workflows/integ-test-dependency-graph.yml:122`
- `.github/workflows/integ-test-dependency-graph.yml:168`
- `.github/workflows/integ-test-dependency-graph.yml:172`

### script-injection (severity: high)

Rule (a): Multiple run: blocks in this file directly interpolate ${{ steps.*.outputs.dependency-graph-file }} and ${{ github.workspace }} expressions into shell commands (echo, if [ ! -e "...", rm, ls -A). For example: `echo "kotlin-dsl report file: ${{ steps.kotlin-dsl.outputs.dependency-graph-file }}"`, `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}*`, `rm ${{ steps.dependency-submission.outputs.dependency-graph-file }}*`, and `ls -A "${{ github.workspace }}/custom/report-dir"`. All ${{ ... }} expressions are substituted before the shell parses the command. These values should be passed via env: variables and referenced as quoted shell variables.

Locations:

- `.github/workflows/integ-test-dependency-submission.yml:115`
- `.github/workflows/integ-test-dependency-submission.yml:116`
- `.github/workflows/integ-test-dependency-submission.yml:117`
- `.github/workflows/integ-test-dependency-submission.yml:175`
- `.github/workflows/integ-test-dependency-submission.yml:178`
- `.github/workflows/integ-test-dependency-submission.yml:247`
- `.github/workflows/integ-test-dependency-submission.yml:250`
- `.github/workflows/integ-test-dependency-submission.yml:330`
- `.github/workflows/integ-test-dependency-submission.yml:334`
- `.github/workflows/integ-test-dependency-submission.yml:337`
- `.github/workflows/integ-test-dependency-submission.yml:371`
- `.github/workflows/integ-test-dependency-submission.yml:374`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 4 script injection findings across 4 workflow files:

1. ci-check-no-dist-update.yml (line 33): Double-quoted `${ALL_CHANGED_FILES}` in the for loop to prevent word-splitting/glob expansion.

2. integ-test-provision-gradle-versions.yml (line 116): Moved `${{matrix.gradle}}` from the run: shell command to an env: block as GRADLE_VERSION, referenced as `$GRADLE_VERSION` in the script.

3. integ-test-dependency-graph.yml (lines 80-82, 122, 168, 172): Moved all `${{ steps.*.outputs.dependency-graph-file }}` and `${{ github.workspace }}` expressions from run: blocks to env: blocks, referencing them as quoted shell variables. Also added quotes around `rm` arguments.

4. integ-test-dependency-submission.yml (lines 115-117, 175, 178, 247, 250, 330, 334, 337, 371, 374): Moved all `${{ steps.*.outputs.dependency-graph-file }}` and `${{ github.workspace }}` expressions from run: blocks to env: blocks, referencing them as quoted shell variables. Also added quotes around `rm` arguments.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in `.github/workflows/integ-test-wrapper-validation.yml` at the 'Check failure' step in the `wrapper-validation-setup-gradle` job. Moved `${{ steps.setup-gradle.outcome }}` out of the `run:` shell string into an `env:` block as `SETUP_GRADLE_OUTCOME`, and updated the shell script to reference `$SETUP_GRADLE_OUTCOME` instead. This follows the same pattern already used in other steps in the same file.

