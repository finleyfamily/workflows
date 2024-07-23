# workflows

🤖 GitHub Action Workflows.

**Table Of Contents** <!-- markdownlint-disable-line MD036 -->

<!-- mdformat-toc start --slug=github --no-anchors --maxlevel=6 --minlevel=2 -->

- [Assign Author To PR](#assign-author-to-pr)
- [Python (build)](#python-build)
  - [Inputs](#inputs)
- [Python (checks)](#python-checks)
  - [Inputs](#inputs-1)
- [Release Drafter](#release-drafter)
- [Spellcheck](#spellcheck)
  - [Prerequisites](#prerequisites)
  - [Inputs](#inputs-2)

<!-- mdformat-toc end -->

## Assign Author To PR

Assigns the author of a pull request to the pull request.

```yaml
on:
  pull_request_target:
    types:
      - opened
      - reopened

jobs:
  assign-author-to-pr:
    uses: finleyfamily/workflows/.github/workflows/pr.assign-author.yml@master
```

## Python (build)

Build python package and upload distributable as an artifact of the job.

```yaml
on:
  pull_request:  # any pull request
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
  pull_request:  # any pull request
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
