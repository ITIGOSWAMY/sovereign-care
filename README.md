# The Council of Sovereign Care

Put your phone down and leave it there. The app keeps a light while you're gone,
and tells you how long you managed.

Saanchie Goswamy · v1 (solo path)

---

## What this version does

- Tap an NFC tag → the app opens → one tap to Begin → lay the phone flat and walk away
- Keeps the screen awake so the page stays alive and the plinth stays lit
- Calibrates each phone's own sensor noise floor for 3 seconds before starting
- Detects a lift (tilt past 20° held for 300ms) and gives you **12 seconds to put it back**
- Ends the session on a screen touch, an app switch, or the grace window running out
- Records the duration honestly — **you are never zeroed out for stopping**
- Keeps duration, streak, longest hold, and the plate you reached
- Exports a shareable card and your full history as JSON

### Look

Black and white only, in two modes — **paper stock with black powder, or black ground with white
powder**. Not two themes: one design, two inks. The sun/moon toggle on the opening screen switches
them and remembers your choice; first run follows your system setting.

Type is **Latin Modern Roman** — the face scientific papers are typeset in, which is the right
register for an experiment from 1787. The **Dunhill** cut, with its tall ascenders, carries the
title and the duration. Three subsetted woff2 cuts ship in `fonts/` (~27KB total, GUST licence
included). They are self-hosted because Latin Modern is not installed on any phone.

**The sun and moon icons are Chladni plates too** — the sun is a mode with six nodal diameters and
no rings, the moon a plain disc bitten by a second circle. Same engine as the artwork, so they
cannot drift from it.

### The figure

The screen is a **Chladni plate** — the sand-on-a-vibrating-surface experiment, solved properly:

```
u(r, θ) = J_m(α_mn · r) · cos(m θ)
```

Bessel of the first kind, where `α_mn` is the n-th zero of `J_m`. Each grain random-walks by an
amount proportional to how hard the plate is vibrating beneath it, so grains freeze wherever the
surface is still and the nodal figure emerges on its own. Nobody draws it. Because the ring radii
come from Bessel zeros they are **unevenly spaced**, which is the signature of a real plate.

The grains are then blurred and pushed through a noise-perturbed threshold, so nearby particles
merge into clumped strokes with ragged wet edges — powder on a plate rather than a particle
system. That threshold is far too expensive to run every frame on a phone, so **the ink is
re-rendered about four times a second and crossfaded**; the sand settles slowly enough that this
is invisible. `INK_MS` at the top of the script controls it.

Three independent axes:

| | drives | reads as |
|---|---|---|
| **n** — nodal rings | how long you have been away | concentric rings, accumulating |
| **m** — nodal diameters | how *still* you have been | radial divisions; fidget and it drops |
| **orbit** | how many are holding | v2 — the parameter is wired, the sync layer is not |

Two people away the same hour get different figures depending on how steady they were.
A different mode is drawn each time the app opens.

### Sound and the ending

A struck gong on begin — six inharmonic partials with a long decay — then a low drone that
fades to nothing over the first few minutes. Synthesised in Web Audio, no files.

When a session ends the figure is **swept away** over four and a half seconds before the
numbers appear, the way a sand mandala is destroyed on completion. `SWEEP_MS` at the top of
the script controls it; set it to 0 to skip.

**Everything is stored in this device's localStorage. Nothing is ever uploaded.
There is no account, no analytics, and no backend.**

## What it does not do yet

No multi-device sync. Six phones in a gallery each run their own session independently —
the collectivity is in the room and the sightlines, not in software. Shared clocks,
simultaneous shattering, and persisted records are v2. See "Later" below.

---

## Deploy

No build step, no dependencies. Works on Vercel, GitHub Pages, Netlify, or any static host.

### Vercel

1. Go to vercel.com → **Add New → Project → Deploy without Git** (or drag this folder in)
2. Drop the whole folder — **including `fonts/`**. No framework, no build command, no output directory.
3. Copy the **production** URL.

> ### ⚠️ Read this before writing any NFC tag
> Vercel gives each deployment its own preview URL (`project-a1b2c3.vercel.app`) that
> **changes every time you push**. A tag written with a preview URL will break on your next
> deploy — and once a sticker is on someone's nightstand you cannot fix it.
>
> Only ever write the **stable production domain** to a tag. Better: buy a short custom
> domain (~$12/yr) and point it at Vercel. Tags are permanent; hosting is not. A custom
> domain means every sticker anyone owns keeps working even if you move hosts entirely.

### GitHub Pages

New public repo → upload all files to the root of `main` → Settings → Pages → source `main` / root.
Your URL is stable by default: `https://username.github.io/repo-name/`

