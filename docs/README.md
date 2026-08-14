# NeonSchedule1

NeonSchedule1 is an unofficial open-source project for building an accurate *Schedule I* mixing calculator and recipe optimizer.
It is intended for players who want to explore recipes by ingredients, effects, cost, value, and profit.

## Current availability

> [!IMPORTANT]
> NeonSchedule1 does not have a public website or player-ready calculator yet.

The repository contains substantial working calculation, search, data, and validation code for contributors.
The web workspace is currently a placeholder with no application interface.
Using the implemented tools requires a source checkout and local development setup.

## What currently exists

- Working code that calculates mixes and searches for recipes
- Supporting calculations for customers, dealers, production, inventory, travel, and property planning
- Local tools that collect, check, and prepare game data for those calculations

These capabilities are development infrastructure and libraries.
They are not exposed through a public player interface.
The [development overview](/docs/development.md#capability-status) records their current scope and limits.

## Planned player features

- Interactive mix building
- Reverse search for desired effects
- Ingredient and effect requirements and exclusions
- Cost, value, profit, and recipe-length filters
- Quick, Balanced, and Precise search modes
- Clear game-version and data-version details
- Shareable recipes

## Choose a path

- [Understand the repository and start contributing](/docs/development.md)
- [Develop or validate the calculation engine](/projects/typescript/README.md)
- [Set up the game-data export tools](/docs/exporter-development-setup.md)
- [Read the detailed exporter manual](/projects/game-data-exporter/README.md)

## Data boundary

The public repository contains original source code and documentation only.
Do not commit or publish game binaries, assemblies, decompiled code, saves, player data, raw exports, or extracted game assets.
The [development overview](/docs/development.md#local-generated-and-publishable-files) explains which development outputs stay local.

## License

Original NeonSchedule1 source code is licensed under the [Apache License 2.0](/LICENSE).
NeonSchedule1 is provided as is, without warranty of any kind, to the extent permitted by applicable law.
The license contains the complete warranty disclaimer and limitation of liability.

## Disclaimer

NeonSchedule1 is an unofficial fan project and is not affiliated with or endorsed by TVGS.
*Schedule I* and its related names and assets belong to their respective owners.
