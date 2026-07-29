# LLM Classification — ART-760 & ART-749

Notebooks and pipelines for the empirical evaluation of LLM-assisted classification in systematic literature reviews.

## Notebooks

| #  | Folder    | Version | What it does |
| --- | --------- | ------- | ------------ |
| 01 |  [`ART760/`](ART760) | 1.0.0 | Monte Carlo simulation procedure for empirical estimation of performance baselines in imbalanced datasets |
| 02 | `ART749/` | — | *(coming soon)* |

Each folder has its own README with full documentation and run instructions.

## Versioning (manual, semver-flavoured)

Versions are created manually via GitHub Releases + git tags.
No automated tooling required.

| Change type | Version bump | Example |
|---|---|---|
| Bug fix, typo, wrong calculation corrected | patch `x.x.↑` | `1.0.0 → 1.0.1` |
| New module added, output format changed, minor improvement | minor `x.↑.0` | `1.0.1 → 1.1.0` |
| Notebook restructured, breaking change in inputs/outputs | major `↑.0.0` | `1.1.0 → 2.0.0` |

Start a subproject at `1.0.0` once its notebook is functional end-to-end
and its README is written. Use `0.x.x` for work in progress.

### Pre-release suffixes (alpha / beta)

When a version is not ready for normal use, append a pre-release suffix
to the *target* version:

| Suffix | Meaning | Example |
|---|---|---|
| `-alpha.N` | Unstable. Incomplete features, outputs may be wrong, structure may still change | `2.0.0-alpha.1` |
| `-beta.N` | Feature-complete. Under testing, no new features expected, only fixes | `2.0.0-beta.1` |

Pre-releases always point to the version they will become:
`2.0.0-alpha.1 → 2.0.0-alpha.2 → 2.0.0-beta.1 → 2.0.0`.
Increment only `N` between pre-releases of the same stage, and tick
**"Set as a pre-release"** in GitHub Releases so the tag is not shown as Latest.
Skip pre-releases for patches and most minors; they only earn their keep
in majors or in minors with real risk.

### Monorepo: one version line per subproject

This repo hosts several independent notebooks (`ART760/`, `ART749/`, ...).
Versions are per subproject, not per repo.

- Tag format: `<subproject>/vX.Y.Z`, lowercase, e.g. `art760/v1.0.0`.
- Pre-releases keep the same shape: `art760/v2.0.0-beta.1`.
- Release title: `ART-760 v1.0.0`.
- Each subproject folder keeps its own `CHANGELOG.md`.
- Every release note starts with a scope line listing the folders touched:
  `Scope: ART760/ (notebook, README). ART749/ untouched.`
- Changes to shared repo files (root `README`, `LICENSE`) do not bump any
  subproject unless they change how it runs.
- The table above is the reference for which version each subproject is at.
  GitHub's "Latest" badge is repo-wide and will point to whichever
  subproject released last, so ignore it.

## License

[AGPL-3.0](https://www.gnu.org/licenses/agpl-3.0.html) —
Juan A. Marin-Garcia (UPV)
