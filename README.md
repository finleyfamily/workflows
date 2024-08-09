# workflows

[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit)](https://github.com/pre-commit/pre-commit)
[![renovate](https://img.shields.io/badge/enabled-brightgreen?logo=renovatebot&logoColor=%2373afae&label=renovate)](https://developer.mend.io/github/finlyfamily/workflows)

🤖 GitHub Action Workflows.

**Table Of Contents** <!-- markdownlint-disable-line MD036 -->

<!-- mdformat-toc start --slug=github --no-anchors --maxlevel=6 --minlevel=2 -->

- [pull_request_target](#pull_request_target)
- [Python (build)](#python-build)
  - [Inputs](#inputs)
- [Python (checks)](#python-checks)
  - [Inputs](#inputs-1)
- [Release Drafter](#release-drafter)
- [Spellcheck](#spellcheck)
  - [Prerequisites](#prerequisites)
  - [Inputs](#inputs-2)

<!-- mdformat-toc end -->

## pull_request_target

- Assigns the author of a pull request to the pull request.
- Enforce a branch naming convention.
- Apply labels to PR.

```yaml
name: pull_request_target

on:
  pull_request_target:

jobs:
  workflows:
    permissions:
      contents: write
      pull-requests: write
    uses: finleyfamily/workflows/.github/workflows/pull_request_target.yml@master
```

## Python (build)

Build python package and upload distributable as an artifact of the job.

```yaml
on:
  pull_request:
  push:
    branches:
      - master

jobs:
  python-build:
    uses: finleyfamily/workflows/.github/workflows/python.build.yml@master
```

### Inputs

| Input            | Description                                                                               | Required |
| ---------------- | ----------------------------------------------------------------------------------------- | -------- |
| `poetry-plugins` | A whitespace seperated list of poetry plugins to be installed (e.g. `plugin0` `plugin1`). | False    |

## Python (checks)

Lint and test python code.

```yaml
on:
  pull_request:
  push:
    branches:
      - master

jobs:
  python-checks:
    strategy:
      matrix:
        python-version: [3.11, 3.12]
    uses: finleyfamily/workflows/.github/workflows/python.checks.yml@master
    with:
      node-version: '20'
      python-version: ${{ matrix.python-version }}
```

### Inputs

| Input            | Description                                                                                                                         | Required |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `disable-tests`  | Disable tests.                                                                                                                      | False    |
| `node-version`   | Version of node to use (e.g. `20`).                                                                                                 | False    |
| `poetry-plugins` | A whitespace seperated list of poetry plugins to be installed (e.g. `plugin0` `plugin1`).                                           | False    |
| `python-version` | Version range or exact version of a Python version to use, using semver version range syntax. Reads from `pyproject.toml` if unset. | False    |

## Release Drafter

Drafts releases based on pull requests.

```yaml
on:
  push:
    # branches to consider in the event; optional, defaults to all
    branches:
      - master

jobs:
  release-drafter:
    permissions:
      contents: write
    uses: finleyfamily/workflows/.github/workflows/release-drafter.yml@master
```

## Spellcheck

Uses [cspell] to check spelling within a project.

```yaml
on:
  pull_request:  # any pull request
  push:
    branches:
      - master

jobs:
  spellcheck:
    uses: finleyfamily/workflows/.github/workflows/spellcheck.yml@master
    with:
      node-version: '20'
```

### Prerequisites

- `setup-npm` target in `Makefile` to install node dependencies
- `spellcheck` target in `Makefile` to run [cspell]

### Inputs

| Input          | Description                         | Required |
| -------------- | ----------------------------------- | -------- |
| `node-version` | Version of node to use (e.g. `20`). | False    |

[cspell]: https://github.com/streetsidesoftware/cspell
