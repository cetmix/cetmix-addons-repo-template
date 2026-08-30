# Sync OCA upstream — reference

## Remotes

| Remote | URL | Role |
|--------|-----|------|
| `origin` | `https://github.com/cetmix/cetmix-addons-repo-template.git` | **Only** remote allowed for push / commits / PRs |
| `OCA` | `https://github.com/OCA/oca-addons-repo-template.git` | **Fetch-only.** NEVER update (`push` URL must stay `DISABLE_PUSH_TO_OCA`) |

**Hard rule:** NEVER commit, push, open a PR, or otherwise modify `OCA/oca-addons-repo-template`. Upstream is read-only; all changes land on Cetmix `origin` only.

## Documented Cetmix commits (non-exhaustive)

| Commit | Subject | Effect |
|--------|---------|--------|
| `2056d8a` | Replace maintainer tools with the cetmix fork | pre-commit → `cetmix-maintainer-tools` |
| `83b2aee` | Update maintainer-tools commit hash | Pin bump |
| `31fc855` | Generate .pot files in private repositories | Token-based pot push |
| `3b5dacc` / `e4203c0` | OPL-1 in pylint | Allow OPL-1 |
| `955ddfc` / `8cc395e` | Runboat link | Cetmix Runboat badge |
| `b1862bc` | update condition for .pot creation | Pot gating |
| `973693d` | Merge branch 'OCA-master' | Sync through OCA `6e74a6a` (uv, renovate, test.yml rename, …) |

## Pre-sync blob SHAs (as of `973693d` / OCA `6e74a6a`)

Refresh after each successful sync.

| Blob | Path |
|------|------|
| `a8d719fb7a68a42b4c5c67ba5934df57799ac142` | `README.md` |
| `2a7e4dad0c6973a94cdd5374ed531800aed0a41c` | `CONTRIBUTING.md` |
| `1b361b0bb94b8dd18a4de8169aeb793b23c7f333` | `copier.yml` |
| `5530760fdac2c5742be280f784784194e7f4a96d` | `pyproject.toml` |
| `a84cff444a788399108964e750da7f4d1672f6d0` | `src/.pre-commit-config.yaml.jinja` |
| `418d3f9845668501b8cc23c9d07467a2724da1a1` | `src/.pylintrc-mandatory.jinja` |
| `684b4136adbd0a22b7c7f3ef1b87568379c090d6` | `src/README.md.jinja` |
| `3bad668f26316903e7ba8fb5b259ce6acbdfd34a` | `src/.github/workflows/pre-commit.yml.jinja` |
| `98f782cd95696b1deeebf7f3f4f77e1d8b354bef` | `src/.github/workflows/test.yml.jinja` |
| `0bd92f20f43c1074cc87ef7df2c008a0e446867d` | `src/{% if github_odoo_ee %}runboat.ee{% endif %}.jinja` |
| `a65ece78e38a7bf392bb6880d586420c725916a5` | `version-specific/mqt-compat/.pylintrc-mandatory.jinja` |

## OCA tip differences to expect

After `973693d`, Cetmix delta vs OCA is mostly branding + EE/pot/Runboat/OPL-1/maintainer-tools URL (+ absent `src/LICENSE`). Future OCA tip may still bump pins, pylint checks, Actions, renovate/dependabot.

## Related local paths

- Maintainer-tools (Cetmix): `/Users/ivansokolov/odoo/pipeline-tools/cetmix-maintainer-tools`
- This template: `/Users/ivansokolov/odoo/pipeline-tools/cetmix-addons-repo-template`
- Contrib OCA-named fork (not consumer template): `/Users/ivansokolov/odoo/pipeline-tools/oca-addons-repo-template` → `cetmix/oca-addons-repo-template`
