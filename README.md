# workflows

🤖 GitHub Action Workflows.

**Table Of Contents** <!-- markdownlint-disable-line MD036 -->

<!-- mdformat-toc start --slug=github --no-anchors --maxlevel=6 --minlevel=2 -->

- [Assign Author To PR](#assign-author-to-pr)
- [Release Drafter](#release-drafter)
- [Spellcheck](#spellcheck)
  - [Prerequisites](#prerequisites)
  - [Inputs](#inputs)

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

env:
  NODE_VERSION: '20'

jobs:
  spellcheck:
    uses: finleyfamily/workflows/.github/workflows/spellcheck.yml@master
    with:
      node-version: ${{ env.NODE_VERSION }}
```

### Prerequisites

- `setup-npm` target in `Makefile` to install node dependencies
- `spellcheck` target in `Makefile` to run [cspell]

### Inputs

| Input          | Description                        | Required |
| -------------- | ---------------------------------- | -------- |
| `node-version` | Version of node to use (e.g. `20`) | False    |

[cspell]: https://github.com/streetsidesoftware/cspell
