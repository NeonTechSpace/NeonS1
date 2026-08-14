# Exporter development setup

This page is the short onboarding path for contributors who need to build NeonSchedule1's local game-data tools.
Read the [development overview](/docs/development.md) first if you are new to the repository.
Use the [detailed exporter manual](/projects/game-data-exporter/README.md) for complete commands, output files, validation modes, and troubleshooting rules.

## What the tools do

The in-game exporter is a MelonLoader mod that writes a versioned data report and directly readable assets.
The offline extractor uses AssetRipper to recover required mesh geometry that Unity does not expose to CPU code.
Both tools are source-only development utilities, not player-facing NeonSchedule1 releases.

## Required software

- Windows x64
- .NET 10 SDK
- A locally installed and licensed copy of *Schedule I*
- MelonLoader installed in the game directory
- The IL2CPP build of S1API installed as `Mods/S1API.Il2Cpp.MelonLoader.dll`
- AssetRipper `1.3.14` for the offline visual pass

## Setup path

1. Set `NEONSCHEDULE1_GAME_DIR` to the *Schedule I* installation directory or pass the `GameDirectory` MSBuild property
2. Build `projects/game-data-exporter/in-game-exporter/NeonSchedule1.GameDataExporter.csproj` in Release configuration
3. Copy the resulting DLL into the game's `Mods` directory
4. Start the game, load a save past the tutorial, and wait for the `Export complete` log message
5. Close the game before running the offline extractor
6. Obtain AssetRipper `1.3.14` separately and verify the published archive hash recorded in the exporter manual
7. Publish and run the offline extractor only when the export requires the offline mesh pass

Continue with [Build the in-game exporter](/projects/game-data-exporter/README.md#build-the-in-game-exporter) for exact commands.
Return to the [TypeScript workspace guide](/projects/typescript/README.md) when the acquisition is ready for normalization or solver validation.
