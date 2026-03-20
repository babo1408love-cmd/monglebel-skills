---
name: monglebel-3d-to-25d
description: "MongeulBell 3D-first to 2.5D conversion strategy. Build in full 3D with cel shading, then convert to 2.5D for final mobile release. Use when making 3D modeling decisions, camera setup, shader choices, or planning the 2.5D conversion pipeline."
argument-hint: "[3D/2.5D question or asset type]"
metadata:
  author: MongeulBell
  version: 1.0.0
  tags: [unity, 3d, 2.5d, cel-shading, toon, mobile, pipeline, conversion, monglebel]
  model: opus
---

# MongeulBell 3D-First to 2.5D Conversion Strategy

MongeulBell is built in **full 3D first**, then converted to **2.5D** for the final mobile release.
This is NOT "start 2D then add depth." This is "build real 3D, then lock it into 2.5D presentation."

---

## 1. Why 3D-First?

| Benefit | Description |
|---------|-------------|
| **Reusable assets** | 3D models can be rotated, relit, reused in trailers/marketing |
| **Easier iteration** | Change camera angle, lighting, layout without redrawing |
| **Better depth** | Real shadows, real occlusion, real parallax |
| **Cel shading** | Toon shader gives 2D-like feel with 3D flexibility |
| **Future-proof** | If game grows, 3D assets are ready for more complex scenes |
| **Consistent style** | One 3D model = consistent from every angle |

---

## 2. The Pipeline Overview

```
[Phase 1: Full 3D Development]
   Model in 3D → Rig → Animate → Apply Cel/Toon Shader
   Set up 3D scene with proper lighting
   Gameplay works fully in 3D
   Camera: Free (for development/debugging)

        ↓

[Phase 2: Lock to 2.5D Presentation]
   Lock camera to fixed isometric angle (~45-60°)
   Portrait orientation (1080x2340)
   Disable camera rotation for players
   Optimize: LOD, texture atlas, draw call batching
   Bake shadows where possible
   Sprite fallback for distant/small objects if needed

        ↓

[Phase 3: Mobile Optimization]
   Profile on target devices
   Reduce overdraw
   Atlas textures
   Pool objects
   Compress textures (ASTC/ETC2)
   Target 30-60 FPS on mid-range phones
```

---

## 3. 3D Modeling Rules for MongeulBell

### Poly Count Guidelines
| Asset Type | Target Polycount | Notes |
|-----------|-----------------|-------|
| Spirit (character) | 1,500 - 3,000 tris | Main focus, can be higher |
| Tower | 1,000 - 2,500 tris | Visible but stationary |
| Enemy | 800 - 2,000 tris | Many on screen at once |
| Castle/Wall section | 2,000 - 5,000 tris | Large but LOD-able |
| Board tile | 200 - 500 tris | Many tiles, keep simple |
| Prop/decoration | 100 - 500 tris | Background detail |
| Projectile | 50 - 200 tris | Small, fast-moving |

### Shape Rules (matches art style)
- **Round everything**: Beveled edges, no hard 90-degree corners
- **Soft silhouettes**: Models should read as cute even in shadow
- **Minimal UV seams**: Clean, simple UV layouts
- **Consistent scale**: Establish unit scale early (1 Unity unit = 1 meter)
- **Modular pieces**: Castle walls, floor tiles, props should snap together

### Material Rules
- **Maximum 1-2 materials per model** (for draw call efficiency)
- **Texture atlas**: Group related assets on shared texture sheets
- **Flat color regions**: Large areas of solid color work best with cel shading
- **Gradient ramps**: Use custom ramp textures for toon lighting
- **NO complex PBR**: No metallic/roughness maps needed for toon style

---

## 4. Cel/Toon Shading Setup

### Shader Approach
MongeulBell uses **cel shading** (toon shading) to make 3D look like 2D illustration.

### Required Shader Features
```
Cel Shading Checklist:
[x] 2-3 step lighting (shadow/midtone/highlight)
[x] Custom shadow color (warm purple/brown, NOT gray/black)
[x] Outline pass (screen-space or inverted hull)
[x] Outline color per-object or per-material
[x] Rim light option (soft glow on edges)
[x] Emission support (for glowing elements)
[x] Transparency/cutout support (for VFX)
[x] Shadow receiving (keep world grounded)
```

