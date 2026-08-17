# AR2 Hub

Credits to @inspecttor for the UI.

## Load

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/maznicky/AR2HUB/main/Script"))()
```

Open the menu with **P** (changeable in its Settings).

---

## Known Issues & things I want to improve

### Issues
"Players" sometimes isn't detected upon loading causing matcha to crash,
(I don't know if that's Matcha or the game)
Matcha crashes if you've got too much ESP on.
Map ESP works fine, it's just the keybinds having issues

### Things I'll improve
Event spawns, too much are unknown
Melee dash requires skill, it isn't perfect but there's room for improvement
(especially with the prediction)
Vehicle Flying (Currently working on, but there are limits I need to tackle)

### Things that aren't possible due to Matchas limits but works elsewhere
Undetected Hitbox Expander
Undetected Silent Aim
Vehicle auto-flip
Anti-vehicle explode
Manipulating vehicles part status'
Freezing Hydration & Energy depletion
Changeable bullet travel speed
Changeable fire-rate
Instant reload
Working Noclip
More specific Item ESP

## Visuals

### Events
In very early testing so events may be inaccurate with their status'.

Green = unlooted, gray = unknown, red = looted (looted sites are wiped from
view).

### Players
A list of players including their name, distance, and their Primary / Secondary / Melee
with attachments.

**Marked Players** - click a player's box to mark them:

| mark | effect |
|------|--------|
| Whitelist | ignored completely - the aimbot and auto melee will not touch them |
| Prioritise | red ESP so you can pick them out of a crowd |

`Clear All Marks` wipes both lists. Marks also carry over to the map dots.

**Player Corpse ESP** lists dead players.

### Zombies
Zombies are classified by what they actually carry

| color | meaning |
|--------|---------|
| orange | special - carries an exclusive gun, melee or backpack |
| blue   | armed - an ordinary gun, melee or bag |
| white  | normal - nothing worth taking |

Categories toggle independently: **Special Guns**, **Special Melee**,
**Special Backpacks**, **Armed**, **Normal**. `Toggle All Types`
selects or clears everything.

Any item whose name carries a `Mod1` / `Mod2` suffix just means
that it's a modified version of the original item. Example:
M4A1Mod1 is the "Operator" M4A1.

**Items In World** lists every distinct gun and bag currently out there with
special filters you can select.

### Vehicles
ESP for every vehicle is colored by **wheel condition**, which is what tells you
how drivable the vehicle is:

| color | wheels |
|--------|--------|
| red    | 0-33%  |
| orange | 34-66% |
| green  | 67-100% |

Boats are always green - they have no wheels to lose. Gray means no reading yet.

Types are grouped and all start disabled.
`Toggle All Types` selects or clears everything. Any vehicle type not
in the built-in list gets a toggle added automatically under **Other**.

### Map
Draws dots onto the real in-game map - open it with **M** as normal.

Players, zombies and vehicles each toggle separately, and all start off. Players
keep their mark colour (prioritised show red), and zombies and vehicles keep the
colours from their own tabs, wheel condition included.

Zombie and vehicle dots reuse those tabs' caches, so their ESP needs to be on
for the map to have anything to draw.

Everything is positioned relative to your own marker, so panning and zooming the
map are handled for you. Two extras worth knowing:

- **Show My Own Dot** - off by default, it just sits on top of your real marker.
- **Map Is Open (override)** - for when the map opens without the script
  noticing, which would otherwise leave dots sat in the middle of your screen.
- **Scale Trim** - only needed if dots drift from where things really are.

### Loot

**Loot ESP** marks searchable objects - drawers, lockers, cabinets, boxes, bags -
and the kind of loot each one can roll. Each is a *chance* of loot, not a
promise: what's actually inside doesn't exist until you open it, so whether a
given box has anything in it can't be shown. Most objects roll several tables and
only the categories you've enabled get named.

Firearms, Ammo, Medical, Backpacks, Utility, Blueprint, MeleeWeapons and Vests
start on. Consumables, Clothing, Hats, Belts, Accessories and VehicleParts start
off - soda cans and student shirts are most of the map and would bury everything
else.

**Ground Items** marks items actually lying on the floor, including anything
dropped by a player. These are real objects so the positions are exact.

| label | meaning |
|-------|---------|
| green, exact name | close enough for the game to name it (roughly 10-15 studs) |
| red / pink / white with a trailing `?` | further out - a guess from the item's shape: firearm, melee, or something else |

---

## Combat

### Aimbot
Hold **E** (rebindable) to aim at the nearest valid target.

- **Target** - Crosshair (nearest to centre) or Distance (nearest in studs).
  Both only consider targets already inside the FOV circle.
- **Hit Part** - Head or Torso.
- **Sticky Target** - holds one target until you release the key.
- **FOV** - radius in pixels, drawn on screen. Turns red when locked.
- **Distance** - ignore anything further away.
- **Smoothness** - settling time, roughly 16 ms per unit.
- **Sensitivity** - trim for the calibrated 0.002443 rad/count.
- **Prediction** - three states:
  - off: aim exactly where the target is, no lead at all
  - on, Amount 0: tracking compensation only, so it stops trailing runners
  - on, Amount above 0: that many studs of lead along the target's travel
- **Drop Prediction** - raises or lowers the aim point. With **Scale With Range**
  on it is the lift at 100 m and grows with the square of distance; with it off
  it is a flat offset at every range, which is far easier to verify.
- **Target Zombies** - testing only. Turn it off for real fights: a zombie near
  your crosshair will outrank a player.

Whitelisted players are skipped entirely.

**Requires Windows "Enhance pointer precision" to be OFF.**
If you're having issues with the Aimbot try doing what I did:
Settings → Bluetooth & devices → Mouse → Additional mouse settings → Pointer
Options. If aim ever goes sluggish, check that first.

### Gun Mods
**No Spread** and **No Recoil**, applied directly to the weapon config tables.

Press **Prepare** once per join, with both toggles off. It runs a single heap
scan (~30 s, the game freezes) and caches ~2,500 addresses. After that both
toggles apply in about 2 ms and are fully reversible. The cache survives script
reloads, so you only pay the freeze once per join.

`Refresh cache` under Advanced is not normally needed - addresses stay valid for
the whole session, including across respawns and weapon swaps.

### Melee Mods
**Auto Melee** swings only when something is actually within your weapon's reach,
read from the melee you're currently holding - so a machete swings sooner than a
pocket knife.

- **Key Mode** - Hold, Toggle or Always. Default key is **V**.
- **Targets** - zombies on, players off. Whitelisted players are never hit.
- **Only Hit What's In Front Of You** - ignores anything behind you.
- **Cooldown** - how often it's allowed to swing.
- **Keep Sprint When Swinging** - swinging normally drops you out of sprint, so
  this presses it again straight after.
- **Predict Swing** - damage doesn't land when the swing starts, it lands near
  the end of the animation. This starts the swing early based on how fast the
  target is closing on you, so runners get hit instead of missed.

Reach and cooldown themselves are the server's call, so nothing here makes your
weapon longer or faster - it just times the swings better than a human can.

### Movement
**Dash** is a short burst in the direction you're facing, default **X**, with
adjustable speed, duration and cooldown.

Worth being aware: this is the most visible thing in the script, since the server
does see you move. It's deliberately built as short bursts with a cooldown rather
than constant speed.

---

## Settings

INS-ui's own settings tab: Theme, Appearance, Interface (including the menu
key), Configs for saving named presets, and System.

Plus two script sections:

- **AR2 Hub** - Panic (stops ESP and aimbot, releases input), Unload AR2 Hub,
  and the input watchdog toggle.
- **Diagnostics** - live players/aim readout and a `Log environment report`
  button that dumps the state worth checking when something stops working.

---

## Keybinds

| Key | Action |
|-----|--------|
| P   | Open / close the menu |
| E   | Hold to aim (rebindable) |
| V   | Auto melee (rebindable, Hold / Toggle / Always) |
| X   | Dash (rebindable) |
| M   | The game's own map - Map ESP draws onto it |
| F7  | Panic - kills the ESP and releases input |
| F8  | Site Probe sweep (also runs automatically every 60 s) |

---

## Notes

Everything starts disabled; nothing scans until you toggle it on.

Distances are shown in meters (studs / 2.75) to match Matchas.

Map ESP and the exact names on ground items read the game's own interface, so
those two need **Hybrid mode** on. Everything else works without it.

Matcha's Players handle can go stale mid-session (unless its the games anti-cheat i have no idea.)
When it does, player-based features idle and recover on their own instead of erroring.
