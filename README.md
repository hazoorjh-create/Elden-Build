<p align="center">
  <img src="BuildMaker.UI/Assets/appicon.png" width="140" alt="Elden Build icon"/>
</p>

<h1 align="center">Elden Build — Elden Ring Build Planner & Save Injector</h1>

<p align="center">
Plan a complete Elden Ring build in a wizard, preview it like the in-game equipment
screen, and forge it directly into a cloned character in your save — no Cheat Engine,
no game running, no risk to your original characters.
</p>

---

## What it does

- **4-step wizard**: pick a save & clone a character → choose gear (weapons with
  upgrades/Ashes of War/affinities, armor, talismans, spells, physick tears) →
  allocate stats with class minimums and live gear-bonus totals → preview and
  **Give Build**, which writes everything into the cloned slot, equipped and legal.
- **Starter characters**: no character to build on? Clone a bundled all-item
  "Elden Lord" of any class straight into your save.
- **Legality-aware**: upgrade caps, ash-of-war/affinity compatibility, talisman
  pouch & memory stone requirements are enforced automatically so the game
  accepts the result.
- **Community Build Hub**: browse builds with auto-rendered thumbnails, upvote,
  comment (Discord sign-in), and import any build by its `ERB-` id — no account
  needed to import. Builds can also be saved locally as plain JSON, fully offline.
- **Safety first**: every save you open is snapshotted into `SaveBackups\` next
  to the app (deduplicated, last 10 kept), plus adjacent backups before every write.

## Project layout

| Path | What |
|---|---|
| `BuildMaker.UI/` | Avalonia desktop app (wizard, hub, theming) |
| `BuildMaker.Runtime/` | Save parsing/copying, build model, CLI wrapper |
| `BuildMaker.UI/Tools/er-save-editor.exe` | Bundled save-editing CLI (modified [ER-Save-Editor](https://github.com/ClayAmore/ER-Save-Editor), MIT/Apache-2.0) |
| `supabase/` | Community hub backend: schema + one-time setup guide |
| `docs/` | Design docs (e.g. the planned live Loadout Manager) |

## Building

```
dotnet build BuildMaker.slnx
dotnet publish BuildMaker.UI/BuildMaker.UI.csproj -c Release -r win-x64 --self-contained true
```

`hub.json` (Supabase URL + anon key for the Community tab) is gitignored;
locally copy `hub.json` next to the exe, in CI it is injected from the
`HUB_JSON` repository secret. Releases are automated: push a `v*` tag and the
[release workflow](.github/workflows/release.yml) attaches a self-contained zip.

## Save-format notes (hard-won)

- The acquired-items registry (`ga_item_data`) is a sorted flat list of item ids
  packed two per 16-byte row; corrupting it makes the game hide the entire
  inventory. The bundled CLI maintains it correctly and self-repairs older damage.
- Talisman inventory handles derive from the talisman id — duplicates corrupt
  the save and are guarded against at three layers.
- Brand-new (never-played) characters have uninitialized save structures;
  builds should be injected into played characters or the bundled starters.
- The Flask of Wondrous Physick is event-flag-gated and cannot be granted; tears
  are only pre-slotted when the character demonstrably owns the flask.

## Credits & licenses

See [THIRD-PARTY-NOTICES](BuildMaker.UI/THIRD-PARTY-NOTICES.txt): ClayAmore's
ER-Save-Editor (save-editing core), Avalonia and friends (UI), community item
data & icons. Elden Ring is © FromSoftware / Bandai Namco; this project is
unaffiliated. Never take modified saves online.
