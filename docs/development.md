# NeonSchedule1 development overview

This page explains what contributors can use today, what each repository area owns, and which parts still need a player-facing product.
For a prerequisite-free introduction, current availability, and planned player features, start with the [project README](/README.md).

## Current development state

NeonSchedule1 has a substantial calculation backend, data pipeline, solver, and validation toolchain.
It does not have a website, installable calculator, published package, or supported player workflow.

In this document, **implemented** means that repository code and tests exist for the named behavior.
It does not mean that players can use that behavior through a public interface.
**Partial** means that useful code exists but still depends on caller-provided state, local game data, further integration, or a future interface.
**Tooling** means that the code supports development or validation rather than direct player use.
**Planned** means that repository ownership exists but the named product capability has not been implemented.

## What contributors can run

After installing the declared Node.js and pnpm versions, contributors can install dependencies, type-check, test, and build the TypeScript workspace without launching the game.
The tests use repository fixtures and exercise the calculation, normalization, solver, allocation, routing, and validation boundaries.

Real *Schedule I* data is not included in the repository.
Creating a real dataset requires a locally installed and licensed copy of the game plus the exporter prerequisites.
Normalization, realistic benchmarks, native comparison, corpus generation, and runtime artifact verification require a compatible local acquisition or normalized dataset.

There is no meaningful web application to run yet.
The web workspace currently contains only its package manifest.

## Capability status

| Area | Status | Implemented repository capability | Current boundary |
| --- | --- | --- | --- |
| Mixing and recipes | Implemented | Ordered ingredient application, effect transformation, standard and seed-derived rule profiles, recipe enumeration, reverse search, ingredient and effect constraints, and deterministic ranking | Private calculation code with no player interface |
| Search | Implemented | Quick, Balanced, Precise, and exhaustive policies, bounded live fallback, completed-depth evidence, precomputed recipe corpora, indexes, and runtime queries | Requires normalized data and is not hosted or published |
| Customers | Partial | Demand, enjoyment, offer evaluation, recipe recommendation, customer selection, and shared-budget allocation | Requires explicit customer state and has no save synchronization |
| Dealers and relationships | Partial | Relationship graphs, person eligibility, dealer assignment, customer and dealer joint allocation, contract-time travel estimates, and worst-case regional travel feasibility | Uses caller-provided live state and straight-line travel evidence rather than route simulation |
| Production and inventory | Partial | Growing, packaging, additives, brick pressing, equipment needs, production time and cost, finished-recipe planning, inventory shortages, purchases, property transfers, capacity-limited transfer arrival timing, selected shopping attribution by property, one-property input readiness, elapsed lifecycle composition through a completed sale, realized profit per game minute, and grow-additive comparisons against a no-additive baseline | Exact lifecycle timing requires caller-supplied movement, exclusive sequential execution, and completed-sale evidence. Exact realized profit requires complete sale revenue and explicit cost treatment. Exact additive ranking requires comparable plans and exact realized profit for each ranked selection. Persistent inventory and live execution remain unavailable |
| Shopping and movement | Partial | Shop options, seller allocation, multi-stop shopping routes, split pickups, capacity-limited return trips, schedule waiting, and remote delivery | Route claims require movement evidence supplied for the relevant player or vehicle |
| People and world | Partial | People, relationships, schedules, map projection, shops, properties, services, access zones, and employee navigation data | Normalized data and calculations exist, but there is no live map or game connection |
| Property blueprints | Partial | Placement validation, construction order, item cost, collision, access, temperature, production capacity, exact installed-pot sprinkler coverage and cycle timing, schedules, transfers, routing inputs, logistics, assigned employee service time, work priority, movement rules, endpoint reachability, and task-internal route candidates | Sprinkler coverage assumes blueprint placements remain fixed during an application and does not simulate mutable pot moisture, exact employee travel depends on current position, task selection, runtime state, and unselected endpoints, and no blueprint editor or public browser exists |
| Data pipeline | Tooling | Hash verification, schema validation, integrity checks, normalization, stable dataset identity, and corruption checks | Requires a local exporter acquisition |
| Game-data tools | Tooling | In-game export, native recipe comparison, convex-collider validation, direct asset export, and offline mesh extraction | Windows-only local development tooling that requires the game and third-party prerequisites |
| Website | Planned | Package ownership is reserved in the workspace | Placeholder only, with no source application or public deployment |

This table describes public repository evidence, not a release promise.
The source and tests remain authoritative when a capability changes.

## Repository areas

