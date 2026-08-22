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
(I don't know if that's Matcha, me or the game)
Map ESP works fine, it's just the keybinds having issues

**Mouse 4 and Mouse 5 cannot be used for anything.** Matcha reads them as never
pressed - 0 out of 86 samples while physically held - so any feature bound to
them silently never fires. They aren't offered in the trigger lists any more.

**Keys drop while you're driving.** Holding W and shift stops the keyboard
reporting a third key: V managed 3% of reads, R managed 0%. That's the keyboard,
not the script. Auto Melee bridges the gaps with a hold latch; Ram doesn't, so
Ram is unreliable while driving.

**Too much ESP at once still costs frames.** Zombie ESP used to spend 36% of the
client on its own (it projected every tracked zombie every frame, ~94 of them).
That's fixed - it now picks the nearest handful a few times a second and draws
only those - but Ground Items is still the heaviest thing in the script.

**Long flights make the game load slower.** Crossing the map faster than Roblox
streams it leaves the loading permanently behind: buildings, trees and sound
effects start appearing late, and it gets worse the longer the session runs
rather than recovering. It's also why arriving somewhere too fast drops you
through the ground. Rejoining resets it. This isn't a bug I can fix - anything
that moves you faster than the game streams will do it - so shorter hops with a
few seconds on the ground between them are the only real mitigation.

### Things I'll improve
Event spawns, too much are unknown
Melee dash requires skill, it isn't perfect but there's room for improvement
(especially with the prediction)
Vehicle Flying (Currently working on, but there are limits I need to tackle)
Landing softness for autopilot - the setting didn't do what it should
Whether ground time resets the engine drain (tested: engine damage never comes
back, but I haven't proven the drain rate itself doesn't reset)
Ram against **players** specifically - zombies die cleanly, players don't always
take damage from the same setup, and I don't yet know whether that's purely the
vehicle's hitbox or something server-side rejecting it

