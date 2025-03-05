# example-required-status-check-action

[![License](http://img.shields.io/badge/license-mit-blue.svg?style=flat-square)](https://raw.githubusercontent.com/suzuki-shunsuke/example-required-status-check-action/main/LICENSE) | [workflow](.github/workflow/pull_request.yaml)

Example of [required-status-check-action](https://github.com/suzuki-shunsuke/required-status-check-action)

## Configure Branch Rulesets

![image](https://github.com/user-attachments/assets/08bd3e7d-5267-44f4-8bb6-c1afe4ccc721)

## Example 1

```yaml
name: pull request
on: pull_request
jobs:
  test:
    runs-on: ubuntu-24.04
    permissions: {}
    timeout-minutes: 10
    steps:
      - run: echo test
  build:
    runs-on: ubuntu-24.04
    permissions: {}
    timeout-minutes: 10
    steps:
      - run: echo build
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

## Example 2. A invalid

## Example 3. Add a job

## Example 4. Run workflow check only when workflow is changed
