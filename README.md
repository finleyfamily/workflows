# workflows

[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit)](https://github.com/pre-commit/pre-commit)
[![renovate](https://img.shields.io/badge/enabled-brightgreen?logo=renovatebot&logoColor=%2373afae&label=renovate)](https://developer.mend.io/github/finlyfamily/workflows)
[![license][license-shield]](./LICENSE)

🤖 GitHub Action Workflows.

**Table Of Contents** <!-- markdownlint-disable-line MD036 -->

<!-- mdformat-toc start --slug=github --no-anchors --maxlevel=6 --minlevel=2 -->

- [Home Assistant Add-on](#home-assistant-add-on)
  - [CI](#ci)
  - [Deploy](#deploy)
- [Home Assistant Repository](#home-assistant-repository)
  - [CI](#ci-1)
  - [Repository Updater](#repository-updater)
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

## Home Assistant Add-on

Workflows for Home Assistant add-ons.

Inspired by [hassio-addons/workflows](https://github.com/hassio-addons/workflows).

### CI

```yaml
name: CI

on:
  pull_request:
  push:
    branches:
      - master
  workflow_dispatch:

jobs:
  workflows:
    permissions:
      pull-requests: read
    uses: finleyfamily/workflows/.github/workflows/hass-addon.ci.yml@master
```

### Deploy

Deploy add-on to repositories.

```yaml
name: Deploy

on:
  release:
    types:
      - published
  workflow_run:
    workflows:
      - CI
    branches:
      - master
    types:
      - completed

jobs:
  auth:
    name: Create app token
    runs-on: ubuntu-latest
    outputs:
      token: ${{ steps.app-token.outputs.token }}
    steps:
      - name: 🏗 Create app token
        id: app-token
        uses: actions/create-github-app-token@v1
        with:
          app-id: ${{ vars.FINLEY_APP_ID }}
          owner: ${{ github.repository_owner }}
          private-key: ${{ secrets.FINLEY_APP_PRIVATE_KEY }}
          repositories: hass-repository,hass-repository-edge
  workflows:
    needs: auth
    uses: finleyfamily/workflows/.github/workflows/hass-addon.deploy.yml@master
    secrets:
      DISPATCH_TOKEN: ${{ needs.auth.outputs.token }}
```

## Home Assistant Repository

Workflows for Home Assistant repositories.

Inspired by [hassio-addons/workflows](https://github.com/hassio-addons/workflows).

### CI

```yaml
name: CI

on:
  pull_request:
  push:
    branches:
      - master

jobs:
  workflows:
    uses: finleyfamily/workflows/.github/workflows/hass-repository.ci.yml@master
```

### Repository Updater

Triggered from individual add-on repos to update to main add-on repository.

```yaml
name: Repository Updater

# yamllint disable-line rule:truthy
on:
  repository_dispatch:
    types: ["update"]

jobs:
  auth:
    name: Create app token
    runs-on: ubuntu-latest
    outputs:
      token: ${{ steps.app-token.outputs.token }}
    steps:
      - name: 🏗 Create app token
        id: app-token
        uses: actions/create-github-app-token@v1
        with:
          app-id: ${{ vars.FINLEY_APP_ID }}
          private-key: ${{ secrets.FINLEY_APP_PRIVATE_KEY }}
  workflows:
    needs: auth
    uses: finleyfamily/workflows/.github/workflows/hass-repository.updater.yml@master
    secrets:
      UPDATER_TOKEN: ${{ needs.auth.outputs.token }}
```

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

For a list of inputs with default values, descriptions, and types, see [`python.build.yml`](./.github/workflows/python.build.yml)

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

For a list of inputs with default values, descriptions, and types, see [`python.checks.yml`](./.github/workflows/python.checks.yml)

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

For a list of inputs with default values, descriptions, and types, see [`spellcheck.yml`](./.github/workflows/spellcheck.yml)

[cspell]: https://github.com/streetsidesoftware/cspell
[license-shield]: https://img.shields.io/github/license/finleyfamily/workflows.svg