### Things that aren't possible due to Matchas limits but works elsewhere
Undetected Hitbox Expander
Undetected Silent Aim
Properly rotating a vehicle upright (angular velocity can't be written - the
write is rejected. Auto Flip works around it with a vertical shove, which isn't
the same thing and doesn't always take)
Anti-vehicle explode
Manipulating vehicles part status' (SetAttribute is silently ignored, and
Anchored isn't a property Matcha knows)
Vehicle repair (the repair prompts need fireproximityprompt, which is missing)
Freezing Hydration & Energy depletion
Changeable bullet travel speed
Changeable fire-rate
Instant reload
Working Noclip
More specific Item ESP

---

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
| Whitelist | ignored completely - the aimbot, auto melee and ram will not touch them |
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

Distance defaults to 120 m and only the nearest 24 get markers. Both of those
are deliberate - see the performance note in Issues.

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
**This is the only feature that needs Hybrid mode**, because it reads the game's
own map interface to know where to draw.

Players, zombies and vehicles each toggle separately, and all start off. Players
keep their mark colour (prioritised show red), and zombies and vehicles keep the
colours from their own tabs, wheel condition included.

Zombie and vehicle dots reuse those tabs' caches, so their ESP needs to be on
for the map to have anything to draw.

Everything is positioned relative to your own marker, so panning and zooming the
map are handled for you. Three extras worth knowing:

- **Show My Own Dot** - off by default, it just sits on top of your real marker.
- **Map Is Open (override)** - for when the map opens without the script
  noticing, which would otherwise leave dots sat in the middle of your screen.
- **Scale Trim** - only needed if dots drift from where things really are.

### Loot
**Ground Items** marks items actually lying on the floor, including anything
dropped by a player. These are real world objects, so the positions are exact.

Labels come from the item's own model, not its name. A gun reads "Firearm", a
knife reads "Melee", and items whose mesh carries a real name (Lantern,
Flashlight, Energy Drink Yellow) name themselves. The game gives no reliable way
to tie a name to a specific object on the floor, so nothing pretends otherwise.

Categories: Firearms, Ammo, Backpacks, Melees, Accessories, VehicleParts,
Consumables, Other. Firearms and Melees are read reliably from the model; ammo,
bags and clothing mostly look identical from outside and land in Other.

Container/searchable-object ESP was removed - what's inside a container doesn't
exist until you open it, so it could only ever show odds, not contents.

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

- **Key Mode** - Hold or Toggle. Default key is **V**.
- **Trigger** - Key or Right Mouse. Mouse 4/5 aren't offered; Matcha can't read
  them.
- **Targets** - players and zombies, both on. Whitelisted players are never hit.
- **Only Hit What's In Front Of You** - ignores anything behind you.

Cooldown, sprint keeping and swing prediction still run at the values that
tested well; their sliders were removed to keep the tab readable.

**Chase** dashes you at whatever you're about to swing at, so the hit lands as
you arrive. It aims where the target *will* be, eases off if the body starts
leaning, and won't dash into someone already running at you - meeting in the
middle is what used to send you past them.

Reach and cooldown themselves are the server's call, so nothing here makes your
weapon longer or faster - it just times the swings better than a human can.

---

## Vehicle Mods

Everything here moves the **vehicle**, not you, by writing its velocity. That's
the one thing this game accepts: a chassis moved 17 studs with 0.13 studs of
correction afterwards, so the server takes it.

**Time in the air is what costs you.** The engine starts depleting about a
second after takeoff and drains roughly 2% per second until you land. That
damage never comes back. Distance and altitude are free - a 4,380 stud hop on a
15% engine survived because it only took two seconds.

### Drive
- **Fly** - hold the trigger and the vehicle goes wherever the camera points.
- **Fly Speed** - no ceiling worth worrying about; 1000 measured a peak of 1108
  studs/s and covered 2,344 studs in a 6 second flight.
- **Trigger / Fly Key** - Key (default **G**), Right Mouse or Space.
- **Auto Flip** - lifts a rolled vehicle back onto its wheels. Angular velocity
  can't be written, so a vertical shove is the only way to undo a flip. It only
  fires when the vehicle has been well past its side for most of a second, so
  hills don't set it off.

Engine protection is always on now rather than being a toggle - it refuses trips
the vehicle can't survive and lands early if the engine runs out mid-flight.
Turning it off only ever meant volunteering to lose the vehicle.

### Ram
Drives the vehicle along the ground at whatever you're hunting. Never leaves the
floor, so there's **no engine drain at all** - the only cost is the crash damage
from the impacts themselves.

**How well this works depends on the vehicle far more than on any setting.** The
hitbox shape and ride height decide whether contact registers at all. The rubber
dinghy is the best I've found; other vehicles barely connect on identical
settings. If something won't land a hit, change vehicle before touching sliders.

Two things worth knowing, both learned the hard way:

- **Speed above ~150 studs/s does nothing.** The vehicle covers more ground per
  physics step than a person is wide, so it passes straight through them and no
  collision is ever registered. 100 is the default for that reason - fast looks
  more violent and does nothing at all. This was behind every ram that "worked"
  but dealt no damage.
- **Engine power is irrelevant.** A vehicle with no fuel left still ran zombies
  over, so it's the velocity that damages, not the throttle.

Settings are **Range**, **Ground Grip** (how hard bumps get pushed back down -
around 60 is what actually connects) and **Ram Speed**. There used to be Ground
and Air modes; Air climbed and dove and reliably did nothing, and Ground was
this without the steering, so there's just the one mode now.

Targets zombies by default, players optional, whitelisted players never.
Trigger defaults to the **R** key - though see the note about keys dropping while
you drive.

Zombies die to a clean pass. Players are less consistent, and I haven't pinned
down why yet - see Things I'll improve.

### Autopilot
Flies the vehicle to a destination and lands it: climb, cross, descend, stop.

- **Travel Height** - how high it crosses (default 280, above every recorded
  destination, so it clears terrain).
- **Travel Speed** - sets the whole profile at once; braking tracks it.

Long trips may still need **manual stops**, for two reasons: the engine drains
the whole time you're airborne, and arriving somewhere the map hasn't streamed
in yet drops the vehicle through the ground.

**Destinations** are grouped by island - Halsey, Faris and Mackinaw - and each
one was recorded by standing on the spot, which is how the landing knows what
height to stop at. There's no way to measure ground height in this game, so a
destination picked off a map wouldn't work.

### Flight Log
Records each flight: time airborne, distance, peak speed, engine before and
after, how long you were grounded first, and where the engine first took damage.
`Print Log` dumps every run to the console. It's there to answer how far you can
get per engine point rather than guessing.

---

## Character

**Dash** is a short burst in the direction you're facing, default **X**, with
adjustable speed, duration and cooldown.

**Infinite Jump** - hold Space to keep jumping.

Both are real movement the server sees, so they're the most visible things in
the script. Dash is deliberately built as short bursts with a cooldown rather
than constant speed.

---

## Settings

INS-ui's own settings tab: Theme, Appearance, Interface (including the menu
key), Configs for saving named presets, and System.

Plus two script sections:

- **AR2 Hub** - Panic (stops ESP and aimbot, releases input), Unload AR2 Hub,
  and the input watchdog toggle.
- **Diagnostics** - live players/aim readout and a `Log environment report`
  button that dumps the state worth checking when something stops working,
  including scheduler health (1.00 = keeping up).

---

## Keybinds

| Key | Action |
|-----|--------|
| P   | Open / close the menu |
| E   | Hold to aim (rebindable) |
| V   | Auto melee (rebindable, Hold / Toggle) |
| X   | Dash (rebindable) |
| G   | Vehicle fly (rebindable) |
| R   | Vehicle ram (rebindable) |
| M   | The game's own map - Map ESP draws onto it |
| F7  | Panic - kills the ESP and releases input |
| F8  | Site Probe sweep (also runs automatically every 60 s) |

Mouse 4 and Mouse 5 are not usable for any of these - Matcha can't see them.

---

## Notes

Everything starts disabled; nothing scans until you toggle it on.

Distances are shown in meters (studs / 2.75) to match Matchas.

**Hybrid mode is only needed for Map ESP.** Everything else - all the other ESPs,
aimbot, gun mods, melee, vehicle mods - works without it.

Matcha's Players handle can go stale mid-session (unless its the games anti-cheat i have no idea.)
When it does, player-based features idle and recover on their own instead of erroring.
The script keeps a scheduler health figure and throttles its own scanning when
the client falls behind. If ESP updates go sluggish, that's it working - check
Diagnostics.
