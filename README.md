# AR2 Hub

Apocalypse Rising 2 tooling for the Matcha executor, built on INS-ui.

## Load

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/maznicky/AR2HUB/main/Script"))()
```

Safe to run more than once. A reload retires the previous instance instead of
stacking a second copy of every loop, and the Gun Mods cache survives it.

Open the menu with **P** (changeable in Settings → Interface → Menu key).

---

## Visuals

### Events
ESP for all 16 map events, grouped by faction: Military, Law, Soviet, Civilian,
Other. Per event: how many are active, the nearest one's distance, and its
zombie roster. Spawn and despawn notifications.

Green = unlooted, gray = unknown, red = looted (looted sites are wiped from
view). Sites are only called looted inside the trust range; beyond it they stay
unknown rather than guessing.

### Players
A live box per player: name, distance, and their Primary / Secondary / Melee
with attachments. Boxes appear and disappear as players join and leave.

### Zombies
Zombies are classified by what they actually carry, read from their Equipment
folder, and coloured accordingly:

| colour | meaning |
|--------|---------|
| orange | special - carries an exclusive gun, melee or backpack |
| blue   | armed - an ordinary gun, melee or bag |
| white  | normal - nothing worth taking |

Categories toggle independently: **Special Guns**, **Special Melee**,
**Special Backpacks**, **Armed**, **Normal**. All start off; `Toggle All Types`
selects or clears everything.

Special detection is a rule, not a list: any item whose name carries a `Mod1` /
`Mod2` suffix counts, which covers every modified variant automatically. The
handful of exclusives without a suffix (Patriot, SOCOM MK23, Garbage Bag, Heavy
Survey bags, Military Duffel) are matched by name.

**Items In World** lists every distinct gun and bag currently out there with
counts, specials starred and listed first. If an exclusive shows up unstarred,
the detection rules are missing it.

The label shows the zombie type, its distance, and the notable item it carries.

### Vehicles
ESP for every vehicle, coloured by **wheel condition**, which is what governs
how drivable one is:

| colour | wheels |
|--------|--------|
| red    | 0-33%  |
| orange | 34-66% |
| green  | 67-100% |

Boats are always green - they have no wheels to lose. Gray means no reading yet.
A label like `39% (3/4)` means only three of four wheels reported, so the figure
is built from a subset rather than the whole set.

Types are grouped (Civilian, Utility, Law & Rescue, Military, Boats) and all
start off; `Toggle All Types` selects or clears everything. Any vehicle type not
in the built-in list gets a toggle added automatically under **Other**.

The **Details** panel lists in-range vehicles of the types you have enabled,
nearest first, with engine, body, fuel tank, fuel and glass condition. Those are
informational only and never affect the ESP colour.

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
Settings → Bluetooth & devices → Mouse → Additional mouse settings → Pointer
Options. With it on, single-count mouse moves land at only 6% of their intended
size and the aimbot cannot track. If aim ever goes sluggish, check that first.

Aim only applies while the Roblox window has focus.

### Gun Mods
**No Spread** and **No Recoil**, applied directly to the weapon config tables.

Press **Prepare** once per join, with both toggles off. It runs a single heap
scan (~30 s, the game freezes) and caches ~2,500 addresses. After that both
toggles apply in about 2 ms and are fully reversible. The cache survives script
reloads, so you only pay the freeze once per join.

Writes are verified before they land: an address is only written if it still
holds the value that was captured. If more than a quarter of the cache no longer
matches, the toggle refuses outright and asks you to run Prepare again, rather
than writing into memory that has been reused.

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

Distances are shown in metres (studs / 2.75) to match Matcha's own ESP.

Matcha's Players handle can go stale mid-session. When it does, player-based
features idle and recover on their own instead of erroring, and the aimbot says
so rather than silently tracking only zombies. The script also waits for the
handle before loading the UI, because the menu cannot accept clicks if it is
built while the handle is down.
