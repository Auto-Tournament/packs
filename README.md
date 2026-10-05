<div align="center">
  <h1>Auto Tournament game packs</h1>
  <p><strong>Games for Auto Tournament, as data files you can review and import</strong></p>
  <p>
    <a href="LICENSE"><img src="https://img.shields.io/badge/License-PolyForm%20Noncommercial-blue.svg" alt="License: PolyForm Noncommercial" /></a>
    <a href="https://docs.autotournament.gg"><img src="https://img.shields.io/badge/docs-docs.autotournament.gg-blue" alt="Docs" /></a>
    <a href="https://discord.gg/n7gHYau7aW"><img src="https://img.shields.io/badge/Discord-join-5865F2?logo=discord&logoColor=white" alt="Discord" /></a>
  </p>
</div>

<br />

A game pack describes one game for [Auto Tournament](https://github.com/Auto-Tournament/auto-tournament): its name, its square tile and how a result gets reported. Nothing in a pack runs. It is data for a module the platform already has, which is what makes a pack safe to review in a pull request and import from a stranger.

A pack is for a game the platform can't watch: the teams know the result and report it (Rocket League, chess, a fighting game on a console). A game the platform watches by reading a game server, like Counter-Strike 2, needs code and is a different kind of module.

## Features

- Packs for games without a server integration, run on the `manual-report` engine
- Results confirmed by the other captain or by an admin, with a timeout
- Optional per-player stats (kills, goals, ...) for each game
- A square tile per game, drawn for this project, and the game's own app icon where one is allowed
- `index.json` and `catalog.json`, so an instance can list every pack without downloading them all

## Install

In your Auto Tournament instance, open **Modules** and browse the game packs, then import the ones you want. You can also download a pack file from `packs/` and upload it on the same page.

## Documentation

Full docs at **[docs.autotournament.gg](https://docs.autotournament.gg)**. In this repo:

- [Pack format, tiles, app icons and repo layout](docs/FORMAT.md)

## Contributing

To add a game:

1. Write `packs/<slug>.json` ([format](docs/FORMAT.md)).
2. Put its tile at `icons/<slug>.svg` and point `icon` at `../icons/<slug>.svg`.
3. If the game has a square app icon, put it at `app-icons/<slug>.webp` (128 × 128, at most 25 KB), point `appIcon` at `../app-icons/<slug>.webp`, and add a row to `app-icons/SOURCES.md`.
4. Add the game to `index.json` and to `packs` in `catalog.json`, with `icon` as `icons/<slug>.svg` (and `appIcon` as `app-icons/<slug>.webp`).
5. Open a pull request.

Only add a game you would actually run a tournament for, with a tile you drew or have the right to share. Before your first pull request is merged you'll be asked to sign the [Contributor License Agreement](CLA.md).

## Sponsors

Auto Tournament is built by one person. A sponsorship pays for development and test servers: [GitHub Sponsors](https://github.com/sponsors/sivert-io) or [Ko-fi](https://ko-fi.com/sivert).

<!-- sponsors:start -->
<!-- sponsors:end -->

## License

Everything here, pack files and tiles, is licensed under the [PolyForm Noncommercial License 1.0.0](LICENSE). Copyright (c) 2026 Sivert Gullberg Hansen. Free for non-commercial use; a Platform license covers the packs used with it, see [pricing](https://autotournament.gg/pricing). Packs published here before 24 September 2026 were MIT and stay MIT.
