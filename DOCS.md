# Documentation — Zuki's Custom FFlag Presets

Full technical reference, complete version history, and flag explanations.

---

## Table of Contents

1. [Visual Presets — Full Version History](#visual-presets--full-version-history)
2. [FPS Preset — Version History](#fps-preset--version-history)
3. [Networking + Physics — Version History](#networking--physics--version-history)
4. [Flag Reference — Reforged V6.4a](#flag-reference--reforged-v64a)
5. [Flag Reference — Stupid FPS / Comp Overhaul](#flag-reference--stupid-fps--comp-overhaul)
6. [Flag Reference — Networking + Physics](#flag-reference--networking--physics)
7. [Test Methodology](#test-methodology)
8. [Hardware Reference](#hardware-reference)
9. [Confirmed Dead Flags](#confirmed-dead-flags)

---

## Visual Presets — Full Version History

### V6.4a — Current (Remarkably Beautiful — Reforged)
**File:** `Visual Flags/Remarkably Beautiful - Reforged V6.4a.json`
**Status:** Stable, locked

Changes from V6.4:
- `DFIntTaskSchedulerTargetFps` raised from 240 → **9999** (fully uncapped)
- `FFlagDebugGraphicsPreferD3D11FL10: false` added — prevents fallback to reduced Feature Level 10 D3D11 path
- `FFlagRenderHiDpi: true` added — native DPI rendering
- `FFlagFixGraphicsQuality: true` added — prevents quality auto-degrading under load
- `DFIntRenderQualityLevel: 21` added as third quality lock
- `FFlagRenderValidateMeshes: false` added — skips redundant per-frame mesh validation

---

### V6.4
**Status:** Archived, superseded by V6.4a

Proto findings baked in at confirmed safe values. First version with the full cubemap + IBL + reflection stack stable end to end:
- `FFlagRenderCubemapCapture: true`, resolution **1024**, frequency **10**
- `FIntRenderIBLResolution: 2048`
- `FIntRenderSkyboxCubeMapSize: 2048`
- `FFlagRenderSkyboxReflections: true`
- `FFlagRenderEnvironmentReflections: true`, samples **64**, bias **-50**
- `FFlagRenderSpecularMap: true`
- `FIntRenderSpecularIntensity: 200`
- `FIntRenderFresnelIntensity: 150`
- `FFlagRenderSkyUseUpdatedSkyColor: true`
- `FFlagRenderShadowmapPrepass: true` added
- `FIntRenderShadowFadeRange: 200` added
- Local light updates raised to max **32**, min **16**
- Local light fade extended to **500/800**
- `FIntRenderCharacterShadowFarDistance` raised to **800**
- Shadow max distance raised to **500**
- `DFIntTaskSchedulerTargetFps: 240`

---

### V6.3.1P — Proto Build
**Status:** Archived — stress test only, never released as stable

Every reflection value pushed to ceiling to find safe limits for V6.4:

| Flag tested | Value used | Outcome |
|---|---|---|
| `FIntRenderCubemapCaptureResolution` | 8192 | No visible gain over 1024 — VRAM pressure only |
| `FIntRenderSkyboxCubeMapSize` | 8192 | Pulled back to 2048 in stable |
| `FIntRenderIBLResolution` | 8192 | Pulled back to 2048 in stable |
| `FIntRenderReflectionResolution` | 8192 | **Breaks SSR** — dropped permanently |
| `FIntRenderEnvironmentReflectionSamples` | 128 | Pulled back to 64 — diminishing returns |
| `FIntRenderSpecularIntensity` | 300 | Blowout in direct sun — pulled to 200 |
| `FIntRenderFresnelIntensity` | 250 | Blowout on light paint — pulled to 150 |
| `FIntRenderCubemapCaptureFrequency` | 1 | Every frame — too expensive, pulled to 10 |

Visual targets confirmed during this build: environment colour bleed on blue metallic paint working correctly, carbon fibre weave rendering individual fibres, ground headlight pools best result to date. These results confirmed the reflection stack was worth fully building out.

---

### V6.3
**Status:** Archived, superseded

Character shadow blob fix:
- `FIntRenderCharacterShadowResolution` 8192 → **2048** — eliminated hard black stamp on flat geometry
- `FIntRenderShadowIntensity` 5 → **4**
- Bias held at **-150** (both flags)
- Cascade distribution held at **2**

Root cause confirmed: high character shadow resolution combined with high shadow intensity produces a hard-edged black pool on flat surfaces. 2048 restores correct soft falloff including on thin geometry like accessories and spikes.

---

### V6.2
**Status:** Archived, superseded

SSR regression fix and shadow tuning correction:
- `FIntRenderReflectionResolution` **dropped entirely** — any value breaks SSR above engine ceiling, confirmed at 2048 in V6.1
- `FIntRenderShadowmapCascadeDistribution` 3 → **2** — value 3 breaks shadow precision on flat ground
- `FIntRenderShadowIntensity` 6 → **5**
- `FFlagRenderReflectionHighQuality: true` added
- `DFIntRenderClampRoughnessMax` pushed to **-1000000000**
- `FIntRenderShadowmapBias` / `DFIntRenderShadowmapBias` -200 → **-150**

---

### V6.1
**Status:** Archived, superseded

First reflection push after V6 baseline:
- `DFIntRenderClampRoughnessMax` -640000000 → **-1000000000**
- `FFlagRenderReflectionHighQuality: true` added
- `FIntRenderReflectionResolution: 2048` added — proven to break SSR, dropped in V6.2
- Local light fade extended to **500/800**
- Local light updates max raised to **32**, min **16**

---

### V6 — Stable
**File:** `Visual Flags/Remarkably Beautiful V6 (Final).json`
**Status:** Archived, superseded by Reforged

Major rebuild establishing the high-quality baseline. Key values at this stage:
- `FIntRenderShadowIntensity`: **6**
- `FIntRenderShadowmapBias` / `DFIntRenderShadowmapBias`: **-200**
- `FIntRenderShadowmapCascadeDistribution`: **3** (later proven problematic on flat ground)
- `FIntRenderShadowmapMaxDistance`: **500**
- `FIntRenderCharacterShadowResolution`: **8192** (later proven to cause blob on flat surfaces)
- `FIntRenderCharacterShadowFarDistance`: **800**
- `FIntRenderAmbientOcclusionSamples`: 64, intensity 1, radius 4
- `FFlagRenderShadowCulling: true` added
- Render distance: **200000**
- Grass max distance: **800**, strands **150**
- Threads raised to **16**
- Texture compositor jobs raised to **8**
- CSG LOD: 4000/10000/15000/20000

---

### V5.2
**File:** `Visual Flags/Archive/cinematic_v5.2_LATEST.json`
**Status:** Archived — last version before V6 naming transition

Changes from V5.1 (verified from file):
- `FIntRenderShadowmapBias` / `DFIntRenderShadowmapBias`: 0 → **-150** — first bias tuning in the project
- `FIntRenderShadowmapCascadeDistribution`: 3 → **2**
- `FIntRenderShadowNearPlane`: 1 → **0**
- `FIntDebugForceMSAASamples` / `FIntDebugFRMOptionalMSAALevelOverride`: 4 → **8**
- `DFFlagTextureQualityOverrideEnabled: true` / `DFIntTextureQualityOverride: 3` added
- AO intensity held at **2** (unchanged from V5.1)
- Char shadow resolution held at **4096** (unchanged from V5.1)
- Char shadow far distance held at **500** (unchanged from V5.1)
- Anisotropy held at **16** (unchanged from V5.1)

---

### V5.1
**File:** `Visual Flags/Archive/cinematic_v5.1.json`
**Status:** Archived

Changes from V5 (verified from file):
- `FIntRenderShadowmapCascadeDistribution`: 5 → **3**
- `FIntRenderAmbientOcclusionSamples`: 16 → **32**
- `FIntRenderAmbientOcclusionRadius`: 2 → **3**
- `FIntRenderAnisotropy` / `FIntRenderAnisotropyLevel`: **16** added — first anisotropic filtering in the project
- MSAA held at **4x** (unchanged)
- AO intensity held at **2** (unchanged)
- Bias still **0** (unchanged)
- `FIntRenderShadowNearPlane`: **1** (unchanged)

---

### V5
**File:** `Visual Flags/Archive/cinematic_v5.json`
**Status:** Archived

Changes from V4 (verified from file):
- `FIntRenderShadowmapCascadeDistribution`: 10 → **5**
- `FIntRenderAmbientOcclusionSamples`: 32 → **16**
- `FIntRenderAmbientOcclusionRadius`: 3 → **2**
- `FIntRenderAmbientOcclusionIntensity`: 5 → **2**
- `FIntRenderCharacterShadowFarDistance`: 200 → **500**
- No anisotropy flags yet
- Bias still **0**
- MSAA still **4x**
- `FIntRenderShadowNearPlane`: **1**

Note: `DFIntRenderClampRoughnessMax: -640000000`, GI, IBL, reflections transparency, and global wind all present from this version onward — carried through from V1.

---

### V4
**File:** `Visual Flags/Archive/cinematic_v4_reconstructed.json`
**Status:** Archived — reconstructed

Changes from V3 (verified from file):
- `FIntRenderShadowmapCascadeDistribution`: 7 → **10**
- `FIntRenderCharacterShadowResolution`: 2048 → **4096**
- `FFlagRenderCharacterShadowsHighQuality: true` added
- AO intensity: **5** (unchanged)
- Shadow intensity: **7** (unchanged)
- Char shadow far distance: **200** (unchanged)
- Bias still **0**

---

### V3
**File:** `Visual Flags/Archive/cinematic_v3_reconstructed.json`
**Status:** Archived — reconstructed

Changes from V2 (verified from file):
- `FIntRenderShadowmapCascadeDistribution`: 8 → **7**
- `FIntRenderShadowmapNearClip` removed
- `FFlagRenderCharacterShadowsHighQuality: true` added
- Char shadow resolution: **2048** (unchanged)
- AO intensity: **5** (unchanged)
- Shadow intensity: **7** (unchanged)
- Bias still **0**

---

### V2
**File:** `Visual Flags/Archive/cinematic_v2_reconstructed.json`
**Status:** Archived — reconstructed

Changes from V1 (verified from file):
- `FIntRenderShadowmapCascadeDistribution` overridden to **8** via duplicate flag entry at bottom of file (main entry still 10, override wins)
- `FIntRenderShadowmapNearClip: 1` added — removed in V3
- `FFlagRenderCharacterShadowsHighQuality` not yet present
- AO intensity: **5** (unchanged)
- Shadow intensity: **7** (unchanged)
- Char shadow resolution: **2048** (unchanged)
- Bias still **0**

---

### V1 — Earliest Recovered Build
**File:** `Visual Flags/Archive/cinematic_v1_earliest_stable.json`
**Status:** Archived — earliest recovered build

First stable cinematic config after V0.9b. FIB3 pipeline fully established. Aggressive early values across the board:

| Flag | Value |
|---|---|
| `FIntRenderShadowIntensity` | 7 |
| `FIntRenderShadowmapSize` / Resolution | 8192 |
| `FIntRenderShadowmapBias` / `DFIntRenderShadowmapBias` | 0 |
| `FIntRenderShadowmapCascadeDistribution` | 10 |
| `FIntRenderShadowmapMaxDistance` | 300 |
| `FIntRenderShadowNearPlane` | 1 |
| `FIntRenderAmbientOcclusionSamples` | 32 |
| `FIntRenderAmbientOcclusionRadius` | 3 |
| `FIntRenderAmbientOcclusionIntensity` | 5 |
| `FIntRenderCharacterShadowResolution` | 2048 |
| `FIntRenderCharacterShadowFarDistance` | 200 |
| `FFlagRenderCharacterShadowsHighQuality` | not present |
| `FIntRenderDistance` | 150000 |
| `FIntDebugForceMSAASamples` | 4 |
| `FIntRenderAnisotropy` / `FIntRenderAnisotropyLevel` | not present |
| `DFIntRenderClampRoughnessMax` | -640000000 |
| Threads | 12 |
| Grass max distance | 500 |
| CSG LOD | 5000/10000/15000/20000 |

`FFlagRenderEnableGlobalIllumination`, `FFlagEnableOutdoorAmbientIBL`, `FFlagEnableReflectionsTransparency`, `FFlagGlobalWindRendering`, and `FFlagGlobalWindActivated` all present from this version.

---

### V0.9b — Origin
**File:** `Visual Flags/Archive/Remarkably Beautiful V0.9b (Stable - Archived).json`
**Status:** Archived — origin build

The config that started everything. Built for stable 60fps cinematic recording on a GTX 1650. Considerably more conservative than V1 — no GI, no IBL, no reflection stack, no AO, no roughness floor:

| Flag | Value |
|---|---|
| `FIntRenderShadowIntensity` | 3 |
| `FIntRenderShadowmapSize` | 2048 |
| `FIntRenderShadowmapBias` | 0 |
| `FIntRenderShadowmapCascadeDistribution` | not set |
| `FIntRenderAmbientOcclusion*` | not present |
| `FIntRenderCharacterShadowResolution` | not set |
| `DFIntRenderClampRoughnessMax` | not set |
| `FFlagRenderEnableGlobalIllumination` | not present |
| `FFlagEnableOutdoorAmbientIBL` | not present |
| `FFlagEnableReflectionsTransparency` | not present |
| `FIntRenderDistance` | 100000 |
| `FIntDebugForceMSAASamples` | 4 |
| `FIntRenderAnisotropy` | not present |
| `DFIntTextureCompositorActiveJobs` | 2 |
| Threads | 12 |
| Grass max distance | 300 |
| CSG LOD | 1000/2000/3000/4000 |

The shadow pixelation and pooling issues that drove the entire V1–V6 iteration were first identified comparing this config against early cinematic results on the same map.

---

## FPS Preset — Version History

### Current — Stupid FPS Multi Client
**File:** `Networking + Physics Flags/Stupid FPS multi client.json`
**Status:** Stable

Built on top of Zukis Complete Comp Overhaul. Changes made:
- Thread counts raised from 14 → **16**
- GPU/CPU culling stack added
- Memory management block added
- Networking + physics block added (confirmed working flags only)
- Dead physics sender flags excluded after cross-game testing

---

### Zukis Complete Comp Overhaul
**File:** `FPS Presets/Zukis Complete Comp Overhaul.json`
**Status:** Base preset

Original FPS nuke. Key values:
- Voxel lighting, all shadows/atmosphere/PostFX/grass disabled
- Shadow intensity **0**, shadowmap size **64**
- Texture mips skipped **6** levels
- Graphics quality level **1**
- Render distance **10000**
- CSG LOD: 250/500/1000/1500
- Threads: **14**
- FPS target: **1000**

---

### performance_fps.json
**File:** `FPS Presets/performance_fps.json`
**Status:** Archived — predates Comp Overhaul naming, verified from file

Nearly identical to Comp Overhaul. Confirmed unique flags:
- `FFlagEnableQuickGameLaunch: true` — faster game load
- `FFlagDontStartRunServiceForRCC: true` — skips unnecessary service init
- `FIntPlayerMaxExtraCharacters: 0` — no extra character slots
- `FFlagHandleAltEnterFullscreenManually: false` — reduces fullscreen transition overhead

All four carried forward into Stupid FPS Multi Client.

---

## Networking + Physics — Version History

### V1.0 — Current
**Status:** Stable

Flags confirmed working across Midnight Chasers and Sniper Arena. Physics send flags excluded after confirming server-lock on all tested games.

**Network flags:**

| Flag | Value | Notes |
|---|---|---|
| `DFIntConnectionMTUSize` | 1400 | Optimal packet size — confirmed 153ms → 33ms ping on MC |
| `DFIntRakNetResendBufferArrayLength` | 128 | Larger resend buffer for packet loss handling |
| `DFIntRakNetSplitMessageProgressInterval` | 0 | Removes split message progress overhead |
| `DFIntOptimizeSendReceiveQueueSize` | 100 | Queue size optimisation |
| `DFIntNetworkOwnershipPerReceive` | 30 | Ownership updates per receive cycle |
| `DFIntNetworkOwnershipTransferDelay` | 0 | Instant ownership on object entry |
| `DFIntMinimumNetworkOwnershipTransferDelay` | 0 | Delay floor zeroed |

**Physics flags:**

| Flag | Value | Notes |
|---|---|---|
| `DFIntPhysicsReceiveRate` | 60 | Incoming physics processing rate |
| `DFIntPhysicsSolverIterations` | 8 | Default is 4 — higher = more accurate local collision |
| `DFIntPhysicsContactMaxSolverIter` | 8 | Contact point iterations, matched to solver |
| `DFIntPhysicsSimBodyInterpolationFactor` | 0 | Removes artificial position smoothing |
| `DFIntMaximumFrequencyForMechanismSleep` | 2 | Min frequency before physics sleep allowed |
| `DFIntSimulationThrottleAdjustTime` | 0 | Disables throttle adjustment delay |
| `DFIntMaxMissedWorldStepsRemembered` | 120 | Missed physics steps held for reconciliation |

**Culling stack:**

| Flag | Value |
|---|---|
| `FFlagRenderCPUCullGeometry` | true |
| `FFlagRenderCullGeometryExact` | true |
| `FFlagRenderCPUShadowCull` | true |
| `FFlagRenderBatchShadowMaps` | true |
| `FFlagRenderLightGridEfficientCull` | true |
| `FFlagEnableBatchedSubmitQueue` | true |
| `FFlagGPUBasedOcclusionCulling` | true |
| `FFlagRenderUseOcclusionQueries` | true |
| `DFIntGPUOcclusionCullingMinScreenSize` | 50 |
| `FIntRenderMaxOpaqueDrawcalls` | 4096 |
| `FIntRenderMaxTransparentDrawcalls` | 2048 |

**Memory management:**

| Flag | Value |
|---|---|
| `DFIntTextureCompositorMemoryBudgetMB` | 512 |
| `DFIntNumAssetsMaxToPreload` | 500 |
| `DFIntAssetPreloadingMaxAllowedMB` | 512 |
| `DFIntSoundMaxChannels` | 32 |
| `DFIntAnimationThrottleMaxElapsed` | 0 |

---

## Flag Reference — Reforged V6.4a

### Lighting Engine

| Flag | Value | Notes |
|---|---|---|
| `FFlagDebugForceFutureIsBrightPhase3` | true | Full FIB3 pipeline. Only one phase flag true at a time — multiple active causes renderer fallback or corruption. |
| `FFlagDebugForceFutureIsBrightPhase2` | false | Must be false. |
| `FFlagDebugForceFutureIsBrightPhase1` | false | Must be false. |
| `DFFlagDebugRenderForceTechnologyVoxel` | false | Must be false for FIB3. |
| `FFlagNewLightAttenuation` | true | Physically accurate light falloff. Required for FIB3 local lights to behave correctly. |
| `FFlagFastGPULightCulling3` | true | GPU culls non-contributing lights before shading. Performance gain, no visual cost. |
| `FFlagRenderLocalLightShadows` | true | Local lights cast shadows. |

### Shadows

| Flag | Value | Notes |
|---|---|---|
| `FIntRenderShadowIntensity` | 4 | Confirmed sweet spot. Higher causes pooling with AO. |
| `FIntRenderShadowmapSize` | 8192 | Shadowmap texture size — maximum. |
| `FIntRenderShadowmapResolution` | 8192 | Shadowmap render resolution — maximum. |
| `FIntRenderShadowmapBias` | -150 | Pulls shadow edges to geometry. Both bias flags must match. |
| `DFIntRenderShadowmapBias` | -150 | Dynamic version. Must match static. |
| `FFlagRenderShadowHighQuality` | true | Forces HQ shadow path. |
| `FFlagRenderShadowCulling` | true | Culls offscreen shadow casters. |
| `FFlagRenderShadowmapPrepass` | true | Depth prepass before shadowmap — sharpens edges. |
| `FIntRenderShadowFadeRange` | 200 | Smooth fade at max shadow distance. |
| `FIntRenderShadowmapCascadeCount` | 4 | Maximum cascade splits. |
| `FIntRenderShadowmapCascadeDistribution` | 2 | Confirmed stable. 3+ breaks flat ground precision. |
| `FIntRenderShadowmapMaxDistance` | 500 | Shadow render distance. |
| `FIntRenderShadowmapMinDistance` | 0 | |

### Character Shadows

| Flag | Value | Notes |
|---|---|---|
| `FFlagRenderCharacterShadows` | true | |
| `FFlagRenderCharacterShadowsHighQuality` | true | |
| `FIntRenderCharacterShadowResolution` | 2048 | Intentionally not higher. 8192 produces a hard black blob on flat surfaces — confirmed across multiple scenes. |
| `FIntRenderCharacterShadowFarDistance` | 800 | |
| `FIntRenderCharacterShadowNearDistance` | 0 | |

### Ambient Occlusion

| Flag | Value | Notes |
|---|---|---|
| `FFlagRenderAmbientOcclusion` | true | |
| `FFlagRenderAmbientOcclusionInner` | true | Inner AO for tight geometry contact. |
| `FIntRenderAmbientOcclusionSamples` | 64 | Clean without noise at this count. |
| `FIntRenderAmbientOcclusionRadius` | 4 | AO reach from surfaces. |
| `FIntRenderAmbientOcclusionIntensity` | 1 | Kept low — stacks with shadow intensity if raised. |
| `FIntRenderAmbientOcclusionBlurRadius` | 1 | Tight blur, no halos. |

### Reflections

| Flag | Value | Notes |
|---|---|---|
| `DFIntRenderClampRoughnessMax` | -1000000000 | Roughness floor at engine minimum. Primary driver of wet paint and metallic depth. |
| `FFlagEnableReflectionsTransparency` | true | |
| `FFlagRenderReflectionHighQuality` | true | Forces HQ reflection path. |
| `FFlagRenderSkyboxReflections` | true | Skybox contributes to surface reflections. |
| `FFlagRenderEnvironmentReflections` | true | Geometry contributes to reflections. |
| `FIntRenderEnvironmentReflectionSamples` | 64 | Diminishing returns confirmed above 64. |
| `FIntRenderEnvironmentReflectionBias` | -50 | |
| `FFlagRenderCubemapCapture` | true | Per-object local cubemap capture. |
| `FIntRenderCubemapCaptureResolution` | 1024 | Confirmed sweet spot — 8192 costs VRAM with no visible gain. |
| `FIntRenderCubemapCaptureFrequency` | 10 | Update every 10 frames — imperceptible vs every frame. |
| `FIntRenderSkyboxCubeMapSize` | 2048 | Skybox environment map for IBL. |
| `FIntRenderIBLResolution` | 2048 | Image-based lighting resolution. |
| `FFlagRenderSpecularMap` | true | |
| `FIntRenderSpecularIntensity` | 200 | 300 causes blowout in direct sun — 200 confirmed safe. |
| `FIntRenderFresnelIntensity` | 150 | Fresnel on panel curves. 250 causes blowout on light paint. |

### Global Illumination & Atmosphere

| Flag | Value | Notes |
|---|---|---|
| `FFlagRenderEnableGlobalIllumination` | true | GI bounce lighting. Present from V1 onward. |
| `FFlagEnableOutdoorAmbientIBL` | true | Skybox as outdoor ambient source. Present from V1 onward. |
| `FFlagRenderAtmosphere` | true | Atmospheric scattering. |
| `FFlagRenderSkyUseUpdatedSkyColor` | true | Accurate sky colour for IBL. |
| `FFlagRenderSkyboxCubeMapResolution` | true | |

### Wind

| Flag | Value |
|---|---|
| `FFlagGlobalWindRendering` | true |
| `FFlagGlobalWindActivated` | true |

### Quality & Performance

| Flag | Value | Notes |
|---|---|---|
| `FIntDebugGraphicsQualityOverride` | 21 | Maximum quality. |
| `DFIntDebugFRMQualityLevelOverride` | 21 | Prevents FRM auto-degrading. |
| `DFIntRenderQualityLevel` | 21 | Third quality lock — added V6.4a. |
| `FFlagFixGraphicsQuality` | true | Prevents quality drop under load — added V6.4a. |
| `FIntDebugForceMSAASamples` | 8 | 8x MSAA — set from V5.2 onward. |
| `FIntDebugFRMOptionalMSAALevelOverride` | 8 | |
| `FIntRenderAnisotropy` / `FIntRenderAnisotropyLevel` | 16 | Maximum anisotropic filtering — added V5.1. |
| `FIntRenderDistance` | 200000 | |
| `FFlagRenderNoLod` | true | Full quality at all distances. |
| `DFIntTaskSchedulerTargetFps` | 9999 | Effectively uncapped — raised in V6.4a. |
| `FFlagTaskSchedulerLimitTargetFpsTo240` | false | |
| `FFlagDebugGraphicsPreferD3D11` | true | |
| `FFlagDebugGraphicsPreferD3D11FL10` | false | Prevents reduced feature level fallback — added V6.4a. |
| `FFlagRenderHiDpi` | true | Native DPI rendering — added V6.4a. |
| `FFlagRenderValidateMeshes` | false | Skips per-frame mesh validation — added V6.4a. |
| `FIntRuntimeMaxNumOfThreads` | 16 | |
| `FIntTaskSchedulerThreadCount` | 16 | |

### Textures & Terrain

| Flag | Value |
|---|---|
| `FFlagPreloadTextureData` | true |
| `FIntDebugTextureManagerSkipMips` | 0 |
| `FIntTerrainArraySliceSize` | 8 |
| `DFIntTextureCompositorActiveJobs` | 8 |
| `DFFlagTextureQualityOverrideEnabled` | true |
| `DFIntTextureQualityOverride` | 3 |

### Grass

| Flag | Value |
|---|---|
| `FIntGrassMovementReducedMotionFactor` | 15 |
| `FIntFRMMaxGrassDistance` | 800 |
| `FIntFRMMinGrassDistance` | 50 |
| `FIntRenderGrassDetailStrands` | 150 |
| `FIntRenderGrassHeightScaler` | 100 |

### CSG LOD

| Flag | Value |
|---|---|
| `DFIntCSGLevelOfDetailSwitchingDistance` | 4000 |
| `DFIntCSGLevelOfDetailSwitchingDistanceL12` | 10000 |
| `DFIntCSGLevelOfDetailSwitchingDistanceL23` | 15000 |
| `DFIntCSGLevelOfDetailSwitchingDistanceL34` | 20000 |

---

## Flag Reference — Stupid FPS / Comp Overhaul

| Flag | Value | Notes |
|---|---|---|
| `DFFlagDebugRenderForceTechnologyVoxel` | true | Voxel lighting — cheapest available path. |
| `FFlagDebugForceFutureIsBrightPhase3/2/1` | false | All off. |
| `FFlagGraphicsEnableNewShadowTechnology` | false | Modern shadowmaps disabled. |
| `FFlagRenderLocalLightShadows` | false | Local light shadows off. |
| `FIntRenderShadowIntensity` | 0 | Zeroed. |
| `FIntRenderShadowmapSize` | 64 | Minimum. |
| `FFlagDisablePostFX` | true | All post-processing off. |
| `FFlagRenderAtmosphere` | false | Atmosphere off. |
| `FFlagDebugSkyGray` | true | Flat grey sky — no skybox cost. |
| `FIntDebugTextureManagerSkipMips` | 6 | 6 mip levels skipped — major VRAM saving. |
| `FIntDebugGraphicsQualityOverride` | 1 | Minimum quality. |
| `FIntRenderDistance` | 10000 | Short distance. |
| `FFlagRenderNoLod` | false | LOD active. |
| `FIntGrassMovementReducedMotionFactor` | 100 | Motion fully reduced. |
| `FIntFRMMaxGrassDistance` / `FIntFRMMinGrassDistance` | 0 | Grass disabled. |
| `FIntRenderLocalLightUpdatesMax` | 4 | Minimal local light updates. |
| `FIntRenderLocalLightUpdatesMin` | 2 | |
| `FIntRenderLocalLightFadeStartDistance` | 1 | Lights fade out immediately. |
| `FIntRenderLocalLightFadeEndDistance` | 1 | |
| `DFIntTaskSchedulerTargetFps` | 1000 | High target. |
| `FFlagTaskSchedulerLimitTargetFpsTo240` | false | |
| `FIntTaskSchedulerThreadCount` | 14 / 16 | 14 in Comp Overhaul and performance_fps, 16 in Stupid FPS. |
| `FIntRuntimeMaxNumOfThreads` | 14 / 16 | Same. |
| `FFlagEnableQuickGameLaunch` | true | From performance_fps, carried forward. |
| `FFlagDontStartRunServiceForRCC` | true | From performance_fps, carried forward. |
| `FIntPlayerMaxExtraCharacters` | 0 | From performance_fps, carried forward. |
| `FFlagHandleAltEnterFullscreenManually` | false | From performance_fps, carried forward. |
| `DFIntCSGLevelOfDetailSwitchingDistance` | 250 | Aggressive LOD switching. |
| `DFIntCSGLevelOfDetailSwitchingDistanceL12` | 500 | |
| `DFIntCSGLevelOfDetailSwitchingDistanceL23` | 1000 | |
| `DFIntCSGLevelOfDetailSwitchingDistanceL34` | 1500 | |

---

## Flag Reference — Networking + Physics

See the tables in [Networking + Physics — Version History](#networking--physics--version-history) above for the full flag list with values and notes.

---

## Test Methodology

All flags tested in live Roblox sessions using in-game debug overlays only. No external tools.

**Test environments:**
- Midnight Chasers (City map) — open world driving, night/day cycle, nature scenes
- Midnight Chasers (Boneville Salt Flats) — flat open terrain, direct sunlight, car paint and shadow testing
- Sniper Arena — competitive FPS, indoor map, 7-8 players active
- Nature test place — FIB3 lighting verification

**Monitoring:** Shift+F1 (network), Shift+F2 (full debug stats) for all before/after comparisons.

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

Thread counts in visual presets are set to 16. FPS presets are 16 (Stupid FPS) or 14 (Comp Overhaul / performance_fps). Adjust `FIntTaskSchedulerThreadCount` and `FIntRuntimeMaxNumOfThreads` to match your CPU if needed.

---

## Confirmed Dead Flags

Tested and confirmed to have no effect. Do not add them back.

| Flag | Reason |
|---|---|
| `DFIntS2PhysicsSenderRate` | Outgoing physics send rate is platform-locked server-side. Confirmed zero effect across Midnight Chasers and Sniper Arena. Out physics packets stay near zero regardless of this flag on every game tested. |
| `DFIntNetworkRunPhysicsSend` | Same — platform-locked server-side. |
| `FIntRenderReflectionResolution` | Breaks SSR above the engine's internal ceiling. Any value causes SSR to fail. Tested at 2048 in V6.1 and 8192 in V6.3.1P — both broke reflections. Dropped after V6.2, never returning. |