HTTPS is required either way — motion sensors and Wake Lock will not run over plain HTTP.

---

## Install it as an app (do not skip this)

**iOS Safari will delete localStorage after seven days without a visit.** A streak app that
loses your history because you successfully stayed off your phone for a week is broken in
the worst possible way. Installed PWAs are exempt.

- **iPhone:** open the URL in Safari → Share → **Add to Home Screen**
- **Android:** Chrome will offer **Install app**, or use ⋮ → Add to Home screen

You also get a proper app icon, no browser chrome, and more reliable screen-wake.

---

## Writing your NFC tags

Buy NTAG215 stickers (a pack of 10 is about $9). Each tag holds one URL.

### iPhone
Web NFC does not exist on iOS, so tags must be written by hand — once per tag.

1. Install **NFC Tools** (Wakdev) from the App Store
2. **Write → Add a record → URL/URI**
3. Enter your production URL plus a ritual, e.g. `https://your-domain.com/?ritual=dinner`
4. Tap **Write**, then hold the **top edge** of the phone against the tag

### Android
Same app works, or Chrome can write tags directly via Web NFC. Android's antenna is usually
in the **centre of the back**, not the top edge.

### One pack, several rituals

Give each tag its own `?ritual=` and the app keeps them apart:

```
https://your-domain.com/?ritual=dinner
https://your-domain.com/?ritual=sleep
https://your-domain.com/?ritual=work
https://your-domain.com/?ritual=walk
```

Put them where they mean something — bedside, desk, dinner table, inside the front door.
The tag is a commitment device, not a sensor. Tapping it is a promise made with your hand.

---

## Gallery configuration

Add `?seat=N` instead of a ritual and the app labels itself as a council seat:

```
https://your-domain.com/?seat=1   … through   ?seat=6
```

In v1 the seats do not talk to each other. That is deliberate for a 72-hour build —
six people watching six glowing plinths in a dark room get the collective experience from
the sightlines, not from a network.

### Room setup

- Six plinths in a circle, 6–8 feet across, **with seating outside the circle**.
  Visitors place their own phone, walk back, sit down, and watch it from a distance.
  Distance, not absence — the temptation has to stay in the room.
- **Two marks on each plinth top.** A small tap target at the edge where the phone's top
  edge lands (NFC will not pass through solid wood, so the tag goes on top under a thin
  vinyl decal), and the resting ring in the centre.
- **Put a Qi charging pad under the centre ring.** Screens lit for 20+ minutes on a
  stranger's phone is otherwise a hard no for anyone below 40% battery, and battery anxiety
  silently decides who participates. ~$12 each. Keep the pad away from the tag; the coil
  can detune it. Keep a Lightning and a USB-C cable per plinth for phones without Qi.
- **Records go on a physical board on the wall**, updated by hand: longest solo hold,
  longest council, today's total. For a three-day show this beats a database — it cannot
  crash, it costs nothing, and a hand-written accumulating number is more on-theme for a
  piece about invisible labour than a server read.
- Narrow-beam spots on the plinth tops only. Room dark otherwise.

### Before the doors open

- Install as a PWA on any gallery-owned demo device
- On phones without Wake Lock support, set **Auto-Lock to Never** — the app warns you
- Test the lift threshold on the actual plinths. Footfall through a plinth reads
  differently than a desk; the 3-second calibration handles it, but confirm.

---

## Tuning

All thresholds are constants at the top of the script block in `index.html`:

| Constant | Default | What it does |
|---|---|---|
| `TILT_LIMIT` | `20` | Degrees off horizontal before it counts as lifted |
| `TILT_HOLD` | `300` | Milliseconds of sustained tilt before the grace window opens |
| `GRACE_MS` | `12000` | How long you have to put it back down |
| `CAL_MS` | `3000` | Baseline calibration length |
| `SPIKE` | `1.35` | Hard motion threshold above the calibrated floor |

If false triggers happen on a wobbly plinth, raise `SPIKE` and `TILT_HOLD` first.

> If you edit any file after deploying, bump `CACHE = 'council-v1'` in `sw.js`.
> Otherwise installed phones keep serving the old cached version.

---

## Later (v2)

- Shared room clock across seats; any single grab resets it for everyone
- Persisted records across the run of a show
- Council rooms by code, so people at home can hold together

You do not need Firebase for this. **Supabase Realtime** is a gentler landing —
free tier, Postgres underneath, realtime channels — and pairs naturally with Vercel.
Solo mode should stay backend-free forever; only the council needs a relay.

---

## Files

```
index.html            the whole app
manifest.webmanifest  PWA metadata
sw.js                 offline shell (bump CACHE on every change)
icon-192.png
icon-512.png
```
