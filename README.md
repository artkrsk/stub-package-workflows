# stub-package-workflows

Reusable GitHub workflows for the `arts/*-stubs` PHPStan stub packages — the composer-only
counterpart to [`wordpress-plugin-workflows`](https://github.com/artkrsk/wordpress-plugin-workflows)
(whose plugin-oriented test/release assume pnpm, Biome, zip artifacts, and wp.org SVN —
none of which apply to a stub library).

## Workflows

| Workflow | Used by | Purpose |
|---|---|---|
| `stub-test.yml` | all stub repos | `php -l` the stub file, verify a clean tree (catches uncommitted regeneration output), run `composer test` |
| `stub-release.yml` | all stub repos | On a `v*` tag: publish a GitHub release with generated notes |
| `stub-check-updates.yml` | free-source repos only | Cron-driven upstream version check (wp.org + optional GitHub secondary); dispatches the caller's generate workflow when stubs lag |
| `stub-generate.yml` | free-source repos only | Download source, `composer generate`, changelog, branch → PR → auto-merge → tag → release |

**Free-source repos** (`elementor-stubs`, `easy-digital-downloads-stubs`) use all four.
**Paid-source repos** (`edd-software-licensing-stubs`, `edd-recurring-stubs`,
`edd-all-access-stubs`, `edd-convertkit-stubs`, `wpml-stubs`) use test + release only —
their sources can't be fetched in CI, and by deliberate decision they carry **no version
automation at all**: regeneration is a manual, local step (see each repo's README) so a
human always reviews what's being published.

## Conventions

- Tags: `vBASE` mirrors the stubbed software's version; `vBASE.N` marks a stub-content-only
  patch against the same upstream version (`v3.8.11.1` still stubs 3.8.11).
- Runner: `ubuntu-latest` by default (override with the `runner` input).
- The generate pipeline's tag push uses the default `GITHUB_TOKEN`, which intentionally
  does not re-trigger the release workflow — generate creates its own release.

See `examples/` for caller templates.
