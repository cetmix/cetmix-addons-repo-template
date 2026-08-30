---
name: bump-maintainer-tools-pin
description: >-
  Bump repo_rev.maintainer_tools commit pins in cetmix-addons-repo-template
  pre-commit jinja to SHAs from cetmix/cetmix-maintainer-tools. Use when
  updating maintainer-tools hooks, fixing license/readme thrash from stale
  pins, or aligning template pins with the Cetmix maintainer-tools tip.
---

# Bump Cetmix maintainer-tools pins in the template

## Facts

- Edit: `src/.pre-commit-config.yaml.jinja` (and `version-specific/**/.pre-commit-config.yaml.jinja` if those blocks hardcode a rev).
- Hook repo URL must stay: `https://github.com/cetmix/cetmix-maintainer-tools`
- Pin source repo (local): `/Users/ivansokolov/odoo/pipeline-tools/cetmix-maintainer-tools` or `git ls-remote https://github.com/cetmix/cetmix-maintainer-tools.git HEAD`
- Do **not** use OCA/maintainer-tools SHAs unless that commit is reachable from the Cetmix fork (usually after a sync there).

## Workflow

```
Bump progress:
- [ ] 1. Pick target SHA from cetmix-maintainer-tools
- [ ] 2. Update all version-block maintainer_tools pins (or the blocks user named)
- [ ] 3. Confirm repo URL is still cetmix/cetmix-maintainer-tools
- [ ] 4. Run template tests
- [ ] 5. Stop for user review
```

### 1. Target SHA

```bash
cd /Users/ivansokolov/odoo/pipeline-tools/cetmix-maintainer-tools
git fetch origin
git rev-parse origin/master
# or: git ls-remote https://github.com/cetmix/cetmix-maintainer-tools.git refs/heads/master
```

Prefer one SHA across all Odoo version blocks unless the user asks for per-version pins.

### 2. Update pins

In `src/.pre-commit-config.yaml.jinja`, replace every:

```jinja
{%- set repo_rev.maintainer_tools = "<old>" %}
```

with the new SHA. Keep the `repo:` line on `cetmix/cetmix-maintainer-tools`.

Also check:

```bash
rg -n 'maintainer_tools|maintainer-tools' \
  src/.pre-commit-config.yaml.jinja \
  version-specific/
```

### 3. Verify

```bash
rg -n 'repo:.*maintainer-tools' -A2 src/.pre-commit-config.yaml.jinja
# Expect only cetmix/cetmix-maintainer-tools
rg -n 'maintainer_tools =' src/.pre-commit-config.yaml.jinja
```

### 4. Test

```bash
poetry run pytest
```

### 5. Finish

Do not commit unless asked. Remind: consumers pick up the new pin on `copier update --UNSAFE -r HEAD` (skill `manage-cetmix-template`).

## Why this matters

Stale pins vs the Cetmix fork cause hook thrash (e.g. license key renames flipping each run). Keep template pins on a Cetmix tip that includes the customizations documented in maintainer-tools `docs/CETMIX_CUSTOMIZATIONS.md`.
