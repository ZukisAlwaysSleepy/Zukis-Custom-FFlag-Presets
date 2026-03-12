# Documentation — Zuki's Custom FFlag Presets

Full technical reference, version history, and flag explanations.

---

## Table of Contents

1. [Reforged — Version History](#reforged--version-history)
2. [Stupid FPS — Version History](#stupid-fps--version-history)
3. [Flag Reference — Reforged](#flag-reference--reforged)
4. [Flag Reference — Stupid FPS](#flag-reference--stupid-fps)
5. [Flag Reference — Networking + Physics](#flag-reference--networking--physics)
6. [Test Methodology](#test-methodology)
7. [Confirmed Dead Flags](#confirmed-dead-flags)

---

## Reforged — Version History

### V6.4a — Current
**File:** `Visual Flags/Remarkably Beautiful - Reforged V6.4a.json`
**Status:** Stable, locked

Changes from V6.3:
- Full cubemap capture pipeline added (`FFlagRenderCubemapCapture`, resolution 1024, frequency 10)
- IBL resolution set to 2048
- Skybox cube map size set to 2048
- Environment reflections enabled at 64 samples
- Fresnel intensity: 150, specular intensity: 200
- Sky colour flag added (`FFlagRenderSkyUseUpdatedSkyColor`)
- FPS target raised to 9999 (effectively unlocked)
- D3D11 FL10 path explicitly disabled (`FFlagDebugGraphicsPreferD3D11FL10: false`)
- HiDPI rendering enabled
- Graphics quality lock added (`FFlagFixGraphicsQuality`)
- Shadow fade range added (`FIntRenderShadowFadeRange: 200`)
- Shadow prepass enabled (`FFlagRenderShadowmapPrepass`)
- Mesh validation disabled (`FFlagRenderValidateMeshes: false`)
- `DFIntRenderQualityLevel: 21` added as belt-and-braces quality lock
- Local light updates max raised from 24 to 32, min from 8 to 16
- Local light fade distances extended to 500/800

Proto build findings (V6.3.1P) carried into V6.4a:
- Cubemap capture resolution proven at 1024 — 8192 costs VRAM with no visible gain
- IBL and skybox at 2048 confirmed sweet spot
- Fresnel 150 and specular 200 avoid blowout on light-coloured paint
- Cubemap update every 10 frames imperceptible vs every frame
- Environment reflection samples at 64 — diminishing returns confirmed above this

---

### V6.3.1P — Proto Build
**Status:** Archived, superseded by V6.4a

Reflection maximum test build. All resolution values pushed to 8192, cubemap capture every frame, specular 300, fresnel 250. Confirmed working features carried forward to V6.4a. Several flags proven harmful at extreme values and dropped or reduced.

Key findings:
- `FIntRenderReflectionResolution: 8192` breaks SSR entirely — dropped from all builds
- `FIntRenderCubemapCaptureResolution: 8192` causes VRAM pressure, no visible quality gain over 1024
- `FIntRenderFresnelIntensity: 250` causes blowout on white/light paint
- `FIntRenderSpecularIntensity: 300` too aggressive in direct sunlight
- Blue metallic paint showing environment colour bleed confirmed working at these settings

---

### V6.3 — Stable
**Status:** Archived, superseded

Character shadow blob fix:
- `FIntRenderCharacterShadowResolution` reduced 8192 → 2048 — eliminated hard black pooling blob on flat geometry
- `FIntRenderShadowIntensity` reduced 5 → 4
- `FIntRenderShadowmapCascadeDistribution` reverted 3 → 2
- `FIntRenderShadowmapBias` kept at -150

Root cause: High character shadow resolution combined with high intensity produces a hard-edged black stamp on flat open surfaces. 2048 gives correct soft silhouette falloff including thin geometry.

---

### V6.2 — Stable
**Status:** Archived, superseded

Reflection and shadow regression fixes:
- `FIntRenderReflectionResolution` dropped — confirmed it breaks SSR above engine ceiling
- `FIntRenderShadowmapCascadeDistribution` reverted 3 → 2 — value 3 breaks flat ground shadow precision
- `FIntRenderShadowIntensity` reduced 6 → 5
- `FFlagRenderReflectionHighQuality` added
- Roughness pushed to -1000000000

---

### V6.1 — Stable
**Status:** Archived, superseded

First reflection push:
- `DFIntRenderClampRoughnessMax` pushed to -1000000000
- `FFlagRenderReflectionHighQuality` added
- `FIntRenderReflectionResolution: 2048` added — later proven to break SSR, removed in V6.2
- Local light fade distances extended to 500/400
- Local light updates max raised to 32

---

### V6 — Stable
**File:** `Visual Flags/Remarkably Beautiful V6 (Final).json`
**Status:** Archived, superseded by Reforged

Major rebuild from original config. Key values at this stage:
- Shadow intensity: 6, bias: -200
- Cascade distribution: 3 (later proven problematic)
- Character shadow resolution: 8192 (later proven to cause blob)
- Cascade max distance: 500
- AO samples: 64, intensity: 1, radius: 4
- FPS target: 60
- Local light fade: 200/400

---

### V0.9b — Original
**File:** `Visual Flags/Archive/Remarkably Beautiful V0.9b (Stable - Archived).json`
**Status:** Archived, origin build

The config that started everything. Built for stable 60fps cinematic recording on a GTX 1650. Conservative settings throughout — no roughness floor push, no reflection stack, no cascade tuning.

Key values at this stage:
- Shadow intensity: 3, bias: 0, shadowmap size: 2048
- No cascade distribution tuning — default values
- Render distance: 100k
- MSAA: 4x (not 8x)
- AO: not present
- Grass distance: 300
- Threads: 12
- Texture compositor jobs: 2
- CSG LOD: 1000/2000/3000/4000 (much tighter than later versions)
- No roughness floor (`DFIntRenderClampRoughnessMax` not set)
- No IBL, no GI, no cubemap capture

The shadow pooling and pixelation issues discovered during the V6 investigation were caused by `FIntRenderShadowmapMaxDistance` being too short at the default, and shadow intensity stacking with AO. These were the two problems that drove every subsequent version.

---

## Stupid FPS — Version History

### V1.0 — Current
**File:** `Networking + Physics Flags/Stupid FPS multi client.json`
**Status:** Stable

Evolved from Zuki's Complete Comp Overhaul (same config, threads at 14). Changes made:
- Thread counts raised from 14 to 16 to match hardware
- Network/physics block added (confirmed working flags only — see below)
- GPU/CPU culling stack added
- Memory management block added
- Dead physics sender flags excluded after cross-game testing

Network/physics flags added:
- `DFIntPhysicsReceiveRate: 60`
- `DFIntPhysicsSolverIterations: 8`
- `DFIntPhysicsContactMaxSolverIter: 8`
- `DFIntNetworkOwnershipTransferDelay: 0`
- `DFIntMinimumNetworkOwnershipTransferDelay: 0`
- `DFIntConnectionMTUSize: 1400`
- `DFIntRakNetResendBufferArrayLength: 128`
- `DFIntOptimizeSendReceiveQueueSize: 100`
- `DFIntMaxMissedWorldStepsRemembered: 120`
- `DFIntSimulationThrottleAdjustTime: 0`

Culling stack added:
- `FFlagRenderCPUCullGeometry`
- `FFlagRenderCullGeometryExact`
- `FFlagRenderCPUShadowCull`
- `FFlagRenderBatchShadowMaps`
- `FFlagRenderLightGridEfficientCull`
- `FFlagEnableBatchedSubmitQueue`
- `FFlagGPUBasedOcclusionCulling`
- `FFlagRenderUseOcclusionQueries`
- `DFIntGPUOcclusionCullingMinScreenSize: 50`
- `FIntRenderMaxOpaqueDrawcalls: 4096`
- `FIntRenderMaxTransparentDrawcalls: 2048`

Memory management added:
- `DFIntTextureCompositorMemoryBudgetMB: 512`
- `DFIntNumAssetsMaxToPreload: 500`
- `DFIntAssetPreloadingMaxAllowedMB: 512`
- `DFIntSoundMaxChannels: 32`
- `DFIntAnimationThrottleMaxElapsed: 0`

---

### Zuki's Complete Comp Overhaul — Base
**File:** `FPS Presets/Zukis Complete Comp Overhaul.json`
**Status:** Base preset

Original FPS nuke config. Threads at 14, no network/physics additions, no culling stack. Stupid FPS multi client is this config evolved. Kept in repo for reference.

---

## Flag Reference — Reforged

### Lighting Engine

| Flag | Value | Explanation |
|---|---|---|
| `FFlagDebugForceFutureIsBrightPhase3` | true | Enables FIB3 — full Future Is Bright pipeline. Cascaded shadowmaps, atmosphere, IBL, GI bounce, complete PBR. Only one phase should be true at a time. |
| `FFlagDebugForceFutureIsBrightPhase2` | false | FIB2 — improved shadowmaps and local light shadows, no full GI. Kept false. |
| `FFlagDebugForceFutureIsBrightPhase1` | false | FIB1 — basic light attenuation only. Kept false. |
| `DFFlagDebugRenderForceTechnologyVoxel` | false | Legacy Voxel engine. Must be false for FIB3. |
| `FFlagNewLightAttenuation` | true | Physically accurate light falloff. Required for correct local light behaviour under FIB3. |
| `FFlagFastGPULightCulling3` | true | GPU-side culling of non-contributing lights. Performance improvement, no visual cost. |

### Shadows

| Flag | Value | Explanation |
|---|---|---|
| `FIntRenderShadowIntensity` | 4 | Shadow darkness multiplier. 4 gives strong shadows without crushing dark areas. Higher values cause pooling in occluded geometry. |
| `FIntRenderShadowmapSize` | 8192 | Shadowmap texture size. Maximum. |
| `FIntRenderShadowmapResolution` | 8192 | Shadowmap render resolution. Paired with size. |
| `FIntRenderShadowmapBias` | -150 | Pulls shadow edges tighter to geometry. Reduces acne and floating shadows. |
| `DFIntRenderShadowmapBias` | -150 | Dynamic version. Both must be set. |
| `FIntRenderShadowmapCascadeCount` | 4 | Shadow cascade splits. 4 is maximum. |
| `FIntRenderShadowmapCascadeDistribution` | 2 | Cascade distance spread. 2 is balanced. 3 breaks flat-ground shadow precision — confirmed in testing. |
| `FIntRenderShadowmapMaxDistance` | 500 | Shadow render distance. 200 caused cascade gaps on original config. |
| `FIntRenderCharacterShadowResolution` | 2048 | Character shadow resolution. Intentionally not 8192 — higher causes hard black pooling blobs on flat surfaces. |
| `FFlagRenderShadowCulling` | true | Culls shadow casters not contributing to visible scene. |
| `FFlagRenderShadowmapPrepass` | true | Depth prepass before shadowmap render. Sharpens edges, reduces overdraw. |
| `FIntRenderShadowFadeRange` | 200 | Smooth shadow fade at max distance instead of hard pop. |

### Ambient Occlusion

| Flag | Value | Explanation |
|---|---|---|
| `FIntRenderAmbientOcclusionSamples` | 64 | AO sample count. 64 gives clean results without noise. |
| `FIntRenderAmbientOcclusionRadius` | 4 | AO reach from surfaces. 4 gives natural depth. |
| `FIntRenderAmbientOcclusionIntensity` | 1 | AO darkness. Kept at 1 — higher values stack with shadow intensity and over-darken. |
| `FIntRenderAmbientOcclusionBlurRadius` | 1 | AO blur. 1 keeps it tight without halos. |

### Reflections

| Flag | Value | Explanation |
|---|---|---|
| `DFIntRenderClampRoughnessMax` | -1000000000 | Roughness floor at engine minimum. Maximum mirror and gloss response on smooth surfaces. Responsible for wet paint look on car bodies. |
| `FFlagRenderReflectionHighQuality` | true | Forces high-quality reflection path. |
| `FFlagRenderSkyboxReflections` | true | Skybox contributes to surface reflections. Critical for outdoor ambient on car paint. |
| `FFlagRenderEnvironmentReflections` | true | Environment geometry contributes to surface reflections. |
| `FIntRenderEnvironmentReflectionSamples` | 64 | Environment reflection sample count. Diminishing returns confirmed above 64. |
| `FFlagRenderCubemapCapture` | true | Per-object local cubemap capture for accurate environment reflections. |
| `FIntRenderCubemapCaptureResolution` | 1024 | Captured cubemap resolution. 1024 confirmed sweet spot — 8192 costs VRAM with no visible gain. |
| `FIntRenderCubemapCaptureFrequency` | 10 | Cubemap update frequency in frames. Every 10 is imperceptible vs every frame. |
| `FIntRenderSkyboxCubeMapSize` | 2048 | Skybox environment map size for IBL. |
| `FIntRenderIBLResolution` | 2048 | Image-based lighting resolution. Affects sky colour bleed into surface reflections. |
| `FIntRenderSpecularIntensity` | 200 | Specular highlight strength. 200 gives strong highlights without blowout in direct sun. |
| `FIntRenderFresnelIntensity` | 150 | Fresnel rim effect. The glowing edge on panel curves at glancing angles. 250 causes blowout on light paint. |

### Global Illumination & Atmosphere

| Flag | Value | Explanation |
|---|---|---|
| `FFlagRenderEnableGlobalIllumination` | true | GI bounce — surfaces receive indirect light from nearby lit surfaces. |
| `FFlagEnableOutdoorAmbientIBL` | true | Outdoor IBL using skybox as ambient light source. |
| `FFlagRenderAtmosphere` | true | Atmospheric scattering — haze, fog falloff, sky gradient. |
| `FFlagRenderSkyUseUpdatedSkyColor` | true | Accurate sky colour sampling for IBL. Improves ambient colour on surfaces. |

### Performance

| Flag | Value | Explanation |
|---|---|---|
| `FIntDebugGraphicsQualityOverride` | 21 | Forces maximum graphics quality. |
| `DFIntDebugFRMQualityLevelOverride` | 21 | FRM quality override — prevents auto-degradation under load. |
| `DFIntRenderQualityLevel` | 21 | Belt-and-braces quality lock alongside FRM override. |
| `FIntDebugForceMSAASamples` | 8 | 8x MSAA. Maximum edge smoothness. |
| `FIntRenderAnisotropy` | 16 | 16x anisotropic filtering. Maximum texture sharpness at oblique angles. |
| `FIntRenderDistance` | 200000 | Maximum render distance. |
| `FFlagRenderNoLod` | true | Disables LOD — everything renders at full quality regardless of distance. |
| `DFIntTaskSchedulerTargetFps` | 9999 | Effectively uncapped FPS. |
| `FFlagDebugGraphicsPreferD3D11` | true | Forces D3D11 rendering path. |
| `FFlagDebugGraphicsPreferD3D11FL10` | false | Prevents fallback to reduced D3D11 Feature Level 10 path. |
| `FFlagRenderHiDpi` | true | Renders at native DPI. Cleaner image on high resolution displays. |
| `FFlagFixGraphicsQuality` | true | Prevents graphics quality from auto-degrading under load. |
| `FIntRuntimeMaxNumOfThreads` | 16 | Maximum CPU threads available to Roblox. |
| `FIntTaskSchedulerThreadCount` | 16 | Task scheduler thread count. |

---

## Flag Reference — Stupid FPS

### Rendering

| Flag | Value | Explanation |
|---|---|---|
| `DFFlagDebugRenderForceTechnologyVoxel` | true | Forces Voxel lighting — cheapest rendering path, no PBR. |
| `FFlagGraphicsEnableNewShadowTechnology` | false | Disables modern shadowmap system. |
| `FFlagRenderLocalLightShadows` | false | Disables local light shadow casting. |
| `FIntRenderShadowIntensity` | 0 | Shadow intensity zeroed. |
| `FIntRenderShadowmapSize` | 64 | Shadowmap at minimum. |
| `FFlagDisablePostFX` | true | Disables all post-processing. |
| `FFlagRenderAtmosphere` | false | Disables atmosphere. |
| `FFlagDebugSkyGray` | true | Forces flat grey sky — no skybox rendering cost. |
| `FIntDebugTextureManagerSkipMips` | 6 | Skips 6 mip levels. Lower resolution textures, major VRAM and streaming saving. |
| `FIntDebugGraphicsQualityOverride` | 1 | Minimum graphics quality. |
| `FIntRenderDistance` | 10000 | Reduced render distance. |
| `FFlagRenderNoLod` | false | LOD active — distant objects render at reduced quality. |

### Culling Stack

| Flag | Value | Explanation |
|---|---|---|
| `FFlagRenderCPUCullGeometry` | true | CPU-side geometry culling before GPU submission. Reduces draw calls. |
| `FFlagRenderCullGeometryExact` | true | Exact bounds culling — more accurate than bounding sphere. |
| `FFlagRenderCPUShadowCull` | true | CPU culls shadow casters before GPU. |
| `FFlagRenderBatchShadowMaps` | true | Batches shadow map renders together. |
| `FFlagGPUBasedOcclusionCulling` | true | GPU culls objects not visible from camera. |
| `DFIntGPUOcclusionCullingMinScreenSize` | 50 | Objects under 50px on screen culled entirely. |
| `FIntRenderMaxOpaqueDrawcalls` | 4096 | Hard cap on opaque draw calls per frame. |
| `FIntRenderMaxTransparentDrawcalls` | 2048 | Hard cap on transparent draw calls per frame. |
| `FFlagEnableBatchedSubmitQueue` | true | Batches GPU command submissions. Reduces CPU-GPU sync overhead at high FPS. |
| `FFlagRenderUseOcclusionQueries` | true | GPU occlusion queries for accurate visibility testing. |

---

## Flag Reference — Networking + Physics

| Flag | Value | Explanation |
|---|---|---|
| `DFIntPhysicsReceiveRate` | 60 | Incoming physics packet processing rate. |
| `DFIntPhysicsSolverIterations` | 8 | Physics solver iterations per step. Default 4. Higher = more accurate local collision. |
| `DFIntPhysicsContactMaxSolverIter` | 8 | Contact point solver iterations. Matched to solver iterations. |
| `DFIntNetworkOwnershipTransferDelay` | 0 | Delay before network ownership transfers to client. Zero = instant vehicle entry. |
| `DFIntMinimumNetworkOwnershipTransferDelay` | 0 | Minimum transfer delay floor. Also zeroed. |
| `DFIntConnectionMTUSize` | 1400 | Network packet size. 1400 is optimal for most connections — reduces fragmentation. Confirmed 153ms → 33ms ping improvement in testing. |
| `DFIntRakNetResendBufferArrayLength` | 128 | Packet resend buffer. Larger buffer handles packet loss better. |
| `DFIntRakNetSplitMessageProgressInterval` | 0 | Disables split message progress reporting overhead. |
| `DFIntOptimizeSendReceiveQueueSize` | 100 | Send/receive queue size optimisation. |
| `DFIntMaxMissedWorldStepsRemembered` | 120 | How many missed physics steps the client remembers for reconciliation. |
| `DFIntSimulationThrottleAdjustTime` | 0 | Disables simulation throttle adjustment delay. |
| `DFIntNetworkOwnershipPerReceive` | 30 | Network ownership updates per receive cycle. |
| `DFIntPhysicsSimBodyInterpolationFactor` | 0 | Removes artificial position smoothing that hides but does not fix position errors. |
| `DFIntMaximumFrequencyForMechanismSleep` | 2 | Minimum frequency before physics mechanisms are allowed to sleep. |

---

## Test Methodology

All flags tested in live Roblox sessions using in-game debug overlays only. No external tools.

**Test environments:**
- Midnight Chasers (City map) — open world driving, night/day cycle, nature scenes
- Midnight Chasers (Boneville Salt Flats) — flat open terrain, direct sunlight, car paint and shadow testing
- Sniper Arena — competitive FPS, indoor map, 7-8 players active
- Nature test place — FIB3 lighting verification

**Monitoring:** Shift+F1 (network), Shift+F9 (full stats) for all before/after comparisons.

**Confirmed results — Stupid FPS vs Reforged on Midnight Chasers:**

| Metric | Reforged | Stupid FPS | Delta |
|---|---|---|---|
| Client memory | 2218 MB | 1501 MB | -717 MB |
| Ping | 153 ms | 33 ms | -120 ms |
| Draw calls | 1472 | 187 | -87% |
| GPU frame time | 14 ms | 2.6 ms | -81% |

**Confirmed results — Stupid FPS on Sniper Arena (8 players, active combat):**

| Metric | Value |
|---|---|
| Client memory | 1939 MB |
| Ping | 133 ms |
| Draw calls | 236 |
| GPU frame time | 1.6 ms |
| Renderer capable | 346 FPS |

---

## Hardware Reference

All presets developed and tested on:

| Component | Spec |
|---|---|
| CPU | AMD Ryzen 7 4800H @ 2.9GHz |
| GPU | NVIDIA GeForce GTX 1650 GDDR6 4GB |
| RAM | 40GB DDR4 3200MHz (32GB + 8GB modules) |
| Storage | 512GB M.2 NVMe PCIe Gen 3×4 |
| Display | 32" 1440p 240Hz external (capped at 144Hz on cable) |
| Network | Ethernet — 100Mbps down / 40Mbps up |

Thread counts in both presets are set to 16. If your CPU has fewer cores adjust `FIntTaskSchedulerThreadCount` and `FIntRuntimeMaxNumOfThreads` to match.

---

## Confirmed Dead Flags

These flags were tested and confirmed to have no effect. Do not add them back.

| Flag | Reason |
|---|---|
| `DFIntS2PhysicsSenderRate` | Outgoing physics send rate is platform-locked server-side. Confirmed zero effect across Midnight Chasers and Sniper Arena. Roblox enforces server authority on all physics send. |
| `DFIntNetworkRunPhysicsSend` | Same reason — platform-locked. |
| `FIntRenderReflectionResolution` | Breaks SSR above the engine's internal ceiling. Setting any value causes SSR to fail. Dropped from all builds after V6.2. |
