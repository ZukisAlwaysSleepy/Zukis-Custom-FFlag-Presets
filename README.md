# Zuki's Custom FFlag Presets

A collection of Roblox client flag configs I've built and tested. Each preset has a specific purpose — some are about pushing visuals as far as the engine allows, some are about raw performance, and one combines the FPS base with a network and physics layer on top.

---

## Structure

```
/FPS Presets
    Zukis Complete Comp Overhaul.json

/Networking + Physics Flags
    Stupid FPS multi client.json

/Visual Flags
    Remarkably Beautiful - Reforged V6.4a.json
    Remarkably Beautiful V6 (Final).json
    /Archive
        Remarkably Beautiful V0.9b (Stable - Archived).json

README.md
DOCS.md
```

---

## Presets

### Remarkably Beautiful — Reforged `V6.4a`
The main visual preset. Started as a shadow investigation on V0.9b and went through six major versions — full FIB3 pipeline, cascaded shadowmaps, cubemap capture, IBL, GI, AO, fresnel, the lot. V6 is in the same folder, and the original V0.9b that started it all is in the Archive subfolder.

Works on any game. Tested on Midnight Chasers (City map and Boneville Salt Flats). RTX-class GPU and 16GB+ RAM recommended for best results, though it was developed entirely on a GTX 1650.

---

### Remarkably Beautiful `V0.9b` ← where it started
The original config. Built for stable 60fps cinematic recording on a GTX 1650. Conservative across the board — shadowmap at 2048, no bias tuning, MSAA 4x, threads at 12, render distance 100k, grass distance 300, no reflection stack, no roughness floor push. Everything in V6.4a is a direct evolution of what started here.

Kept in `/Visual Flags/Archive` for reference.

---

### Zukis Complete Comp Overhaul
Pure FPS config. Voxel lighting, everything visual stripped back, built for competitive play and squeezing frames. This is the base that Stupid FPS multi client was built on top of.

---

### Stupid FPS Multi Client
The FPS preset extended with a confirmed-working network and physics layer. MTU tuning, physics solver quality, ownership transfer timing, GPU/CPU culling stack, memory capped for multi-client dev use.

Tested on Midnight Chasers and Sniper Arena (FFA, 8 players active). Confirmed results vs Reforged on the same hardware:
- Memory per instance: 2218MB → 1501MB
- Ping: 153ms → 33ms
- Draw calls reduced by 87%
- GPU frame time: 14ms → 2.6ms

One thing worth knowing: outgoing physics send rate (`DFIntS2PhysicsSenderRate`) is locked server-side on every game tested. The flag does nothing and isn't included. Full breakdown in DOCS.md.

---

## Installation

Using a bootstrapper is the easiest way to manage FFlags — no hunting down version folders, no reapplying after every Roblox update. Both Bloxstrap and Fishstrap handle this for you.

### Bloxstrap

1. Open Bloxstrap
2. Go to **Fast Flags** in the left sidebar
3. Click **Fast Flag Editor**
4. Hit the warning prompt — it just tells you to be careful, click through it
5. Click **Add New → Import JSON**
6. Open the preset JSON from this repo, copy everything inside it, paste it into the box and click **OK**
7. Click **Save** in the top right
8. Launch Roblox through Bloxstrap as normal

Your flags are now saved inside Bloxstrap and will persist across Roblox updates automatically.

---

### Fishstrap

Fishstrap is a Bloxstrap fork so the layout is identical:

1. Open Fishstrap
2. Go to **FastFlags** tab
3. Click **Fast Flag Editor**
4. Click **Add New → Import JSON**
5. Paste the contents of your chosen preset JSON and click **OK**
6. Click **Save**
7. Launch Roblox through Fishstrap

---

### After a Roblox update

If you're using Bloxstrap or Fishstrap you don't need to do anything — your flags are stored inside the bootstrapper and re-applied every launch automatically.

If for any reason your flags stopped working after an update, open the Fast Flag Editor and check they're still listed. If they've been cleared, just re-import the JSON following the steps above.

---

### Manual install (no bootstrapper)

If you'd rather not use a bootstrapper:

**Windows**
1. Go to `%localappdata%\Roblox\Versions`
2. Open the latest version folder (the one containing `RobloxPlayerBeta.exe`)
3. Create a folder called `ClientSettings` if it doesn't exist
4. Inside it, create `ClientAppSettings.json`
5. Paste your preset into it and save

