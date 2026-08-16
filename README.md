# AR2 Hub

Credits to @inspecttor for the UI.

## Load

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/maznicky/AR2HUB/main/Script"))()
```

Safe to run more than once. A reload retires the previous instance instead of
stacking a second copy of every loop, and the Gun Mods cache survives it.

Open the menu with **P** (changeable in its Settings).

---

## Visuals

### Events
In very early testing so events may be inaccurate with their status'.
Has most map events, if I've missed any DM me.

Green = unlooted, gray = unknown, red = looted (looted sites are wiped from
view). Sites are only called looted inside the trust range; beyond it they stay
unknown rather than guessing.

### Players
A live box per player: name, distance, and their Primary / Secondary / Melee
with attachments.

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

The label shows the zombie type, its distance, and the notable item it carries.

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

The **Details** panel lists in-range vehicles of the types you have enabled,
nearest first, with engine, body, fuel tank, fuel and glass condition. Those are
informational only and never affect the ESP color.

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
| F7  | Panic - kills the ESP and releases input |
| F8  | Site Probe sweep (also runs automatically every 60 s) |

---

## Notes

Everything starts disabled; nothing scans until you toggle it on.

Distances are shown in meters (studs / 2.75) to match Matchas.

Matcha's Players handle can go stale mid-session (unless its the games anti-cheat i have no idea.)
When it does, player-based features idle and recover on their own instead of erroring.
