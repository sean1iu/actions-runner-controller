https://github.com/github/c2c-actions-runtime/issues/2258

[actions-runner-controller/releases workflow](https://github.com/actions-runner-controller/releases/blob/main/.github/workflows/release-runners.yaml)
needs to run (for now) anytime we have a new runner release. This is currently
triggered by [a workflow in actions/actions-runner-controller](https://github.com/actions/actions-runner-controller/blob/master/.github/workflows/release-runners.yaml).

## Polling solution

We could have a workflow running hourly and checking whether there was a new
runner version released that we don't know about just yet. A sample flow could
go like:

- Run hourly, check actions/runner releases against one of [these files](https://github.com/actions/actions-runner-controller/commit/6da1cde09ca642e2cac02d6046ba2eefd6e8c31f) (or create a new one containing only the runner version, would probably make things easier).
- if there's a new release and we have not created a pull request for it already
  (humans can be slow and take more than an hour to review a PR), create a pull
  request updating [these files](https://github.com/actions/actions-runner-controller/commit/6da1cde09ca642e2cac02d6046ba2eefd6e8c31f)
- once that PR gets merged it will be business as usual

Does this need to happen within ARC or can it happen directly in
actions-runner-controller/releases? If those version changes are relevant for
ARC I would say it's better we keep them centralized, having multiple workflows
won't hurt us if they don't need attention.

## repository_dispatch solution
Requires setting up a PAT or a GitHub app with `repo` scopes withing the Actions
org.
