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
| `000d1db` | Merge branch 'OCA-master' | Sync through OCA `0c2edf1` (Odoo 20.0) |

## Pre-sync blob SHAs (as of OCA `0c2edf1`, after the 20.0 sync)

Refresh after each successful sync. Hashes below are the Cetmix tree after that sync (pins included).

| Blob | Path |
|------|------|
| `3c3f6be4fbafeeb39db902566118867fabf45cfb` | `README.md` |
| `2a7e4dad0c6973a94cdd5374ed531800aed0a41c` | `CONTRIBUTING.md` |
| `9949bdb2931fe4294407d5dd67d095544bbb96e1` | `copier.yml` |
| `5530760fdac2c5742be280f784784194e7f4a96d` | `pyproject.toml` |
| `11dca46a5b2d1ea940ba9e318caef409aed8d2f5` | `src/.pre-commit-config.yaml.jinja` |
| `e4bb62f21a93b6fcc8c172c4ed60d693ff4b4296` | `src/.pylintrc-mandatory.jinja` |
| `684b4136adbd0a22b7c7f3ef1b87568379c090d6` | `src/README.md.jinja` |
| `631224b0bc2c5acceb28d27a2d992c5549f98a9f` | `src/.github/workflows/pre-commit.yml.jinja` |
| `4ed517f45729a266accb5e95716ba02d605ba309` | `src/.github/workflows/test.yml.jinja` |
| `cd6290cec68b95f98e17069ce7917c70d80274bc` | `src/{% if github_odoo_ee %}runboat.ee{% endif %}.jinja` |
| `a65ece78e38a7bf392bb6880d586420c725916a5` | `version-specific/mqt-compat/.pylintrc-mandatory.jinja` |

## OCA tip differences to expect

After the Odoo 20.0 sync (OCA `0c2edf1`), Cetmix delta vs OCA is branding, EE/pot/Runboat/OPL-1, maintainer-tools URL, 19.0/20.0 pins on the Cetmix maintainer-tools tip, pre-commit-only `ocabot-*`, and absent `src/LICENSE`. Future OCA tip may still bump pins, pylint checks, Actions, renovate/dependabot.

## Related local paths

- Maintainer-tools (Cetmix): `/Users/ivansokolov/odoo/pipeline-tools/cetmix-maintainer-tools`
- This template: `/Users/ivansokolov/odoo/pipeline-tools/cetmix-addons-repo-template`
- Contrib OCA-named fork (not consumer template): `/Users/ivansokolov/odoo/pipeline-tools/oca-addons-repo-template` → `cetmix/oca-addons-repo-template`
