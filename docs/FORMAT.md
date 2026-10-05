# Pack format

The pack file, tiles, app icons and how the repo is laid out.

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
| `slug` | yes | `lower-case-with-hyphens`. The game's usual slug (`counter-strike-2`, `rocket-league`), so search enriches the same row instead of adding a second. |
| `name` | yes | What people call the game. |
| `igdbId` | no | Legacy. Game search used IGDB before 3.0 and uses Wikidata now. This id only links games that an older instance added through IGDB search to this pack. New packs leave it out. |
| `engine` | yes | The module that runs it. `manual-report` today. |
| `aliases` | no | Extra search terms. |
| `version` | no | Your own version string. Re-importing the same slug updates it. |
| `description` | no | One line, at most 300 characters. |
| `icon` | no | Where the tile lives, relative to this file — `../icons/<slug>.svg`. A path, never markup and never a URL. Without one the app shows the game's text mark. |
| `appIcon` | no | Where the game's own square app icon lives, relative to this file — `../app-icons/<slug>.webp`. A PNG or WebP, never a URL. Without one the small game pills show the game's initials. |
| `report.confirmation` | no | `opponent` (the other captain agrees) or `admin`. |
| `report.confirmTimeoutMin` | no | How long the opponent has before it goes to an admin. |
| `stats` | no | Up to 40 fields. `type` is `integer`, `decimal` or `text`; `scope` is `player` or `team`. |
| `account` | no | The account players need for this game, as a sign-in provider: `steam`, `epic`, `discord`, `google`, `github`, `twitch` or `oidc`. It lists the game under that account on people's connections page (Rocket League → Epic Games). Leave it out when the game is on several platforms. Needs an instance that knows the field (older 3.0 betas refuse it). |

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
anything: the slug, name, legacy IGDB id (older packs only), version, engine, a
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

