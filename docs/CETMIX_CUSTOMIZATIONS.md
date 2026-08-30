# Cetmix customizations

This repository is the Cetmix fork of [OCA/oca-addons-repo-template](https://github.com/OCA/oca-addons-repo-template).

**Canonical remote:** `https://github.com/cetmix/cetmix-addons-repo-template` (`origin`).

**NEVER update the OCA repo.** Write only to Cetmix (`origin`). No commit, push, PR, or any write to `OCA/oca-addons-repo-template`. Sync is fetch + merge into this fork only; keep the `OCA` remote push URL disabled.

Consumer addon repos (e.g. `cetmix-tools`) set:

```yaml
_src_path: https://github.com/cetmix/cetmix-addons-repo-template.git
```

Related tooling: [cetmix/cetmix-maintainer-tools](https://github.com/cetmix/cetmix-maintainer-tools) (local clone often at `~/odoo/pipeline-tools/cetmix-maintainer-tools`). See that repo’s `docs/CETMIX_CUSTOMIZATIONS.md` and `.cursor/skills/sync-oca-upstream/`.

Only the changes below are intentional Cetmix customizations. Everything else should track OCA `master` when syncing (unless noted).

## Sibling forks (do not confuse)

| Repo | Role |
|------|------|
| `cetmix/cetmix-addons-repo-template` | **This repo** — template used by Cetmix addon repos |
| `cetmix/oca-addons-repo-template` | Separate contrib/upstream-tracking fork; not the consumer template |
| `OCA/oca-addons-repo-template` | Upstream |

## 1. Branding and Copier defaults

**Files:** `README.md`, `CONTRIBUTING.md`, `copier.yml`, `pyproject.toml`

- Package/name: `cetmix-addons-repo-template`; authors Cetmix.
- Bootstrap/update docs use `https://github.com/cetmix/cetmix-addons-repo-template.git` with `copier … -r HEAD` (no `--UNSAFE` needed on current Copier).
- Defaults: `org_slug=cetmix`, `org_name=Cetmix`, `repo_website=https://cetmix.com`, `odoo_version=19.0`.
- `use_ruff` follows OCA: yes for Odoo ≥ 17, no for 14–16 (question hidden below 14).
- Extra question: `github_odoo_ee` (default `yes`) — load Cetmix enterprise addons in CI.
- Extra question from OCA: `postgres_image` (optional).
- Dev env follows OCA: `uv` + `[dependency-groups]` (`uv run pytest`). Package name stays `cetmix-addons-repo-template`.

## 2. Pre-commit → Cetmix maintainer-tools

**Commits:** `2056d8a`, `83b2aee`, …

**File:** `src/.pre-commit-config.yaml.jinja`

- Repo URL must be `https://github.com/cetmix/cetmix-maintainer-tools` (not `OCA/maintainer-tools`).
- Every `repo_rev.maintainer_tools` pin must be a commit that exists on the **Cetmix** maintainer-tools fork.
- Keep `oca-fix-manifest-website` args as `["{{ repo_website }}"]` (defaults to `https://cetmix.com`).

Pin bumps: use skill `.cursor/skills/bump-maintainer-tools-pin/`.

## 3. Pylint: OPL-1 + Cetmix readme template URLs

**Commits:** `3b5dacc`, `4f096d9`, …

**Files:**

- `src/.pylintrc-mandatory.jinja`
- `version-specific/mqt-compat/.pylintrc-mandatory.jinja`

- `license_allowed` / `license-allowed` must include **`OPL-1`**.
- Readme template URLs currently point at `https://github.com/cetmix/maintainer-tools/...` (historical path). Prefer aligning to `cetmix/cetmix-maintainer-tools` when touching these files; do not silently revert to OCA URLs.

When merging OCA pylint check lists (`deprecated-module`, `deprecated-self-cr`, `translation-injection`, …), **re-apply Cetmix license/URL deltas** after taking OCA’s check set unless the user asks otherwise.

## 4. GitHub Actions: EE clone, private `.pot` push, Runboat token

**Files:**

- `src/.github/workflows/test.yml.jinja` (OCA renamed from conditional `{% if ci == 'GitHub' %}test.yml{% endif %}.jinja`)
- `src/.github/workflows/pre-commit.yml.jinja`

Cetmix `test.yml` must keep:

- Push branches include `{{ odoo_version }}-dev` (not only `ocabot-*`).
- `RUNBOAT_GITHUB_TOKEN: ${{ secrets.GIT_PUSH_TOKEN }}`.
- When `github_odoo_ee`: clone `cetmix/enterprise` tarball for `$ODOO_VERSION` into `$ADDONS_PATH` using `secrets.GIT_PUSH_TOKEN`.
- `.pot` export uses `GIT_PUSH_TOKEN` / `x-access-token` so private repos can push (commit `31fc855`).

On OCA sync: do **not** drop these blocks when adopting OCA workflow renames/structure. Prefer porting Cetmix EE/pot/dev-branch behavior into whatever filename OCA uses, then delete the obsolete Cetmix-only path if both would render.

## 5. Repo README badges and Runboat

**Files:** `src/README.md.jinja`, `src/{% if github_odoo_ee %}runboat.ee{% endif %}.jinja`

- Non-OCA Runboat badge → `https://runboat.cetmix.com/webui/builds.html?repo=…`.
- Optional `runboat.ee` marker file when `github_odoo_ee` is enabled.
- OCA banner image may still appear in the rendered README template; Cetmix historically left branding mixed—do not reintroduce OCA-only assumptions for `org_slug != OCA` without checking consumer repos.

## 6. Absent `src/LICENSE`

Cetmix tree has **no** `src/LICENSE` while OCA still ships AGPL text there. Treat as intentional for multi-license (incl. OPL-1) modules unless the user asks to restore it. On sync, do not blindly restore OCA’s file without confirmation.

`tests/test_copy.py` asserts LICENSE is absent and expects Cetmix Runboat/codecov badges (not OCA’s).

## Not customizations / take OCA on sync

| Observation | Action |
|-------------|--------|
| OCA renames workflow to `test.yml.jinja`, adds `postgres_image`, renovate, uv.lock, pylint check updates | Take OCA structure/features, then **re-apply** EE / pot / `-dev` / Cetmix maintainer-tools / OPL-1 |
| Stale pin drift vs latest `cetmix-maintainer-tools` | Fix via bump skill; not an excuse to point back at OCA maintainer-tools |
| Cosmetic comment URL `cetmix/maintainer-quality-tools` in eslintrc | Harmless; optional fix |

## Syncing from OCA

Use `.cursor/skills/sync-oca-upstream/`. After merge, always re-verify this inventory — merges can silently overwrite Cetmix URLs and workflow blocks.
