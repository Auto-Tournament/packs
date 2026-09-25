# Auto Tournament game packs

Games for [Auto Tournament](https://github.com/Auto-Tournament/auto-tournament),
as files.

<div align="center">

### Sponsor Auto Tournament

Running tournaments or LANs with Auto Tournament? Your organisation can keep it growing.
Auto Tournament is built and maintained by one person — sponsorships pay for development, test servers and infrastructure.

[![Sponsor on GitHub](https://img.shields.io/badge/Sponsor-GitHub-ea4aaa?logo=githubsponsors&logoColor=white)](https://github.com/sponsors/sivert-io)
[![Support on Ko-fi](https://img.shields.io/badge/Support-Ko--fi-ff5e5b?logo=kofi&logoColor=white)](https://ko-fi.com/sivert)
[![Become a sponsor](https://img.shields.io/badge/Become%20a%20sponsor-Discord-5865F2?logo=discord&logoColor=white)](https://discord.gg/n7gHYau7aW)

Using it for a business, paid events or hosting? That needs a commercial licence → [Licensing](https://docs.autotournament.gg/reference/licensing)

</div>

A **game pack** describes one game: its name, its square tile, and how a
result gets reported. Nothing in a pack executes — it is data, and it runs on
a module the platform already has. That is what makes a pack safe to hand
around, review in a pull request, and import from a stranger.

An admin imports one from **Modules** in their instance, either by browsing
this repo from inside the app or by downloading a file and uploading it.

## What a pack can and cannot do

A pack works for a game **the platform cannot watch** — the teams know the
result and report it. Rocket League, chess, a fighting game on a console in
somebody's living room.

A game the platform *watches*, reading rounds off a game server the way it
does for Counter-Strike 2, is not a pack. That needs code, and code is a
different kind of module.

## The format

```json
{
  "schema": 1,
  "slug": "call-of-duty",
  "name": "Call of Duty",
  "engine": "manual-report",
  "aliases": ["cod"],
  "version": "1.0.0",
  "description": "One line. Shown on the Modules page.",
  "icon": "../icons/call-of-duty.svg",
  "report": { "confirmation": "opponent", "confirmTimeoutMin": 60 },
  "stats": [
    { "key": "kills", "label": "Kills", "type": "integer", "scope": "player" }
  ]
}
```

| Field | Required | What it is |
| --- | --- | --- |
| `schema` | yes | `1`. An instance refuses a schema it does not read. |
| `slug` | yes | `lower-case-with-hyphens`. An IGDB slug where one exists, so search enriches the same row instead of adding a second. |
| `name` | yes | What people call the game. |
| `igdbId` | no | The game's numeric IGDB id — the "IGDB ID" on its igdb.com page. A game a player picks from IGDB search is matched to this pack by it first, so the pill draws this pack's app icon even where IGDB's slug is not the pack's (`trackmania--2`, `deadlock--2`). Leave it out for a franchise pack or a game IGDB has no single entry for. Needs an instance that knows the field (older 3.0 betas refuse it). |
| `engine` | yes | The module that runs it. `manual-report` today. |
| `aliases` | no | Extra search terms. |
| `version` | no | Your own version string. Re-importing the same slug updates it. |
| `description` | no | One line, at most 300 characters. |
| `icon` | no | Where the tile lives, relative to this file — `../icons/<slug>.svg`. A path, never markup and never a URL. Without one the app shows the game's text mark. |
| `appIcon` | no | Where the game's own square app icon lives, relative to this file — `../app-icons/<slug>.webp`. A PNG or WebP, never a URL. Without one the small game pills fall back to the IGDB cover, then the game's initials. |
| `report.confirmation` | no | `opponent` (the other captain agrees) or `admin`. |
| `report.confirmTimeoutMin` | no | How long the opponent has before it goes to an admin. |
| `stats` | no | Up to 40 fields. `type` is `integer`, `decimal` or `text`; `scope` is `player` or `team`. |

Unknown fields are refused rather than ignored, so a pack written for a newer
schema fails loudly instead of quietly doing less than it says.

## Tiles

A tile is a square SVG, full bleed, drawn in the Auto Tournament palette —
every fill written as `fill="var(--at-ember, #ff6a3d)"` with a hex fallback,
so the tile follows whatever theme the instance is using and still looks right
opened on its own. The variables are listed in the platform repo under
`brand/modules/README.md`.

Tiles are checked on import against a strict allowlist of elements and
attributes, and **rejected, not stripped**, if they contain anything else. A
tile is inlined into the admin's page, so a `<script>`, an `onload=`, a
`<foreignObject>` or a reference to another host is a refusal with a reason.
Run your SVG through SVGO first; the platform repo has the config.

## App icons

The tile is our art. The app icon is the game's own: the square icon players
know it by, the one on their phone, desktop or launcher. The app draws it in
the 20 px game pills ("What do you play?", a profile's games), where a player
has to pick out their game at a glance and a wide wordmark shrinks to a smear.

An app icon is a **square PNG or WebP, 128 × 128, at most 25 KB**. The
platform checks the bytes, not the file name — a file that is not a PNG or
WebP, is not square, is outside 32–512 px or is over 25 KB is refused on
import. Never a slice of a wordmark: if a game has no square icon, leave
`appIcon` out.

Record where every icon came from in [`app-icons/SOURCES.md`](app-icons/SOURCES.md).
Game icons are trademarks of their owners and are used only to identify the
game.

## Layout

    catalog.json            the game catalog: every pack, and signed code modules
    index.json              every pack in this repo (read by 3.0 beta instances)
    packs/<slug>.json       one game
    icons/<slug>.svg        its tile
    app-icons/<slug>.webp   its square app icon, and SOURCES.md

A pack names its tile rather than carrying it, so the JSON stays something a
person can read and a reviewer can diff.

### index.json

Each entry repeats just enough for the app to draw a card before it downloads
anything: the slug, name, IGDB id (when the pack has one), version, engine, a
one-line description, the pack's path, the tile's path *relative to this file*
(`icons/<slug>.svg`, without the `../`), and the app icon's the same way
(`app-icons/<slug>.webp`).

```json
{
  "schema": 1,
  "packs": [
    {
      "slug": "call-of-duty",
      "name": "Call of Duty",
      "version": "1.0.0",
      "engine": "manual-report",
      "description": "One line.",
      "file": "packs/call-of-duty.json",
      "icon": "icons/call-of-duty.svg",
      "appIcon": "app-icons/call-of-duty.webp"
    }
  ]
}
```

### catalog.json

What Auto Tournament 3.0 reads: the one list an admin installs games from.
`packs` is exactly `index.json`'s list — keep the two the same. `modules`
lists code modules (CS2 first) and their releases:

```json
{
  "schema": 1,
  "packs": [ … ],
  "modules": [
    {
      "id": "cs2",
      "name": "Counter-Strike 2",
      "description": "One line.",
      "icon": "icons/cs2.svg",
      "releases": [
        {
          "version": "3.0.0",
          "serverApi": "^0.1.0",
          "clientApi": "^0.2.0",
          "url": "https://github.com/Auto-Tournament/auto-tournament/releases/download/module-cs2-v3.0.0/cs2-3.0.0.atmod",
          "sha256": "…",
          "size": 1234567
        }
      ]
    }
  ]
}
```

A module entry is pasted from the `catalog-entry.json` its release publishes.
That entry names its tile `icons/<id>.svg`; the release attaches the tile as
`<id>.svg` — add it here at that path.
Nothing here is trusted because it is listed: an instance installs a code
module only if the release is signed by a key compiled into the platform, and
downloads only from `https://github.com/Auto-Tournament/`.

## Adding a game

1. Write `packs/<slug>.json`, with the game's `igdbId` from its igdb.com page when IGDB has one entry for it.
2. Put its tile at `icons/<slug>.svg` and point `icon` at `../icons/<slug>.svg`.
3. If the game has a square app icon, put it at `app-icons/<slug>.webp` (128 × 128, at most 25 KB), point `appIcon` at `../app-icons/<slug>.webp`, and add a row to `app-icons/SOURCES.md`.
4. Add the game to `index.json` and to `packs` in `catalog.json`, with its `igdbId`, `icon` as `icons/<slug>.svg` (and `appIcon` as `app-icons/<slug>.webp`).
5. Open a pull request.

Please only add a game you would actually run a tournament for, with a tile
you have the right to share.

## Sponsors

Your logo here — [sponsor Auto Tournament](https://discord.gg/n7gHYau7aW) to be listed.

## Licence

[PolyForm Noncommercial 1.0.0](LICENSE), for everything in this repository: the
pack files and the tiles. The same licence as the platform: free to use, change
and share for non-commercial purposes; commercial use needs a licence from the
author. See [Licensing and commercial use](https://docs.autotournament.gg/reference/licensing).

Packs published here before this change were MIT and stay MIT.

The tiles are Auto Tournament's own artwork — drawn for this project, not the
games' own logos or key art. Adding a game means adding a tile you drew or
have the right to share, not one you found.

Before your first pull request is merged you'll be asked to sign the
[Contributor License Agreement](CLA.md) with a comment on the pull request.
