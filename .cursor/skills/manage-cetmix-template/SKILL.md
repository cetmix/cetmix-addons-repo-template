---
name: manage-cetmix-template
description: >-
  Manage the Cetmix Copier addons-repo template and consumer addon repos:
  bootstrap, copier update, develop/test the template, and route upstream sync
  or maintainer-tools pin bumps. Use when working on
  cetmix-addons-repo-template, .copier-answers.yml, or Cetmix addon repo CI
  scaffolding.
---

# Manage Cetmix addons-repo-template

## Facts

- Template: `https://github.com/cetmix/cetmix-addons-repo-template`
- Local path (typical): `/Users/ivansokolov/odoo/cetmix-addons-repo-template`
- Consumers set `_src_path: https://github.com/cetmix/cetmix-addons-repo-template.git`
- Hooks come from `https://github.com/cetmix/cetmix-maintainer-tools`
- Customization inventory: [docs/CETMIX_CUSTOMIZATIONS.md](../../../docs/CETMIX_CUSTOMIZATIONS.md)

## Choose a workflow

| Goal | Do this |
|------|---------|
| Bootstrap a new addon repo | § Bootstrap |
| Refresh an existing addon repo from template | § Update consumer |
| Change template jinja / Copier questions | § Develop template |
| Merge OCA upstream into the template | Skill `sync-oca-upstream` |
| Move `repo_rev.maintainer_tools` pins | Skill `bump-maintainer-tools-pin` |

## Bootstrap

```bash
pipx install copier pre-commit
copier copy -r HEAD \
  https://github.com/cetmix/cetmix-addons-repo-template.git some-repo
cd some-repo
git init  # if needed
git add .
pre-commit install
pre-commit run -a
git commit -am 'Hello world'
```

Typical answers for Cetmix: `org_slug=cetmix`, `org_name=Cetmix`, `repo_website=https://cetmix.com`, `ci=GitHub`, `github_odoo_ee=yes` (needs `GIT_PUSH_TOKEN` + access to `cetmix/enterprise` for EE CI).

## Update consumer

In the addon repo:

```bash
copier update -r HEAD
pre-commit run
git add -A
# commit only if user asks
pre-commit run -a || true
```

Resolve `.rej` files manually; never leave forbidden `*.rej` (pre-commit fails them).

After update, confirm `.pre-commit-config.yaml` still uses `cetmix/cetmix-maintainer-tools` (a bad merge can revert to OCA).

## Develop template

```bash
cd /Users/ivansokolov/odoo/cetmix-addons-repo-template   # or this clone
uv run pytest
```

Layout:

| Path | Role |
|------|------|
| `copier.yml` | Questions / defaults / tasks |
| `src/` | Rendered tree (jinja) |
| `version-specific/` | Older Odoo pre-commit / pylint includes |
| `tests/` | Copier render + pre-commit smoke tests |
| `.github/workflows/` | CI for the **template** itself (not copied into consumers) |

Consumer workflows live under `src/.github/workflows/*.jinja`.

## Secrets / ops assumptions

Consumer CI expects GitHub secret `GIT_PUSH_TOKEN` when:

- `github_odoo_ee` is true (clone `cetmix/enterprise`)
- `github_enable_makepot` pushes `.pot` on branch push (incl. private repos)
- Runboat env injection uses the same secret name

## Anti-patterns

- **NEVER update the OCA repo** (`OCA/oca-addons-repo-template` or any OCA org write). Upstream sync is fetch + merge into Cetmix only.
- Do not tell consumers to copy from `OCA/oca-addons-repo-template` or `cetmix/oca-addons-repo-template`.
- Do not commit/push unless asked.
- Do not “fix” consumer repos by pointing hooks at OCA maintainer-tools.
