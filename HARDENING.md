<!-- markdownlint-disable -->

# Hardening Report: gradle--actions/v6.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **gradle--actions/v6.2.0** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): ${{ needs.build-distribution.result }}, ${{ needs.caching-integ-tests.result }}, ${{ needs.other-integ-tests.result }}, and ${{ needs.dependency-submission-integ-tests.result }} are directly interpolated inside a run: shell command. Any ${{ ... }} expression in a run: block is a script-injection risk regardless of the context, as the value is substituted into the shell command string before the shell parses it.

Locations:

- `.github/workflows/ci-integ-test.yml:59`

### script-injection (severity: high)

Rule (b): The env var ALL_CHANGED_FILES (set from ${{ steps.changed-files.outputs.all_changed_files }}, a steps.*.outputs.* value) is expanded unquoted in a for loop: `for file in ${ALL_CHANGED_FILES}; do`. An unquoted shell expansion allows word-splitting and glob expansion on attacker-controlled content (e.g. filenames with spaces, semicolons, or shell metacharacters), enabling command injection.

Locations:

- `.github/workflows/ci-check-no-dist-update.yml:32`

### script-injection (severity: high)

Rule (a): ${{matrix.gradle}} is directly interpolated inside a run: shell command: `run: gradle help "-DgradleVersionCheck=${{matrix.gradle}}"`. The matrix value is substituted into the shell command string before the shell parses it, allowing injection of shell metacharacters.

Locations:

- `.github/workflows/integ-test-provision-gradle-versions.yml:109`

### script-injection (severity: high)

Rule (a): Multiple ${{ steps.*.outputs.dependency-graph-file }} and ${{ github.workspace }} expressions are directly interpolated inside run: shell commands. Examples include: `echo "gradle-assemble report file: ${{ steps.gradle-assemble.outputs.dependency-graph-file }}"`, `if [ ! -e "${{ steps.gradle-assemble.outputs.dependency-graph-file }}" ]`, `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}` (unquoted rm), and `ls -A "${{ github.workspace }}/downloaded-dependency-graphs"`. These are substituted into the shell before parsing, enabling injection.

Locations:

- `.github/workflows/integ-test-dependency-graph.yml:91`
- `.github/workflows/integ-test-dependency-graph.yml:131`
- `.github/workflows/integ-test-dependency-graph.yml:183`

### script-injection (severity: high)

Rule (a): Multiple ${{ steps.*.outputs.dependency-graph-file }}, ${{ steps.dependency-submission.outputs.dependency-graph-file }}, and ${{ github.workspace }} expressions are directly interpolated inside run: shell commands. Examples include: `echo "kotlin-dsl report file: ${{ steps.kotlin-dsl.outputs.dependency-graph-file }}"`, `if [ ! -e "${{ steps.kotlin-dsl.outputs.dependency-graph-file }}" ]`, `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}*` (unquoted rm), `rm ${{ steps.dependency-submission.outputs.dependency-graph-file }}*`, and `ls -A "${{ github.workspace }}/custom/report-dir"`. These are substituted into the shell before parsing.

Locations:

- `.github/workflows/integ-test-dependency-submission.yml:120`
- `.github/workflows/integ-test-dependency-submission.yml:185`
- `.github/workflows/integ-test-dependency-submission.yml:260`
- `.github/workflows/integ-test-dependency-submission.yml:390`

### script-injection (severity: high)

Rule (a): ${{ steps.setup-gradle.outcome }} is directly interpolated inside a run: shell command: `if [ "${{ steps.setup-gradle.outcome}}" != "failure" ]`. The expression is substituted into the shell command string before the shell parses it.

Locations:

- `.github/workflows/integ-test-wrapper-validation.yml:35`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 6 script-injection findings across 6 workflow files:

1. ci-integ-test.yml (line 59): Moved ${{ needs.build-distribution.result }}, ${{ needs.caching-integ-tests.result }}, ${{ needs.other-integ-tests.result }}, and ${{ needs.dependency-submission-integ-tests.result }} into env: block variables (BUILD_DISTRIBUTION_RESULT, CACHING_INTEG_TESTS_RESULT, OTHER_INTEG_TESTS_RESULT, DEPENDENCY_SUBMISSION_INTEG_TESTS_RESULT) and referenced them as plain env vars in the run: block.

2. ci-check-no-dist-update.yml (line 32): Fixed unquoted expansion of ALL_CHANGED_FILES in for loop by using `IFS=' ' read -ra FILES <<< "$ALL_CHANGED_FILES"` and iterating over `"${FILES[@]}"` to prevent word-splitting and glob injection.

3. integ-test-provision-gradle-versions.yml (line 109): Moved ${{matrix.gradle}} into env: block as GRADLE_VERSION and referenced it as $GRADLE_VERSION in the gradle help command.

4. integ-test-dependency-graph.yml (lines 91, 131, 183): Moved all ${{ steps.*.outputs.dependency-graph-file }} and ${{ github.workspace }} expressions into env: blocks (GRADLE_ASSEMBLE_GRAPH_FILE, GRADLE_BUILD_GRAPH_FILE, GRADLE_BUILD_AGAIN_GRAPH_FILE, CONFIG_CACHE_STORE_GRAPH_FILE, GITHUB_WORKSPACE_PATH) and referenced them as plain env vars. Also fixed unquoted `rm` to use quoted `rm "$VAR"`.

5. integ-test-dependency-submission.yml (lines 120, 185, 260, 390): Moved all ${{ steps.*.outputs.dependency-graph-file }}, ${{ steps.dependency-submission.outputs.dependency-graph-file }}, and ${{ github.workspace }} expressions into env: blocks and referenced them as plain env vars. Fixed unquoted `rm` calls to use quoted form.

6. integ-test-wrapper-validation.yml (line 35): Moved ${{ steps.setup-gradle.outcome }} into env: block as SETUP_GRADLE_OUTCOME and referenced it as $SETUP_GRADLE_OUTCOME in the if condition.

