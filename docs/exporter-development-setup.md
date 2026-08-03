# Exporter development setup

NeonSchedule1 includes an in-game data exporter and an offline asset extractor.
The in-game exporter is a MelonLoader mod that targets `net6.0`.
The offline asset extractor targets `net10.0` and uses AssetRipper for mesh geometry that Unity does not expose to CPU code.

## Required software

- .NET 10 SDK
- Schedule I installed locally
- MelonLoader installed in the Schedule I directory
- The IL2CPP build of S1API installed as `Mods/S1API.Il2Cpp.MelonLoader.dll`
- AssetRipper 1.3.14 for the offline visual pass

## Setup

1. Install .NET 10 or extract its portable SDK into an ignored local directory
2. Set `NEONSCHEDULE1_GAME_DIR` to the Schedule I directory or pass the `GameDirectory` MSBuild property
3. Build `projects/game-data-exporter/in-game-exporter/NeonSchedule1.GameDataExporter.csproj` in Release configuration
4. Copy `NeonSchedule1.GameDataExporter.dll` into the game's `Mods` directory
5. Start Schedule I, load a save past the tutorial, and wait for the `Export complete` log message
6. Close Schedule I before running the offline extractor
7. Obtain AssetRipper 1.3.14 separately and verify the ZIP SHA-256 recorded in the exporter README
8. Publish `projects/game-data-exporter/offline-asset-extractor/NeonSchedule1.OfflineAssetExtractor.csproj` as a self-contained Windows x64 executable

See [Schedule I data export tools](../projects/game-data-exporter/README.md) for build commands, output files, verification rules, and data boundaries.
