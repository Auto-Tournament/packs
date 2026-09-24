# Auto Tournament game packs

Games for [Auto Tournament](https://github.com/Auto-Tournament/auto-tournament),
as files.

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
| `engine` | yes | The module that runs it. `manual-report` today. |
| `aliases` | no | Extra search terms. |
| `version` | no | Your own version string. Re-importing the same slug updates it. |
| `description` | no | One line, at most 300 characters. |
| `icon` | no | Where the tile lives, relative to this file — `../icons/<slug>.svg`. A path, never markup and never a URL. Without one the app shows the game's text mark. |
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

## Layout

    index.json              every pack in this repo
    packs/<slug>.json       one game
    icons/<slug>.svg        its tile

A pack names its tile rather than carrying it, so the JSON stays something a
person can read and a reviewer can diff.

### index.json

Each entry repeats just enough for the app to draw a card before it downloads
anything: the slug, name, version, engine, a one-line description, the pack's
path, and the tile's path *relative to this file* (`icons/<slug>.svg`, without
the `../`).

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
      "icon": "icons/call-of-duty.svg"
    }
  ]
}
```

## Adding a game

1. Write `packs/<slug>.json`.
2. Put its tile at `icons/<slug>.svg` and point `icon` at `../icons/<slug>.svg`.
3. Add the game to `index.json`, with `icon` as `icons/<slug>.svg`.
4. Open a pull request.

Please only add a game you would actually run a tournament for, with a tile
you have the right to share.

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
