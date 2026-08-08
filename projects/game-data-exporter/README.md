# Schedule I data export tools

This directory contains two source-only C# tools for exporting data from a locally installed copy of Schedule I:

- `in-game-exporter` builds a MelonLoader DLL that writes a versioned JSON report and directly readable assets.
- `offline-asset-extractor` builds a console executable that recovers mesh geometry Unity marks as CPU-unreadable.

Compiled DLL and executable files are not published as release assets.
Users compile both projects locally and provide their own game, mod-loader, API, .NET, and AssetRipper installations.

Version `0.0.3` was built against Schedule I `0.4.6f11`, MelonLoader `0.7.3`, S1API `3.1.6`, and AssetRipper `1.3.14`.
A newer game or dependency version requires a fresh build and extraction audit.

## Requirements

- Windows x64
- A locally installed and licensed copy of Schedule I
- MelonLoader installed into Schedule I
- The IL2CPP build of S1API installed as `Mods/S1API.Il2Cpp.MelonLoader.dll`
- .NET 10 SDK
- AssetRipper `1.3.14` for the offline visual pass

Download `AssetRipper_win_x64.zip` from the [AssetRipper 1.3.14 release](https://github.com/AssetRipper/AssetRipper/releases/tag/1.3.14).
Its published SHA-256 is:

```text
808cddf66dd0357ad6b36b97de3a2aef5e3552e63af3ee0610f9a03a0378101c
```

AssetRipper is a separate third-party dependency and is not included in this repository.

## Build the in-game exporter

Run the following commands from this directory.

The DLL project reads references from the local Schedule I installation.
Provide the installation path through `NEONSCHEDULE1_GAME_DIR` or the `GameDirectory` MSBuild property.

PowerShell:

```powershell
$env:NEONSCHEDULE1_GAME_DIR = 'C:\Program Files (x86)\Steam\steamapps\common\Schedule I'
dotnet build '.\in-game-exporter\NeonSchedule1.GameDataExporter.csproj' -c Release
```

Bash on Windows:

```bash
export NEONSCHEDULE1_GAME_DIR='C:/Program Files (x86)/Steam/steamapps/common/Schedule I'
dotnet build './in-game-exporter/NeonSchedule1.GameDataExporter.csproj' -c Release
```

Equivalent one-command PowerShell build:

```powershell
dotnet build '.\in-game-exporter\NeonSchedule1.GameDataExporter.csproj' -c Release -p:GameDirectory='C:\Program Files (x86)\Steam\steamapps\common\Schedule I'
```

Equivalent one-command Bash build:

```bash
dotnet build './in-game-exporter/NeonSchedule1.GameDataExporter.csproj' -c Release -p:GameDirectory='C:/Program Files (x86)/Steam/steamapps/common/Schedule I'
```

The DLL is written to:

```text
in-game-exporter/bin/Release/net6.0/NeonSchedule1.GameDataExporter.dll
```

The build fails when MelonLoader or the IL2CPP S1API DLL cannot be found under the supplied game directory.
Game assemblies are referenced locally with `Private=false` and are not copied into the build output.

## Install and run the in-game exporter

1. Close Schedule I.
2. Remove or disable another copy of `NeonSchedule1.GameDataExporter.dll` so only one exporter handles the save-load event.
3. Copy `NeonSchedule1.GameDataExporter.dll` into the game's `Mods` directory.
4. Start Schedule I and load a save that is past character creation and the tutorial.
5. Watch `MelonLoader/Latest.log` for progress.
6. Wait for the final `Export complete` message before closing the game.

A new save is sufficient once character creation and the tutorial are complete.
The exporter does not require progression unlocks to enumerate the validated data.

By default, the exporter writes under the game process working directory:

```text
<Schedule I>/UserData/NeonSchedule1/exports
```

A normal Steam launch is expected to use the Schedule I directory as that working directory.

Set `NEONSCHEDULE1_EXPORT_OUTPUT` before starting the game to use another directory.
Steam must inherit that environment variable, so restart Steam after changing a persistent user environment variable.

Each run creates:

```text
neonschedule1-game-data-<UTC run id>.json
neonschedule1-game-data-<UTC run id>.json.sha256
neonschedule1-assets-<UTC run id>/
```

The report schema is `neonschedule1-game-data-export-1`.
Advanced world, layout, navigation, schedule, and visual data is stored in its top-level `discovery` object.
It records game, exporter, MelonLoader, and S1API versions.
The asset directory contains directly readable textures, sprites, images, and OBJ meshes plus their report metadata and hashes.

The exporter writes the following data:

- Mixing products, ingredients, effects, mixer maps, and oracle cases
- Recipes, stations, seeds, growing, packaging, additives, soils, quality, and oven transformations
- Items, prices, shops, suppliers, listings, unlocks, properties, businesses, and logistics
- People, customers, preferences, relationships, schedules, and presentations
- Map regions, locations, services, navigation, access zones, and shop positions
- Buildables, footprints, colliders, surfaces, docks, storage, interaction points, and placement data
- Mesh, material, texture, sprite, icon, and other visual references

This is an explicit versioned export schema, not an unrestricted Unity object or save dump.
It excludes player names, organization names, Steam identifiers, unrelated save state, assemblies, and decompiled code.

## Build the offline asset extractor

Publish a self-contained Windows x64 executable.

PowerShell:

```powershell
dotnet publish '.\offline-asset-extractor\NeonSchedule1.OfflineAssetExtractor.csproj' -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true -p:DebugType=None -p:DebugSymbols=false
```

Bash on Windows:

```bash
dotnet publish './offline-asset-extractor/NeonSchedule1.OfflineAssetExtractor.csproj' -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true -p:DebugType=None -p:DebugSymbols=false
```

The executable is written to:

```text
offline-asset-extractor/bin/Release/net10.0/win-x64/publish/NeonSchedule1.OfflineAssetExtractor.exe
```

The published executable is self-contained and does not require a .NET runtime on the machine where it runs.

## Run the offline asset extractor

Close Schedule I before this pass so AssetRipper reads a stable game installation.
Use the JSON report created by the DLL and a new output directory for that report.

PowerShell:

```powershell
& '.\offline-asset-extractor\bin\Release\net10.0\win-x64\publish\NeonSchedule1.OfflineAssetExtractor.exe' `
  --report 'D:\NeonSchedule1\exports\neonschedule1-game-data-<run id>.json' `
  --assetripper 'D:\Tools\AssetRipper-1.3.14\AssetRipper.GUI.Free.exe' `
  --game-data 'C:\Program Files (x86)\Steam\steamapps\common\Schedule I\Schedule I_Data' `
  --output 'D:\NeonSchedule1\offline-assets\<run id>' `
  --checkpoint 5
```

Bash on Windows:

```bash
'./offline-asset-extractor/bin/Release/net10.0/win-x64/publish/NeonSchedule1.OfflineAssetExtractor.exe' \
  --report 'D:/NeonSchedule1/exports/neonschedule1-game-data-<run id>.json' \
  --assetripper 'D:/Tools/AssetRipper-1.3.14/AssetRipper.GUI.Free.exe' \
  --game-data 'C:/Program Files (x86)/Steam/steamapps/common/Schedule I/Schedule I_Data' \
  --output 'D:/NeonSchedule1/offline-assets/<run id>' \
  --checkpoint 5
```

Command-line options:

```text
--report <report.json>                    Required DLL report
--assetripper <AssetRipper.GUI.Free.exe> Required AssetRipper executable
--game-data <Schedule I_Data>            Required Unity data directory
--output <directory>                     Required output directory
--port <0-65535>                         Optional fixed loopback port
--limit <count>                          Optional smoke-test target limit
--checkpoint <count>                     Optional manifest checkpoint interval
--no-resume                              Ignore an existing manifest
```

The extractor starts one hidden AssetRipper process on a loopback-only port and stops only that owned process.
Its manifest schema is `neonschedule1-offline-mesh-export-1`.
It writes:

```text
offline-mesh-manifest.json
offline-extractor.log
assetripper.log
meshes/*.glb
```

An interrupted run can be repeated with the same command.
Resume is allowed only when the report SHA-256 matches the existing manifest.
Before skipping a completed GLB, the extractor rechecks its byte length, `glTF` header, and SHA-256.

Exit codes are:

```text
0   All mesh signatures resolved
2   Pass completed with unresolved entries
64  Invalid command line
130 Canceled after saving a checkpoint
1   Fatal startup or extraction failure
```

Successful terminal statuses are `matched`, `matched-identical-duplicates`, and `ambiguous-variants-preserved`.
Distinct matching variants are preserved rather than guessed away.
`not-found`, `signature-mismatch`, and `error` remain unresolved and must be investigated or retried.

## Completion checks

The DLL stage is complete only when:

- The JSON report, SHA-256 file, and matching direct-asset directory exist.
- The report hash matches the sidecar file.
- The report records zero direct-asset verification errors.

The offline stage is complete only when:

- The manifest source hash matches the DLL report.
- Every CPU-unreadable report reference is represented.
- No unresolved statuses remain.
- Every GLB passes file-length, `glTF` header, and SHA-256 verification.

## Output scope

The tools convert Unity textures to PNG, readable meshes to OBJ, and matching AssetRipper meshes to GLB while producing the report and verification hashes.
Further normalization, transformation, or interpretation of the exported data is outside their scope.
Keep each report, its assets, and its hashes together when verifying an export run.

## Data and distribution boundary

Only source code and documentation for these tools belong in this repository.
Do not commit or publish:

- Compiled DLL or executable release assets
- AssetRipper binaries
- Game binaries or assemblies
- Decompiled code
- Saves or player identifiers
- Raw exporter reports
- Extracted or modified game assets

Public hosting of extracted or modified Schedule I visuals remains an open project decision.
Modifying an extracted asset does not by itself establish permission to redistribute it.
