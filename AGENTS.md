# Instructions for coding agents / LLMs

This repo is the Mintlify documentation site for gligabao (`../gligabao`). The source of truth for what gligabao does is
that repo: its `docs/*.md`, `README.md`, `CHANGELOG.md` and the code. This site must say the same.

## Versioning and changelog (required for every change)

This repo is versioned on its own (separately from `../gligabao`). The version lives in two files at the repo root:

- `VERSION`: the current semantic version, one line, no `v` prefix (e.g. `0.2.0`).
- `CHANGELOG.md`: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) format, newest entry first.

With every change that is committed (pages, navigation, theme, assets; not pure typo fixes):

1. Pick the new version (semver; while `0.x`): **minor** for new pages, new sections, restructured navigation or
   anything that moves a URL (renamed/removed pages break inbound links); **patch** for content corrections, wording,
   link fixes and syncing pages with a gligabao patch release. Use `1.0.0` only when the user says so.
2. Write the new `VERSION`.
3. Add a new section at the top of `CHANGELOG.md`, directly under the intro: `## [x.y.z] - YYYY-MM-DD` with
   `### Added` / `### Changed` / `### Fixed` / `### Removed` subsections as needed. Describe what changed for a
   reader in plain words (not the commit message). When the change documents a gligabao release, name it
   (e.g. "documents gligabao 0.24.0"). Never edit or reorder older entries.
4. Check that `VERSION` equals the newest changelog entry.

If several changes land in one working session before a commit, use one version bump for the whole commit.
Do not add an `[Unreleased]` section: the top entry must always match `VERSION`.

## Keeping in sync with gligabao

- When `../gligabao` changes behaviour, API, config or schema, update the matching page here. Mapping:
  `../gligabao/docs/<name>.md` is the page `<section>/<name>.mdx` (see `docs.json` for sections); the README sections
  feed `index`, `getting-started/*`, `api-reference/overview`, `embedding/go-library` and `operations/versioning`.
  The `evals/` section is `../gligabao/docs/evals.md` (`evals/evals`), `experiments.md` (`evals/experiments`) and
  `training/EVALS.md` (`evals/training`).
- The `training/` section is written from `../gligabao/docs/sidecar.md`, `../gligabao/docs/training/*.md` and the code under
  `internal/sidecar`, `internal/api/training/` and `sidecar/ai_train/`; it does not map one-to-one to a source page.
  Update it when training jobs, the sidecar API, the `gb_training_job_runs` table or `sidecar_url` change.
- Do not drop content when converting: every fact in the source must appear in the page.
- Link between pages with Mintlify routes (`/api-reference/remember#anchor`), never with `.md` file names.
- Link to repo files that are not on this site (`evals/README.md`, `CHANGELOG.md`) with GitHub URLs.
- A new page must be added to `navigation` in `docs.json`; a removed page must be removed from it and from all links.

## Checks before committing

- `mint dev` renders the site; `mint broken-links` must report nothing.
- Every page has `title` and `description` front matter.
