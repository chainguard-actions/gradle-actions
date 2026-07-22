<!-- markdownlint-disable -->

# Hardening Report: gradle--actions/v6.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **gradle--actions/v6.1.1** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): `${{ steps.gradle-assemble.outputs.dependency-graph-file }}`, `${{ steps.gradle-build.outputs.dependency-graph-file }}`, and `${{ steps.gradle-build-again.outputs.dependency-graph-file }}` are interpolated directly inside `run:` shell blocks (used in `echo`, `if [ ! -e ... ]`, and `rm` commands). These are `steps.*.outputs.*` values — workflow-controllable — and flow through YAML template substitution before the shell parses them, enabling script injection. Additionally, `${{ steps.config-cache-store.outputs.dependency-graph-file }}` is used directly in `if` and `rm` commands in the same file.

Locations:

- `.github/workflows/integ-test-dependency-graph.yml:93`
- `.github/workflows/integ-test-dependency-graph.yml:107`

### script-injection (severity: high)

Rule (a): `${{ steps.kotlin-dsl.outputs.dependency-graph-file }}`, `${{ steps.groovy-dsl.outputs.dependency-graph-file }}`, `${{ steps.groovy-dsl-again.outputs.dependency-graph-file }}`, `${{ steps.dependency-submission.outputs.dependency-graph-file }}`, `${{ steps.dependency-graph.outputs.dependency-graph-file }}`, and `${{ github.workspace }}` are interpolated directly inside `run:` shell blocks (used in `echo`, `if [ ! -e ... ]`, `rm`, and `ls -A` commands). These are workflow-controllable values that flow through YAML template substitution before the shell parses them, enabling script injection.

Locations:

- `.github/workflows/integ-test-dependency-submission.yml:130`
- `.github/workflows/integ-test-dependency-submission.yml:196`
- `.github/workflows/integ-test-dependency-submission.yml:280`
- `.github/workflows/integ-test-dependency-submission.yml:360`
- `.github/workflows/integ-test-dependency-submission.yml:395`

### script-injection (severity: high)

Rule (a): `${{ steps.setup-gradle.outcome}}` is interpolated directly inside a `run:` shell block: `if [ "${{ steps.setup-gradle.outcome}}" != "failure" ]`. Even though `outcome` is a GitHub-controlled value, any `${{ ... }}` directly inside a `run:` script is a script-injection risk because the value is substituted by the YAML template engine before the shell parses it.

Locations:

- `.github/workflows/integ-test-wrapper-validation.yml:33`

### script-injection (severity: high)

Rule (b): `ALL_CHANGED_FILES` is set from `${{ steps.changed-files.outputs.all_changed_files }}` (a step output — workflow-controllable) and then expanded **unquoted** in a `for` loop: `for file in ${ALL_CHANGED_FILES}; do`. An unquoted shell expansion allows the shell to parse metacharacters (glob chars, word-splitting) out of the value. The variable must be double-quoted: `for file in "${ALL_CHANGED_FILES}"; do`.

Locations:

- `.github/workflows/ci-check-no-dist-update.yml:27`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 4 script-injection findings across 4 workflow files:

1. `.github/workflows/integ-test-dependency-graph.yml`: Moved `${{ steps.gradle-assemble.outputs.dependency-graph-file }}`, `${{ steps.gradle-build.outputs.dependency-graph-file }}`, `${{ steps.gradle-build-again.outputs.dependency-graph-file }}`, `${{ steps.config-cache-store.outputs.dependency-graph-file }}`, and `${{ github.workspace }}` out of `run:` blocks into `env:` blocks, referencing them as plain environment variables (`$ASSEMBLE_GRAPH_FILE`, `$BUILD_GRAPH_FILE`, `$BUILD_AGAIN_GRAPH_FILE`, `$CONFIG_CACHE_GRAPH_FILE`, `$WORKSPACE`).

2. `.github/workflows/integ-test-dependency-submission.yml`: Applied the same env-block pattern to all 5 affected steps: moved `${{ steps.kotlin-dsl.outputs.dependency-graph-file }}`, `${{ steps.groovy-dsl.outputs.dependency-graph-file }}`, `${{ steps.groovy-dsl-again.outputs.dependency-graph-file }}`, `${{ steps.config-cache-store.outputs.dependency-graph-file }}`, `${{ steps.dependency-submission.outputs.dependency-graph-file }}`, `${{ steps.dependency-graph.outputs.dependency-graph-file }}`, and `${{ github.workspace }}` into `env:` blocks.

3. `.github/workflows/integ-test-wrapper-validation.yml`: Moved `${{ steps.setup-gradle.outcome }}` into an `env:` block as `SETUP_GRADLE_OUTCOME` and referenced it as `$SETUP_GRADLE_OUTCOME` in the shell script.

4. `.github/workflows/ci-check-no-dist-update.yml`: Added double quotes around `${ALL_CHANGED_FILES}` in the `for` loop (`for file in "${ALL_CHANGED_FILES}"`) to prevent word-splitting and glob expansion of metacharacters from the workflow-controllable value.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/integ-test-provision-gradle-versions.yml at line 132. Moved ${{matrix.gradle}} out of the run: shell command into an env: block as GRADLE_VERSION_CHECK, then referenced it as $GRADLE_VERSION_CHECK in the shell script. This prevents the matrix value from being interpolated directly into the shell command string before the shell sees it.

