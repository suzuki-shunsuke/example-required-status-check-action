# example-required-status-check-action

[![License](http://img.shields.io/badge/license-mit-blue.svg?style=flat-square)](https://raw.githubusercontent.com/suzuki-shunsuke/example-required-status-check-action/main/LICENSE) | [workflow](.github/workflow/pull_request.yaml)

Example of [required-status-check-action](https://github.com/suzuki-shunsuke/required-status-check-action)

## Configure Branch Rulesets

1. Enable `Require status checks to pass`
1. Add `status-check` to `Status checks that are required`

![image](https://github.com/user-attachments/assets/08bd3e7d-5267-44f4-8bb6-c1afe4ccc721)

## Example 1

```mermaid
graph LR;
    Test-->status-check;
    Build-->status-check;
```

Set up the workflow.
To merge pull requests, the jobs `test` and `build` must pass.

```yaml
name: pull request
on: pull_request
jobs:
  test:
    runs-on: ubuntu-24.04
    permissions: {}
    timeout-minutes: 10
    steps:
      - run: test -n "$FOO"
        env:
          FOO: ${{vars.FOO}}
  build:
    runs-on: ubuntu-24.04
    permissions: {}
    timeout-minutes: 10
    steps:
      - run: test -n "$FOO"
        env:
          FOO: ${{vars.FOO}}
  status-check:
    runs-on: ubuntu-24.04
    timeout-minutes: 10
    needs:
      - test
      - build
    if: always()
    permissions:
      contents: read # To get the workflow content by GitHub API
    steps:
      - uses: suzuki-shunsuke/required-status-check-action@latest
        with:
          needs: ${{ toJson(needs) }}
```

The variable `FOO` is used in the workflow, but please don't set the variable first.
Let's create a pull request, the workflow fails.
The job `test` and `build` fail, so `status-check` fails.
`status-check` fails, so the pull request can't be merged expectedly.

- [commit](https://github.com/suzuki-shunsuke/example-required-status-check-action/pull/3/commits/f40fb97b379273926a8444813ad7c75987360d71)
- [workflow](https://github.com/suzuki-shunsuke/example-required-status-check-action/actions/runs/13671523965?pr=3)

![image](https://github.com/user-attachments/assets/36fe3c41-400d-48d0-a26c-ce798ab91942)

Then set the variable `FOO` and rerun only `test`.

![image](https://github.com/user-attachments/assets/1578aef2-529d-4be1-a06a-ee036dc3df0a)

![rerun only test](https://github.com/user-attachments/assets/d3f6e31d-380b-4128-b8b1-b18a3506dba4)

Then `test` succeeds but `status-check` fails expectedly because `build` fails.

![image](https://github.com/user-attachments/assets/a214dd3e-00f0-461a-ae70-5402c73fe2ec)

Then `Re-run failed jobs`.

![Re-run failed jobs](https://github.com/user-attachments/assets/2d400304-b5a7-4f11-9364-2c4c25db7251)

Then all jobs succeed. `status-check` succeeds because `test` and `build` succeed.

![image](https://github.com/user-attachments/assets/5646e20b-ca13-402b-80c4-4121db5195b7)

## Example 2. A invalid

Let's add a new job `check`.

```yaml
name: pull request
on: pull_request
jobs:
  test:
    runs-on: ubuntu-24.04
    permissions: {}
    timeout-minutes: 10
    steps:
      - run: test -n "$FOO"
        env:
          FOO: ${{vars.FOO}}
  build:
    runs-on: ubuntu-24.04
    permissions: {}
    timeout-minutes: 10
    steps:
      - run: test -n "$FOO"
        env:
          FOO: ${{vars.FOO}}
  check:
    runs-on: ubuntu-24.04
    permissions: {}
    timeout-minutes: 10
    steps:
      - run: test -n "$FOO"
        env:
          FOO: ${{vars.FOO}}
  status-check:
    runs-on: ubuntu-24.04
    timeout-minutes: 10
    needs:
      - test
      - build
    if: always()
    permissions:
      contents: read # To get the workflow content by GitHub API
    steps:
      - uses: suzuki-shunsuke/required-status-check-action@latest
        with:
          needs: ${{ toJson(needs) }}
```

Note that `check` isn't included in `status-check`'s `needs` now.

## Example 3. Add a job

## Example 4. Run workflow check only when workflow is changed
