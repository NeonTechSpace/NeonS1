# NeonS1

NeonS1 is an unofficial, open-source mixing calculator and recipe optimizer for *Schedule I*.
The planned calculator supports manual mixing and recipe search by effects, ingredients, cost, value, and profit.

> [!IMPORTANT]
> NeonS1 is in early development and does not have a public calculator yet.

## Planned features

- Interactive mix building
- Reverse search for desired effects
- Cost, value, and profit optimization
- Required and excluded ingredient filters
- Quick, Balanced, and Precise search modes
- Search progress and ETA ranges
- Game and data version details for every result
- Shareable recipes

## Search modes

| Mode | Intended use |
| --- | --- |
| **Quick** | Return a useful result with the shortest wait |
| **Balanced** | Explore more recipes within a moderate time budget |
| **Precise** | Use the largest live depth and work limits |

Search modes change how much work the solver performs, not how mixing is modeled.
A result is labeled exact only when the search proves it.
A limited live result contains valid candidates from completed depths and names the state, work, or time limit that stopped the search.

## Result evidence

The calculation packages preserve ingredient order and apply effect transformations explicitly.
Production solver results record the game version, dataset identity, algorithm version, source, and proof status.

Game updates can change mixing behavior or values.
Production data and solver artifacts are versioned so incompatible results are not reused.

The repository does not contain game binaries, assemblies, decompiled source code, saves, player data, or game assets.

## Development

NeonS1 is a monorepo for the website and its calculation engine.

### Exporter

See [Exporter development setup](docs/exporter-development-setup.md) for the required local tools and setup steps.

## License

Original NeonS1 source code is licensed under the [Apache License 2.0](../LICENSE).
NeonS1 is provided as is, without warranty of any kind, to the extent permitted by applicable law.
The licence contains the complete warranty disclaimer and limitation of liability.

## Disclaimer

NeonS1 is an unofficial fan project and is not affiliated with or endorsed by TVGS.
*Schedule I* and its related names and assets belong to their respective owners.
