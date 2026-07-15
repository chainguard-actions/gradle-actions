<!-- markdownlint-disable -->

# Hardening Report: gradle--actions/v6.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **gradle--actions/v6.0.0** was hardened automatically. 7 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): The step 'Print changes to dist directory' sets env var ALL_CHANGED_FILES from ${{ steps.changed-files.outputs.all_changed_files }} (a step output that can contain attacker-controlled filenames from pull requests) and then uses it unquoted in a for loop: `for file in ${ALL_CHANGED_FILES}`. The unquoted expansion allows shell metacharacter injection (word splitting, glob expansion). The value should be double-quoted: `for file in "${ALL_CHANGED_FILES}"` or iterated safely.

Locations:

- `.github/workflows/ci-check-no-dist-update.yml:33`

### script-injection (severity: high)

Rule (a): The step 'Check generated dependency graphs' (dependency-graph-multiple-builds job) directly interpolates ${{ steps.gradle-assemble.outputs.dependency-graph-file }}, ${{ steps.gradle-build.outputs.dependency-graph-file }}, and ${{ steps.gradle-build-again.outputs.dependency-graph-file }} inside a run: shell block in echo and if [ ! -e "..." ] commands. Any ${{ ... }} expression in a run: block is a script-injection risk as the value is substituted before the shell parses the command.

Locations:

- `.github/workflows/integ-test-dependency-graph.yml:103`

### script-injection (severity: high)

Rule (a): The step 'Check and delete generated dependency graph' (dependency-graph-config-cache job) directly interpolates ${{ steps.config-cache-store.outputs.dependency-graph-file }} inside a run: shell block in both an if condition and an unquoted rm command: `rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}`. The unquoted rm is especially dangerous as it allows shell metacharacter injection.

Locations:

- `.github/workflows/integ-test-dependency-graph.yml:131`

### script-injection (severity: high)

Rule (a): The step 'Check for downloaded dependency graphs' (dependency-graph-generate-submit-and-upload-check job) directly interpolates ${{ github.workspace }} inside a run: shell block in ls and if commands: `ls -A "${{ github.workspace }}/downloaded-dependency-graphs"`. Any ${{ ... }} expression in a run: block is a script-injection risk.

Locations:

- `.github/workflows/integ-test-dependency-graph.yml:156`

### script-injection (severity: high)

Rule (a): Multiple steps in integ-test-dependency-submission.yml directly interpolate ${{ steps.*.outputs.dependency-graph-file }} and ${{ github.workspace }} inside run: shell blocks. Affected steps include: 'Check generated dependency graphs' (dependency-submission-multiple-builds job, echo/if with steps outputs), 'Check and delete generated dependency graph' (dependency-submission-config-cache job, if and unquoted rm ${{ steps.config-cache-store.outputs.dependency-graph-file }}*), 'Check and delete generated dependency graph' (dependency-submission-with-setup-gradle job, rm ${{ steps.dependency-submission.outputs.dependency-graph-file }}*), 'Check generated dependency graph and env vars' (if with steps output and ls -A "${{ github.workspace }}/..."), 'Check generated dependency graphs' (custom-report-dir-submit job, echo/if/ls with steps and github.workspace), and 'Check downloaded dependency graph' (ls -A "${{ github.workspace }}/custom/report-dir").

Locations:

- `.github/workflows/integ-test-dependency-submission.yml:116`
- `.github/workflows/integ-test-dependency-submission.yml:168`
- `.github/workflows/integ-test-dependency-submission.yml:240`
- `.github/workflows/integ-test-dependency-submission.yml:275`
- `.github/workflows/integ-test-dependency-submission.yml:318`
- `.github/workflows/integ-test-dependency-submission.yml:362`

### script-injection (severity: high)

Rule (a): The step 'Run Gradle build' (provision-gradle-version job) directly interpolates ${{matrix.gradle}} inside a run: shell command: `run: gradle help "-DgradleVersionCheck=${{matrix.gradle}}"`. Any ${{ ... }} expression in a run: block is a script-injection risk as the value is substituted before the shell parses the command.

Locations:

- `.github/workflows/integ-test-provision-gradle-versions.yml:130`

### script-injection (severity: high)

Rule (a): The step 'Check failure' (wrapper-validation-setup-gradle job) directly interpolates ${{ steps.setup-gradle.outcome}} inside a run: shell block: `if [ "${{ steps.setup-gradle.outcome}}" != "failure" ]`. Any ${{ ... }} expression in a run: block is a script-injection risk.

Locations:

- `.github/workflows/integ-test-wrapper-validation.yml:42`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 7 script-injection findings across 5 workflow files:

1. **ci-check-no-dist-update.yml** (line 33): Quoted `${ALL_CHANGED_FILES}` → `"${ALL_CHANGED_FILES}"` in the for loop to prevent word splitting and glob expansion.

2. **integ-test-dependency-graph.yml** (line 103): Moved `${{ steps.gradle-assemble.outputs.dependency-graph-file }}`, `${{ steps.gradle-build.outputs.dependency-graph-file }}`, and `${{ steps.gradle-build-again.outputs.dependency-graph-file }}` into `env:` block as `GRADLE_ASSEMBLE_GRAPH_FILE`, `GRADLE_BUILD_GRAPH_FILE`, `GRADLE_BUILD_AGAIN_GRAPH_FILE`.

3. **integ-test-dependency-graph.yml** (line 131): Moved `${{ steps.config-cache-store.outputs.dependency-graph-file }}` into `env:` block as `CONFIG_CACHE_GRAPH_FILE`; also properly quoted the `rm` command.

4. **integ-test-dependency-graph.yml** (line 156): Moved `${{ github.workspace }}` into `env:` block as `GITHUB_WORKSPACE_PATH`.

5. **integ-test-dependency-submission.yml** (lines 116, 168, 240, 275, 318, 362): Fixed 6 steps across multiple jobs by moving step outputs and `github.workspace` into `env:` blocks with descriptive variable names (`KOTLIN_DSL_GRAPH_FILE`, `GROOVY_DSL_GRAPH_FILE`, `GROOVY_DSL_AGAIN_GRAPH_FILE`, `CONFIG_CACHE_GRAPH_FILE`, `DEPENDENCY_GRAPH_FILE`, `GITHUB_WORKSPACE_PATH`). Also properly quoted `rm` commands.

6. **integ-test-provision-gradle-versions.yml** (line 130): Moved `${{matrix.gradle}}` into `env:` block as `GRADLE_VERSION`.

7. **integ-test-wrapper-validation.yml** (line 42): Moved `${{ steps.setup-gradle.outcome}}` into `env:` block as `SETUP_GRADLE_OUTCOME`.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection vulnerabilities in `.github/workflows/integ-test-provision-gradle-versions.yml`:
1. Line ~76 (`Check current version output parameter` step): Moved `${{ steps.gradle-current.outputs.gradle-version }}` from inside the `script:` JavaScript block into a step-level `env:` block as `GRADLE_VERSION`, then referenced it as `process.env.GRADLE_VERSION` in the script using a template literal.
2. Line ~107 (`Check output parameter` step): Same fix applied for `${{ steps.setup-gradle.outputs.gradle-version }}` — moved to `env: GRADLE_VERSION:` and referenced via `process.env.GRADLE_VERSION` in the script.
Both fixes eliminate the risk of attacker-controlled Gradle version strings being interpolated directly into executable JavaScript code.

