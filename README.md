# .github

Shared GitHub defaults for repositories owned by [alexiszamanidis](https://github.com/alexiszamanidis).

GitHub uses the community files in this public repository for any of my repositories that do not define their own copy. A repository-level file always wins.

## Defaults that apply automatically

These files are used by GitHub as fallbacks across my public repositories:

| File | Purpose |
| --- | --- |
| [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) | Expected behavior and how to report problems |
| [CONTRIBUTING.md](CONTRIBUTING.md) | How to open issues and pull requests |
| [GOVERNANCE.md](GOVERNANCE.md) | Who maintains the projects and how decisions are made |
| [SECURITY.md](SECURITY.md) | How to report vulnerabilities privately |
| [SUPPORT.md](SUPPORT.md) | Where to get help |
| [FUNDING.yml](FUNDING.yml) | Sponsor button |
| [PULL_REQUEST_TEMPLATE.md](PULL_REQUEST_TEMPLATE.md) | Default pull request description |
| [.github/ISSUE_TEMPLATE](.github/ISSUE_TEMPLATE) | Bug, feature, and documentation issue forms |
| [.github/DISCUSSION_TEMPLATE](.github/DISCUSSION_TEMPLATE) | Q&A and idea discussion forms |

## Reusable workflows

Reusable workflows live here once and can be called from any repository. They do **not** run automatically; each app opts in.

### Prettier format

This matches the format job already used in `ansible`, `ai-agents`, and `dotfiles`. It runs `npm run format` and commits the result when the workflow has write access.

In another repository, add `.github/workflows/format.yml`:

```yaml
name: ci

on: [pull_request, push]

permissions:
    contents: write

jobs:
    format:
        uses: alexiszamanidis/.github/.github/workflows/prettier.yml@master
```

Optional inputs: `node-version` (default `22`), `format-command` (default `format`), and `commit-message`.

The calling repository still needs its own `package.json` with a `format` script. Shared Prettier rules live in [@alexiszamanidis/prettier-config](https://github.com/alexiszamanidis/prettier-config).

### Ansible lint

Used by `ansible`, `ansible-work`, and `dotfiles`. It installs Ansible and ansible-lint, optionally unlocks vault and installs Galaxy collections, then runs `ansible-lint --fix`.

```yaml
jobs:
    lint:
        needs: [format]
        uses: alexiszamanidis/.github/.github/workflows/ansible-lint.yml@master
        permissions:
            contents: write
        secrets:
            VAULT_PASSWORD: ${{ secrets.VAULT_PASSWORD }}
        with:
            use-vault: true
            install-galaxy: true
```

Optional inputs: `python-version` (default `3.12`), `ansible-version` (default `14.2.0`), `ansible-lint-version` (default `25.7.0`), `use-vault` (default `false`), and `install-galaxy` (default `false`).

Each repo still keeps its own `.ansible-lint` config. Only pass `VAULT_PASSWORD` and set `use-vault: true` when the repo needs it.

## What stays per repository

GitHub does not inherit these from a `.github` repository. Keep them in each project that needs them:

* `LICENSE` - already MIT in the open-source repos that need one
* `.github/CODEOWNERS`
* `.github/dependabot.yml`
* Project-specific CI, release, and deploy workflows
* Shareable tool configs such as Prettier (`prettier-config`) and editor settings (`.editorconfig`)

`vscode-git-worktrees` already has its own community files and templates, so those continue to override these defaults.
