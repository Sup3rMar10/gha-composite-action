# gha-composite-action

A **composite action** (`actions/test-summary`): it runs a command, writes a pass/fail summary with the last 40 lines of output to the job summary, and returns `status` as an output. Used from a workflow in the same repo with `uses: ./actions/test-summary`.

## What ran (2026-10-16)

The Actions run **succeeded** with three jobs, two of them self-tests of the action:

| Job | Proves |
|---|---|
| `use-the-action` | normal use on a passing unit test |
| `self-test-failure-path` | a failing command makes `status` = `failed`, and `fail-on-error: false` keeps the job green |
| `self-test-injection` | a command containing quotes and `$()` runs as a command and does not break the action's script |

## Decisions

- **The command goes through `env:`, never straight into the script.** Writing `${{ inputs.command }}` into a shell script lets input become code; an environment variable cannot.
- **Self-tests inside the CI** so a change to the action cannot quietly break the failure path.
- **Output through `$GITHUB_OUTPUT`**, summary through `$GITHUB_STEP_SUMMARY`.
- **Composite vs JavaScript vs Docker actions:** composite is the cheapest (shell steps, no build); JavaScript is faster to start and can use the toolkit; Docker is slow to start and Linux-only. Choose composite until you need more.

## Not covered

Publishing the action for other repos (versioning with tags, the Marketplace), Windows runners (the script assumes bash), and the output of very long logs (only the last 40 lines are kept).

MIT licensed.