**Mac**
1. Go to `~/Library/Roblox/Versions`
2. Open the latest version folder
3. Create `ClientSettings/ClientAppSettings.json`
4. Paste preset contents and save

> With manual install the file gets wiped on every Roblox update. You'll need to reapply it each time. This is why bootstrapper is the recommended method.

---

### Checking it worked
Press **Shift+F9** in-game. Reforged should show `FRM Quality: 21`, FPS presets should show `FRM Quality: 1`. `Technology` will say `Unified` on both visual presets — that's correct, FIB3 is a client-side override.

---

## Test Hardware

All presets were developed and tested on the following machine:

| Component | Spec |
|---|---|
| CPU | AMD Ryzen 7 4800H @ 2.9GHz |
| GPU | NVIDIA GeForce GTX 1650 GDDR6 4GB |
| RAM | 40GB DDR4 3200MHz (32GB + 8GB) |
| Storage | 512GB M.2 NVMe PCIe Gen 3×4 |
| Display | 32" 1440p 240Hz external (capped at 144Hz on cable) |
| Network | Ethernet — 100Mbps down / 40Mbps up |

---

## Debug Shortcuts

| Shortcut | What it shows |
|---|---|
| Shift+F1 | Network — ping, send/receive rates, physics packets |
| Shift+F2 | Full debug stats — FPS, physics, render times, memory, draw counts |
| Shift+F3 | Network graph |
| Shift+F4 | Memory breakdown by category |
| Shift+F5 | Physics — solver step time |
| Shift+F8 | Developer console — script output, errors, warnings |
| Shift+F9 | Client stats overlay — comprehensive dump of all active stats |

---

## Known Issues

### Reforged
- Reflections are screen-space only. Car roofs won't reflect the sky when the camera is behind it — engine limitation, nothing flags can fix.
- FIB3 shows as `Unified` in the debug menu. That's correct, it's a client override.
- Character shadow resolution is 2048 on purpose. 8192 creates a hard black blob on flat ground — tested and confirmed across multiple scenes.

### Stupid FPS / Comp Overhaul
- Textures will look low-res up close. That's the mip skipping doing its job.
- Physics send rate is server-locked. Outgoing physics packets stay near zero regardless of flags. See DOCS.md.
- FPS ceiling depends on the game. The renderer can push 300+ but most games cap the server heartbeat at 60Hz.

### All Presets
- Resets on every Roblox update.
- Client-side only. Server physics, game logic, and other players are unaffected.
- CrossExperience errors in the developer console are normal — game-side asset permissions, nothing to do with flags.

---

## Contributing

If you want to add something, test it in at least two different games first and bring debug menu screenshots before and after so there's actual data behind the change. Flags copied from other community lists without testing won't be merged.

Bug reports should include your hardware, which preset, which game, and debug menu screenshots showing the problem.

---

## Disclaimers

These are client-side FFlags — configuration values built into Roblox itself that you're just toggling. They are not mods, not shaders, not injectors, and not third-party software. Roblox exposes these flags intentionally and bootstrappers like Bloxstrap and Fishstrap have dedicated UIs for managing them.

Using FFlags is not bannable. You are not modifying game files, injecting code, or altering how the game server sees you. Everything here affects your client renderer only.

These are also not shaders. There are no ReShade presets, no custom HLSL, no external post-processing. What you're seeing is the Roblox engine running its own pipeline at settings it's fully capable of — just not ones it defaults to.

Results will vary depending on your hardware, the game you're playing, and what lighting technology the developer has set server-side. A game running Voxel or Compatibility lighting will not respond to FIB3 flags the same way Midnight Chasers does. Some flags may have no visible effect in certain games.

No config in this repo has been tested on every game. If something looks wrong in a specific game, it's likely that game's lighting setup, not a bug in the preset. The debug menus are there to help you diagnose it.

---



| Prefix | What it means |
|---|---|
| `FFlag` | Boolean on/off, set at launch |
| `FInt` | Numeric value, set at launch |
| `DFFlag` | Boolean, can be overridden by Roblox servers at runtime |
| `DFInt` | Numeric, can be overridden by Roblox servers at runtime |

The `DF` prefix flags can technically be changed server-side at any time. The static `F` prefix ones are locked once the client launches.

---

Full version history and flag-by-flag breakdowns are in [DOCS.md](DOCS.md).
