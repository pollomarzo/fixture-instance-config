# fixture-instance-config — the interim INSTANCE-CONFIG (repo=paper tier)

The tenant's journal identity for the interim dogfood: `journal.yml` manifest + `editions/`
+ `brand/` + `registry/`. Mirrors the engine's `test/fixture-instance/` unit fixture,
reshaped into a standalone repo. Data only — no engine logic lives here (design §3, §5).

## This repo MUST remain public ([R32], dec. 16)

Stage-1 fork-PR builds hold **no secrets** and a read-only token (design §1b), and
`ci/run.sh` clones the instance repo with a plain **tokenless** `git clone --depth 1`:

```bash
git clone --depth 1 "https://github.com/${INSTANCE_REPO}.git" "$inst_dir"
```

A private instance-config silently breaks every fork preview (the clone 401s with no token
to fall back on). It is brand + data, so public is fine — `oak validate` warns and
`oak bootstrap` enforces public visibility. **Do not make this repo private.**

## Layout

| Path | Role |
|---|---|
| `journal.yml` | tenant manifest: tier, `id_sentinel`, `id_pattern`, preview provider, zenodo community (§3) |
| `editions/fixture-edition.yml` | edition data — venue/license/funding; declares NO `exports:` ([R52]/[R53]) |
| `brand/brand.yml` + `brand/logo.svg` | visual identity; logo is a REAL file (typst/theme load by path, [R38]) |
| `registry/papers.yml` | one entry `{id, slug, location, edition}` → `pollomarzo/fixture-paper-repo` ([R47]) |

## Key coupling to the paper repo

- `registry/papers.yml` entry `id: fixture-2026-interim-paper` **must** equal the paper's
  `project.id`.
- `journal.yml` `id_pattern: ^fixture-\d{4}-[a-z0-9-]+$` accepts that id; `id_sentinel:
  fixture-template-placeholder` is the one id `oak validate` rejects — the paper deliberately
  uses neither the sentinel nor the pattern-violating form.
- `preview.provider: artifact` → with no Cloudflare secrets, `oak deploy-preview` degrades to
  an artifact-link comment instead of failing ([R6]).
