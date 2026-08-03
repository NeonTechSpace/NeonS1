# Game data exporter

This directory contains the independent C# mod that exports the reviewed game data required by NeonSchedule1.
It is separate from the pnpm workspace and website runtime.

The exact data whitelist requires owner approval before the exporter reads or writes game data.
Raw exports, game files, assemblies, decompiled code, saves, and player identifiers must not enter the repository.

See [Exporter development setup](../../docs/exporter-development-setup.md) for the required local tools.