### Recommended Shader Options (Unity)
1. **URP Toon Shader** (custom): Best control, MongeulBell-specific
2. **Toony Colors Pro 2** (Asset Store): Production-ready toon shader
3. **Custom ShaderGraph**: Visual, editable, team-friendly

### Lighting Rules
- **Single directional light**: Main sun, warm tone
- **Shadow color**: Warm purple or warm brown (NEVER cool gray)
- **Ambient**: Warm fill from below/sides
- **Point lights**: Minimal, only for special effects (tower glow, magic)
- **Light probes**: Place around castle for consistent indirect lighting
- **NO realtime GI**: Too expensive for mobile

### Outline Settings
- **Width**: 1-3 pixels at 1080p (thicker = more cartoon-like)
- **Color**: Dark warm brown or object-specific dark shade
- **Distance fade**: Outlines thin or disappear at distance
- **Character outlines**: Slightly thicker than environment

---

## 5. Camera Setup for 2.5D Lock

### Development Camera (Phase 1)
- Free-look camera for debugging and development
- Can orbit, zoom, pan
- Helper overlay showing final 2.5D frame

### Production Camera (Phase 2 - 2.5D Lock)
```
Camera Settings:
- Type: Orthographic (or tight perspective FOV 20-30)
- Angle: 45-60 degrees from horizontal
- Rotation: Fixed (no player rotation)
- Portrait: 1080 x 2340 (19.5:9)
- Zoom: Fixed or 2-level zoom (overview / close-up)
```

### Why Orthographic?
- No perspective distortion at screen edges
- Consistent scale across the play area
- Easier to align UI overlays
- Classic 2.5D/isometric feel
- Better for mobile touch targeting

### Camera Composition
```
┌─────────────────────┐
│   Top HUD (10%)     │
├─────────────────────┤
│                     │
│   Castle Interior   │
│   (Board/Command)   │
│                     │
│   ─── Courtyard ─── │
│                     │
│   ←Wall  Wall Wall→ │
│                     │
│   Enemy Approach    │
│                     │
├─────────────────────┤
│  Controls (18%)     │
└─────────────────────┘
```

### Depth Layers (back to front)
1. Sky / far background
2. Distant mountains/trees
3. Castle exterior back wall
4. Castle interior (board area)
5. Courtyard / muster area
6. Internal stairs
7. Castle walls (left/front/right)
8. Enemy approach zone
9. VFX / particles (overlay)
10. UI

---

## 6. Animation Pipeline

### 3D Animation Rules
- **Skeleton**: Simple bone setup (15-25 bones for characters)
- **Animation style**: Snappy, slightly exaggerated (cartoon timing)
- **Blend shapes**: Optional for facial expressions (can use texture swap)
- **IK**: Minimal, only for ground alignment if needed
- **Animation clips**: Separate clips per action (idle, walk, attack, etc.)

