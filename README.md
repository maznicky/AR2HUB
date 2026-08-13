# AR2 Hub

Apocalypse Rising 2 tooling for the Matcha executor, built on INS-ui.

## Load

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/maznicky/AR2HUB/main/Script"))()
```

## Visuals

**Events** — ESP for most map events, grouped by faction (Military / Law /
Soviet / Civilian / Other). Green = unlooted, gray = unknown, red = looted (wiped from view).
Spawn and despawn notifications.

**Players** — a live box per player: name, distance, and their Primary /
Secondary / Melee with attachments.

**Zombies** — placeholder, not implemented.

## Combat

**No Spread** and **No Recoil**, applied to the weapon config tables.

Press **Prepare** once per session, with both toggles OFF, before using either.
It runs a single heap scan (~30s, the game freezes) and caches ~2,500 addresses.
After that both toggles apply instantly and are fully reversible. The cache
survives script reloads, so you only pay the freeze once per join.

## Keybinds

| Key | Action |
|-----|--------|
| F7  | Panic — kills the ESP and releases input |
| F8  | Site Probe sweep (also runs automatically every 60s) |

## Notes

Everything starts disabled; nothing scans until you toggle it on.
Distances are shown in metres (studs ÷ 2.75) to match Matcha's own ESP.
