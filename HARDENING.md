<!-- markdownlint-disable -->

# Hardening Report: gradle--actions/v6.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **gradle--actions/v6.0.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): ${{matrix.gradle}} is interpolated directly inside a run: shell command. The matrix value flows through YAML template substitution before the shell sees it, enabling command injection. Offending line: `run: gradle help "-DgradleVersionCheck=${{matrix.gradle}}"`

Locations:

- `.github/workflows/integ-test-provision-gradle-versions.yml:130`

### script-injection (severity: high)

Rule (a): Multiple ${{ steps.*.outputs.dependency-graph-file }} and ${{ github.workspace }} expressions are interpolated directly inside run: shell commands. These flow through YAML template substitution before the shell sees them. Examples include: `echo "gradle-assemble report file: ${{ steps.gradle-assemble.outputs.dependency-graph-file }}"`, `if [ ! -e "${{ steps.config-cache-store.outputs.dependency-graph-file }}" ]`, `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}`, and `ls -A "${{ github.workspace }}/downloaded-dependency-graphs"`.

Locations:

- `.github/workflows/integ-test-dependency-graph.yml:95`
- `.github/workflows/integ-test-dependency-graph.yml:130`
- `.github/workflows/integ-test-dependency-graph.yml:175`
- `.github/workflows/integ-test-dependency-graph.yml:185`

### script-injection (severity: high)

Rule (a): Multiple ${{ steps.*.outputs.dependency-graph-file }} and ${{ github.workspace }} expressions are interpolated directly inside run: shell commands. Examples include: `echo "kotlin-dsl report file: ${{ steps.kotlin-dsl.outputs.dependency-graph-file }}"`, `if [ ! -e "${{ steps.dependency-submission.outputs.dependency-graph-file }}" ]`, `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}*`, `ls -A "${{ github.workspace }}/custom/report-dir"`, and `echo "report file: ${{ steps.dependency-graph.outputs.dependency-graph-file }}"`.

Locations:

- `.github/workflows/integ-test-dependency-submission.yml:120`
- `.github/workflows/integ-test-dependency-submission.yml:175`
- `.github/workflows/integ-test-dependency-submission.yml:245`
- `.github/workflows/integ-test-dependency-submission.yml:330`
- `.github/workflows/integ-test-dependency-submission.yml:380`

### script-injection (severity: high)

Rule (a): ${{ steps.setup-gradle.outcome }} is interpolated directly inside a run: shell command. Offending line: `if [ "${{ steps.setup-gradle.outcome}}" != "failure" ] ; then`. The expression flows through YAML template substitution before the shell sees it.

Locations:

- `.github/workflows/integ-test-wrapper-validation.yml:37`

### script-injection (severity: high)

Rule (b): The shell variable ${ALL_CHANGED_FILES} is expanded unquoted inside a for loop: `for file in ${ALL_CHANGED_FILES}; do`. ALL_CHANGED_FILES is set from ${{ steps.changed-files.outputs.all_changed_files }} (a third-party action output that can contain attacker-controlled filenames from a pull request). An unquoted expansion allows the shell to parse metacharacters, glob patterns, and word-split the value, enabling command injection or unexpected behavior.

Locations:

- `.github/workflows/ci-check-no-dist-update.yml:27`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 5 script injection findings across 5 workflow files:

1. integ-test-provision-gradle-versions.yml: Moved ${{matrix.gradle}} to env block as GRADLE_VERSION_CHECK.

2. integ-test-dependency-graph.yml: Moved ${{ steps.gradle-assemble.outputs.dependency-graph-file }}, ${{ steps.gradle-build.outputs.dependency-graph-file }}, ${{ steps.gradle-build-again.outputs.dependency-graph-file }}, ${{ steps.config-cache-store.outputs.dependency-graph-file }}, and ${{ github.workspace }} expressions to env blocks. Also fixed unquoted 'rm' command to use quoted variable.

3. integ-test-dependency-submission.yml: Moved all step output and github.workspace expressions to env blocks across 6 steps (including one additional instance in the 'Check generated dependency graph and env vars' step not in the original findings). Fixed unquoted 'rm' commands to use quoted variables.

4. integ-test-wrapper-validation.yml: Moved ${{ steps.setup-gradle.outcome}} to env block as SETUP_GRADLE_OUTCOME.

5. ci-check-no-dist-update.yml: Replaced unquoted `for file in ${ALL_CHANGED_FILES}` with safe `while IFS= read -r file; done <<< "$ALL_CHANGED_FILES"` pattern to prevent word splitting and glob expansion on attacker-controlled filenames.

