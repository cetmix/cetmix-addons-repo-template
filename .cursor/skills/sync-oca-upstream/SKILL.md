---
name: sync-oca-upstream
description: >-
  Sync cetmix/cetmix-addons-repo-template with upstream
  OCA/oca-addons-repo-template while preserving Cetmix customizations. Use when
  updating from OCA, merging upstream, or refreshing this Cetmix Copier
  template fork.
---

# Sync OCA upstream into Cetmix addons-repo-template

## Facts (do not invent others)

- This repo: `https://github.com/cetmix/cetmix-addons-repo-template` (`origin`).
- Upstream: `https://github.com/OCA/oca-addons-repo-template` (remote name: `OCA`, branch: `master`).
- **NEVER update the OCA repo.** No commit, push, PR, branch, tag, release, issue, or any write to `OCA/oca-addons-repo-template` (or any other OCA org repo). Sync is **fetch + merge into Cetmix only**. Only `origin` (Cetmix) is a write remote. Keep `OCA` fetch-only (`git remote set-url --push OCA DISABLE_PUSH_TO_OCA`).
- Companion fork: `cetmix/cetmix-maintainer-tools` — pre-commit must keep pointing there. Sync that repo with its own skill when needed.
- Not this repo: `cetmix/oca-addons-repo-template` (contrib fork). Do not merge that into here by mistake.

## Customizations to preserve

Authoritative inventory: [docs/CETMIX_CUSTOMIZATIONS.md](../../../docs/CETMIX_CUSTOMIZATIONS.md).

| Area | Paths / checks | Required end state |
|------|----------------|--------------------|
| Maintainer-tools | `src/.pre-commit-config.yaml.jinja` | `repo: https://github.com/cetmix/cetmix-maintainer-tools` + Cetmix SHAs |
| Copier defaults | `copier.yml` | `org_slug`/`org_name`/`repo_website` Cetmix; keep `github_odoo_ee` |
| OPL-1 | `src/.pylintrc-mandatory.jinja`, `version-specific/mqt-compat/.pylintrc-mandatory.jinja` | `OPL-1` in allowed licenses |
| CI EE + pot | GitHub `test` workflow jinja (`src/.github/workflows/test.yml.jinja`) | EE tarball + `GIT_PUSH_TOKEN` pot push + `-dev` branches |
| Runboat | `src/README.md.jinja`, `runboat.ee` jinja | Cetmix Runboat URL / EE marker |
| Branding | `README.md`, `CONTRIBUTING.md`, `pyproject.toml` | Cetmix names and clone URLs |
| LICENSE | `src/LICENSE` | Stay absent unless user asks to restore |

## Workflow

```
Sync progress:
- [ ] 1. Preconditions
- [ ] 2. Snapshot customizations
- [ ] 3. Fetch and merge OCA/master
- [ ] 4. Re-apply customizations / resolve workflow rename
- [ ] 5. Verify inventory
- [ ] 6. Run tests
- [ ] 7. Stop for user review (commit/push only if asked)
```

### 1. Preconditions

```bash
git status   # must be clean
git remote get-url OCA || git remote add OCA https://github.com/OCA/oca-addons-repo-template.git
git remote set-url --push OCA DISABLE_PUSH_TO_OCA
git fetch OCA master
git merge-base HEAD OCA/master
git log --oneline HEAD..OCA/master
```

If working tree is dirty, stop.

### 2. Snapshot customizations

`PRESYNC=$(git rev-parse HEAD)`. Archive or note blob SHAs for preserve-list files (see [reference.md](reference.md)).

### 3. Fetch and merge

```bash
git merge --no-ff OCA/master -m "$(cat <<'EOF'
Merge branch 'OCA-master'

EOF
)"
```

### 4. Re-apply customizations

Restore preserve-list content from `$PRESYNC` where OCA overwrote Cetmix behavior. Special cases:

- **Workflow rename:** OCA uses `src/.github/workflows/test.yml.jinja`; Cetmix may still have `{% if ci == 'GitHub' %}test.yml{% endif %}.jinja`. After merge, keep a single rendered path: port Cetmix EE / pot / `-dev` / `RUNBOAT_GITHUB_TOKEN` into the surviving file, remove duplicates.
- **pre-commit URL:** force `cetmix/cetmix-maintainer-tools` even if OCA reset pins/URL.
- **pylintrc:** re-add `OPL-1`; do not leave OCA-only license lists.
- **`src/LICENSE`:** if merge restored it, `git rm` unless the user wants it back.
- **`copier.yml`:** restore Cetmix defaults and `github_odoo_ee`; carefully merge new OCA questions (e.g. `postgres_image`) if useful.

If OCA changed a preserve-list file in a way that must be combined (not overwrite), stop and ask — do not guess.

### 5. Verify inventory

```bash
grep -n 'cetmix/cetmix-maintainer-tools' src/.pre-commit-config.yaml.jinja
! grep -n 'github.com/oca/maintainer-tools\|github.com/OCA/maintainer-tools' src/.pre-commit-config.yaml.jinja
grep -n 'OPL-1' src/.pylintrc-mandatory.jinja version-specific/mqt-compat/.pylintrc-mandatory.jinja
grep -n 'github_odoo_ee\|GIT_PUSH_TOKEN\|cetmix/enterprise\|runboat.cetmix.com' \
  copier.yml src/README.md.jinja src/.github/workflows/*
test ! -e src/LICENSE
git diff --stat OCA/master
```

### 6. Run tests

```bash
uv run pytest
```

If `uv` is missing or the lockfile is broken after sync, report and ask before changing the toolchain.

### 7. Finish

Do not commit or push unless the user asks. Report: OCA tip SHA, merge commit (if any), verification results, test results, remaining behind/ahead counts.

## Anti-patterns

- **NEVER update the OCA repo** — no push, PR, commit, or write of any kind to `OCA/*`.
- Do not repoint pre-commit at OCA maintainer-tools “to match upstream”.
- Do not force-push Cetmix `master`.
- Do not skip re-apply after merge.
