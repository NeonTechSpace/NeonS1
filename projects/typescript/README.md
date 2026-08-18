# NeonSchedule1 TypeScript workspace

This workspace contains NeonSchedule1's calculation engine, data compiler, solver, tests, and a placeholder for the future website.
It is contributor-facing infrastructure and does not provide a public calculator.

For repository-wide orientation, read the [development overview](/docs/development.md).
For product status, read the [project README](/README.md).
The [capability status table](/docs/development.md#capability-status) distinguishes implemented calculations from partial integration and development tooling.

## Workspace packages

| Package | Responsibility | Interface status |
| --- | --- | --- |
| `@neonschedule1/core` | Versioned data schemas and deterministic calculations for mixing, customers, dealers, production, world data, and property layouts | Private library package |
| `@neonschedule1/data-compiler` | Verification and normalization of exporter acquisitions | Private command-line tool |
| `@neonschedule1/solver` | Search policies, allocation, benchmarks, precomputed artifacts, runtime queries, and verification | Private library and command-line tools |
| `@neonschedule1/web` | Future player-facing application | Manifest-only placeholder |

## Prerequisites

- Node.js `24.19.0`
- pnpm `11.x`

## Install and validate

Run commands from this directory:

```powershell
pnpm install --frozen-lockfile
pnpm check
pnpm test
pnpm build
```

These are the canonical workspace-wide type-check, test, and build commands.
Package-specific scripts are defined in each package manifest.

## Normalize an acquisition

Build the core and compiler, then normalize an exporter acquisition:

```powershell
pnpm data:normalize -- --acquisition <directory> --output <directory>
```

The compiler verifies source hashes, assets, references, schemas, and domain invariants before it writes a content-addressed normalized dataset.
If `--output` is omitted, the compiler infers a `normalized` directory next to an acquisition stored under an `acquisitions` directory.
Normalizer `0.0.39` writes the static vehicle graph layers and endpoint evidence to `world/vehicle-navigation.json`.
That document keeps native costs separate from geometric edge distances and marks layer composition and endpoint-offset traversal as unsupported evidence.

Use the [exporter setup page](/docs/exporter-development-setup.md) to create an acquisition.
Use the [exporter manual](/projects/game-data-exporter/README.md) for the full data and validation boundary.

## Solver workflows

The root workspace scripts group specialized solver work by purpose:

| Purpose | Commands |
| --- | --- |
| Search and allocation benchmarks | `pnpm solver:benchmark`, `pnpm solver:benchmark:allocation`, `pnpm solver:benchmark:joint-allocation` |
| Search verification | `pnpm solver:verify` |
| In-game recipe validation | `pnpm solver:native` |
| Corpus generation and maintenance | `pnpm solver:precompute`, `pnpm solver:precompute:refresh` |
| Corpus verification and runtime packaging | `pnpm solver:precompute:verify`, `pnpm solver:precompute:package` |
| In-game convex-collider validation | `pnpm data:validate-convex` |

These commands build their required packages before running the owning command-line tool.
Most solver commands select a normalized dataset from `.local/normalized` unless an explicit dataset path is supplied.
The [exporter manual's validation section](/projects/game-data-exporter/README.md#export-and-validation-modes) explains workflows that require a game launch.

## Generated and local outputs

Build output under `dist` is generated and ignored.
Workspace state under `.local` is ignored and includes normalized datasets, benchmarks, native-validation evidence, precomputed corpora, verification reports, and runtime packages.
Do not commit raw game exports, extracted assets, normalized local data, or generated solver artifacts.
See the [development overview](/docs/development.md#local-generated-and-publishable-files) for the repository-wide boundary.