| Area | Responsibility | Status |
| --- | --- | --- |
| [`projects/typescript/packages/core`](/projects/typescript/packages/core) | Versioned data contracts and deterministic game calculations | Implemented private workspace package |
| [`projects/typescript/packages/data-compiler`](/projects/typescript/packages/data-compiler) | Verification and normalization of local exporter output | Implemented command-line development tool |
| [`projects/typescript/packages/solver`](/projects/typescript/packages/solver) | Recipe search, allocation, precomputation, benchmarking, runtime artifacts, and verification | Implemented private libraries and command-line tools |
| [`projects/typescript/web`](/projects/typescript/web) | Future player-facing website | Placeholder package only |
| [`projects/game-data-exporter`](/projects/game-data-exporter) | In-game acquisition, native validation, and offline mesh extraction | Implemented source-only local tools |

The [TypeScript workspace guide](/projects/typescript/README.md) owns package commands and solver workflows.
The [exporter manual](/projects/game-data-exporter/README.md) owns acquisition and in-game validation procedures.

## Data flow

1. The in-game exporter writes a versioned report and directly readable assets from a locally installed copy of *Schedule I*
2. The offline extractor can recover required mesh geometry that Unity does not expose to the in-game tool
3. The data compiler verifies the acquisition and writes a normalized dataset identified by its content hash
4. The core package applies deterministic calculations to normalized data and explicit caller-provided state
5. The solver searches recipes, allocates constrained resources, creates precomputed corpora, and verifies results
6. A future web application can turn those operations into player workflows

Each boundary uses versioned schemas and stable identities so incompatible game data, normalized data, and solver artifacts are not silently combined.

## Architecture and ownership

The exporter owns acquisition from the installed game.
It emits an explicit report instead of exposing an unrestricted object or save dump.

The data compiler is the trust boundary between local acquisition and portable calculations.
It verifies hashes, checks references and domain invariants, removes unsupported runtime state, and writes canonical normalized documents.

The core package owns data schemas and calculations that do not need file-system or game-process access.
Callers must provide mutable facts such as inventory, progression, current relationships, positions, timing, and ownership when a calculation needs them.

The solver owns search budgets, precomputation, allocation orchestration, benchmarks, verification, and runtime artifact loading.
Bounded results report their limits instead of presenting unfinished search as proof of the best possible result.

The web package is intended to consume these boundaries later.
It does not currently define product architecture, deployment, accounts, persistence, or live save synchronization.

## Compatibility

Exporter version `0.0.22` targets *Schedule I* `0.4.6f13`, MelonLoader `0.7.3`, S1API `3.1.6`, and AssetRipper `1.3.14`.
A newer game or dependency version requires a new build and extraction audit.

Normalizer version `0.0.37` defines the current normalized output contract.
Normalized datasets record the game version, normalizer version, source hashes, file hashes, counts, and one dataset identity.
Solver artifacts bind to compatible dataset and algorithm identities.
No game acquisition or normalized production dataset is committed to this repository.

## TypeScript development

The TypeScript workspace requires Node.js `24.19.0` and pnpm `11.x`.

Run the canonical checks from `projects/typescript`:

```powershell
pnpm install --frozen-lockfile
pnpm check
pnpm test
pnpm build
```

`pnpm check` runs the package TypeScript checks.
`pnpm test` runs the workspace Vitest suite.
`pnpm build` builds each package that defines a build script.
The [TypeScript workspace guide](/projects/typescript/README.md) explains normalization, benchmarks, native validation, and precomputation.

## Exporter development

Exporter development requires Windows, a local licensed game installation, .NET 10, MelonLoader, S1API, and AssetRipper for the offline visual pass.
Start with the concise [exporter setup page](/docs/exporter-development-setup.md).
Use the [detailed exporter manual](/projects/game-data-exporter/README.md) for build commands, output verification, native validation, and recovery procedures.

## Local, generated, and publishable files

Original NeonSchedule1 source code, tests, and documentation are the repository's publishable content.
Publication of another artifact requires a separate review of its source, contents, dependencies, and game-data boundary.

The TypeScript workspace ignores `.local`, `node_modules`, `dist`, and TypeScript build-information files.
The exporter workspace ignores `.local`, `bin`, and `obj`.
Build directories are generated and can be recreated from source.

Raw acquisitions, extracted assets, normalized local datasets, benchmark reports, native-validation evidence, precomputed corpora, verification reports, and runtime packages are local development outputs.
They are not approved public artifacts merely because a tool can generate them.

Never publish game binaries, assemblies, decompiled code, saves, player identifiers, raw exporter reports, or extracted game assets from this repository.
The [project README](/README.md#data-boundary) states the public data boundary.

## Documentation paths

- Product purpose, availability, and planned player features belong in the [project README](/README.md)
- Contributor status, ownership, architecture, and data flow belong on this page
- TypeScript package commands and solver workflows belong in the [TypeScript workspace guide](/projects/typescript/README.md)
- Exporter onboarding belongs in the [exporter setup page](/docs/exporter-development-setup.md)
- Export, extraction, native validation, and completion procedures belong in the [exporter manual](/projects/game-data-exporter/README.md)
