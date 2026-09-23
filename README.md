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
  "icon": "<svg viewBox=\"0 0 2048 2048\">…</svg>",
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
| `icon` | no | The square tile, as SVG markup. Without one the app shows the game's text mark. |
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

## Adding a game

1. Write `packs/<slug>.json`.
2. Add it to `index.json`.
3. Open a pull request.

Please only add a game you would actually run a tournament for, with a tile
you have the right to share.

## Licence

To be decided before this repo is announced. The tiles in it are Auto
Tournament's own artwork, not the games' own logos.