### 2.5D Considerations
- Animations only need to look good from the **locked camera angle**
- Back-facing animations can be simplified (player never sees model's back fully)
- Walk cycles: 4-directional is usually enough
- Attack animations: Design for the primary viewing angle

### Optimization
- **Animation compression**: Keyframe reduction in Unity
- **Shared skeletons**: Same-type enemies share skeleton
- **GPU instancing**: Use for repeated elements (enemies, projectiles)

---

## 7. Environment Construction

### Modular Approach
Build the castle and world from **snap-together 3D pieces**:

```
Castle Pieces:
- Wall segments (straight, corner, gate)
- Tower bases (round, square)
- Floor tiles (stone, grass, wood)
- Stair segments (straight, curved)
- Roof pieces (dome, pointed-round)
- Decoration slots (flower pot, banner, torch)
```

### Terrain
- **NOT Unity Terrain**: Too heavy for mobile
- **Mesh-based ground**: Simple planes with painted vertex colors
- **Grass/plants**: Billboard sprites or very low-poly meshes
- **Water**: Simple scrolling UV shader, no realtime reflections

### Background
- **Skybox**: Gradient or painted sky (not photo-realistic)
- **Far elements**: 2D sprites on planes (mountains, clouds, trees)
- **Parallax**: Slight movement difference between layers for depth feel

---

## 8. Mobile Optimization Checklist

### Rendering
- [ ] **Draw calls**: Target < 100 per frame
- [ ] **Triangles**: Target < 100K visible per frame
- [ ] **Overdraw**: Minimize transparent overlaps
- [ ] **Resolution**: Render at 0.75-1.0x native, upscale if needed
- [ ] **Shadow resolution**: Low (512-1024) or baked
- [ ] **Anti-aliasing**: FXAA or none (MSAA too expensive)

### Textures
- [ ] **Atlas**: Group small textures into atlases
- [ ] **Compression**: ASTC 4x4 (iOS+Android) or ETC2 (Android)
- [ ] **Max size**: 1024x1024 for characters, 512x512 for props
- [ ] **Mipmaps**: Enable for 3D objects, disable for UI

### Memory
- [ ] **Object pooling**: All enemies, projectiles, VFX
- [ ] **Addressables**: Load/unload assets on demand
- [ ] **Texture streaming**: If supported
- [ ] **Target**: < 300MB total memory

### Performance
- [ ] **Target FPS**: 30 stable, 60 ideal
- [ ] **Battery**: Consider thermal throttling
- [ ] **Loading**: < 5 seconds to gameplay
- [ ] **Target devices**: 3-4 year old mid-range phones

---

## 9. 2.5D Conversion Checklist

When transitioning from 3D development to 2.5D locked presentation:

### Camera Lock
- [ ] Fix camera angle to chosen isometric view
- [ ] Lock rotation (no player camera control)
- [ ] Set orthographic or tight perspective
- [ ] Frame composition matches portrait 1080x2340
- [ ] All gameplay elements visible without scrolling (or with controlled scroll)

### Visual Polish
- [ ] Models look good from locked angle (no ugly back-faces)
- [ ] Shadows read well from fixed view
- [ ] Outlines are correct thickness at final resolution
- [ ] Colors and lighting feel cohesive
- [ ] Depth is clearly readable (what's in front/behind)

### Optimization Pass
- [ ] Remove unseen geometry (faces never visible from locked angle)
- [ ] Reduce LOD for distant objects more aggressively
- [ ] Bake lighting where possible
- [ ] Replace distant 3D objects with sprite billboards
- [ ] Profile on target mobile devices

### Gameplay Verification
- [ ] Touch targets align with visual positions
- [ ] Slot positions are clearly tap-able
- [ ] Card/dice UI doesn't occlude important game elements
- [ ] Enemy paths are visually clear
- [ ] Tower placement preview works at locked angle

---

## 10. Tool Recommendations

### 3D Modeling
- **Blender** (free): Primary modeling tool
- Export: FBX format to Unity
- Scale: Apply scale before export (1 unit = 1 meter)

### Texturing
- **Substance Painter** or **3D Coat**: For complex textures
- **Photoshop/Clip Studio**: For simple flat-color textures
- **Gradient ramp textures**: Can be made in any image editor

### Unity Packages
- **Cinemachine**: Camera management (already imported)
- **DOTween** (Free): Animation tweening (already imported)
- **URP**: Render pipeline for mobile (if not already using)
- **Shader Graph**: Visual shader creation
- **Post Processing**: Bloom, color grading (light use only)

### Workflow
```
Blender (model) → FBX export → Unity import
                                    ↓
                          Apply toon material
                                    ↓
                          Set up in scene
                                    ↓
                          Test at locked camera angle
                                    ↓
                          Iterate if needed
```

---

## 11. Common Mistakes to Avoid

| Mistake | Why It's Bad | What to Do Instead |
|---------|-------------|-------------------|
| Making models too detailed | Wasted tris, bad mobile perf | Low-poly + cel shade |
| Using realistic PBR materials | Doesn't match toon style | Use flat/ramp toon shader |
| Free camera for players | Breaks 2.5D illusion, shows ugly angles | Lock camera, design for one angle |
| Gray/black shadows | Feels cold, doesn't match cute style | Warm purple/brown shadows |
| Too many lights | Expensive, hard to control | One directional + ambient |
| Photo-realistic textures | Style mismatch | Painted/flat color textures |
| Building only for 2D from start | Loses 3D benefits (reuse, flexibility) | Build 3D, present 2.5D |
| Ignoring mobile perf until late | Painful optimization later | Profile regularly from Phase 1 |

---

## 12. Summary

**MongeulBell Development Pipeline**:
1. **Build in 3D** with cel/toon shading from day one
2. **Design for the locked angle** but keep full 3D flexibility
3. **Lock to 2.5D** when ready for production/release
4. **Optimize for mobile** throughout, not just at the end

**Key principle**: The 3D is the source of truth. The 2.5D presentation is just a camera lock + optimization pass. Never throw away the 3D — it's your most flexible asset.

End.
