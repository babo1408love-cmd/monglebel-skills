---
name: monglebel-art-style
description: "MongeulBell visual art style guide. Cute heart-pounding tower defense aesthetic -- round shapes, pastel colors, toy-like warmth. Use when creating UI, characters, environments, VFX, or any visual assets for MongeulBell."
argument-hint: "[asset type or visual question]"
metadata:
  author: MongeulBell
  version: 1.0.0
  tags: [art, style, visual, ui, cute, pastel, tower-defense, monglebel]
  model: opus
---

# MongeulBell Art Style Guide -- "Two-geun Two-geun Tower" Style

MongeulBell's visual identity is **"cute heart-pounding tower defense"**.
The world feels like a magical toy set come to life -- warm, round, sparkling, and full of gentle excitement.

---

## 1. Core Emotion Keywords

| Korean | English | Description |
|--------|---------|-------------|
| 두근두근 | Heart-pounding | Gentle excitement, anticipation, not fear |
| 귀여움 | Cute | Round, soft, endearing |
| 포근함 | Cozy/Warm | Feels like being wrapped in something soft |
| 반짝임 | Sparkle | Magical particles, gentle glow |
| 동화적 | Storybook-like | Fairytale atmosphere, illustrated feel |
| 장난감 같은 | Toy-like | Miniature world, diorama feeling |
| 희망적 | Hopeful | Even dark moments feel recoverable |

**One-line summary**: "A warm storybook diorama where cute spirits defend a sparkling castle from sad, corrupted creatures."

---

## 2. Color Palette Direction

### Primary Palette (Base World)
- **Warm pastels**: Soft mint, cream yellow, rose pink, lavender blue
- **World Tree greens**: Fresh spring green, sage, warm olive
- **Sky**: Gradient from soft blue to peach at horizon
- **Gold accents**: Warm gold for rewards, sparkles, highlights

### Element Colors (5-Element Cycle)
| Element | Primary | Accent | Feel |
|---------|---------|--------|------|
| Water | Soft sky blue | White foam | Gentle, flowing |
| Fire | Warm coral/orange | Golden yellow | Cozy campfire, not violent |
| Wind | Mint green | Silver sparkle | Breezy, light |
| Earth | Warm brown/amber | Moss green | Sturdy, grounded |
| Light | Cream gold | White radiance | Pure, hopeful |

### Corruption Palette (Enemies/Spores)
- **NOT**: Black, blood red, toxic green, neon purple
- **YES**: Muted purple-gray, dusty violet, clouded teal
- Corruption looks **sad and tired**, not evil and scary
- Dark colors always have warm undertones

### UI Palette
- **Backgrounds**: Warm cream, soft parchment
- **Borders**: Rounded, warm brown or gold
- **Buttons**: Soft gradients, never sharp/flat
- **Text**: Dark warm brown (not pure black)
- **Highlights**: Soft glow, not hard neon

---

## 3. Shape Language

### Characters & Spirits
- **Round everything**: Round heads, round bodies, round paws
- **Big eyes**: Expressive, sparkly, large relative to face
- **Small mouths**: Simple, cute expressions
- **Soft proportions**: Chibi-adjacent (2-3 head-to-body ratio)
- **No sharp edges**: Even armor/weapons have rounded edges
- **Size**: Spirits are smaller than human characters

### Towers
- **Organic + architectural blend**: Stone/wood with living vines/flowers
- **Round tops**: Domed, mushroom-cap, or flower-shaped tops
- **Warm materials**: Wood, smooth stone, crystal, living plants
- **Glowing elements**: Soft inner light showing active state
- **Height variety**: Short/medium/tall for visual hierarchy
- **NOT**: Military bunkers, industrial metal, cold concrete

### Castle & Walls
- **Storybook castle**: Round turrets, warm stone, ivy/flowers growing
- **Walls feel alive**: Not cold fortifications but living barriers
- **Internal stairs**: Clearly visible, inviting, well-lit
- **Muster area**: Courtyard with greenery, feels safe
- **Board/Command panel**: Tabletop game aesthetic, carved into wood/stone

### Enemies (Spore-Infected Creatures)
- **Cute-sad, not scary**: Think corrupted forest animals
- **Visual corruption**: Purple-gray patches, droopy features, clouded eyes
- **Still recognizable as creatures**: Not monsters, just sick/sad beings
- **Purification effect**: When cleansed, corruption lifts like fog
- **Size**: Small-medium, mini-boss slightly larger (not towering)

### Environment
- **Diorama feeling**: World looks like a miniature toy set
- **Layered depth**: Clear foreground/midground/background
- **Nature everywhere**: Grass, flowers, trees, vines, water
- **Warm lighting**: Golden hour feel, soft shadows
- **Particles**: Floating pollen, gentle sparkles, soft fireflies

---

## 4. UI Design Rules

### General Principles
- **Round corners on everything** (minimum 12px radius)
- **Soft drop shadows** (not hard, always warm-toned)
- **Gradient backgrounds** (never flat solid colors)
- **Icon style**: Filled, rounded, simple silhouettes
- **Animation**: Bouncy, springy (ease-out-back), never sharp/instant
- **Touch targets**: Minimum 44x44pt for mobile

### Card Design
- **Card shape**: Rounded rectangle with decorative border
- **Card illustration**: Centered, fills ~60% of card face
- **Card frame**: Element-colored border with ornamental corners
- **Cost indicator**: Top-left circle with clear number
- **Card name**: Bottom, warm brown on parchment background
- **Card glow**: Soft outer glow matching element color when active
- **Back**: Unified design with World Tree motif

### HUD (Top Zone ~10%)
- **Minimal info**: Only current-turn essentials
- **Semi-transparent background**: Warm tint, not solid
- **Icons over text**: Use visual icons wherever possible
- **Life display**: Heart icons, not health bars
- **Wave counter**: Soft, friendly number display

### Control Bar (Bottom Zone ~18%)
- **Thumb-friendly**: Large, well-spaced buttons
- **Card hand**: Fan layout, cards overlap slightly
- **Dice area**: Clear, centered dice with roll animation space
- **Spirit buttons**: Circular portraits with cooldown overlay

### Panels & Popups
- **Parchment/scroll aesthetic**: Warm paper texture backgrounds
- **Decorative borders**: Vine/floral frame elements
- **Gentle transitions**: Fade + scale, never hard cut
- **Close button**: Always visible, always round, top-right

---

## 5. Animation & VFX Style

### Character Animation
- **Idle**: Gentle breathing, blinking, small sway
- **Walk**: Bouncy steps, slight bob
- **Attack**: Quick but cute, no violent follow-through
- **Purify effect**: Golden/white particles rising, creature fading gently
- **Spirit return**: Soft glow, floating upward, peaceful

### Tower VFX
- **Projectiles**: Glowing orbs, leaf storms, water drops (not bullets/missiles)
- **Impact**: Soft burst, sparkle, ripple (not explosion)
- **Upgrade**: Growing vines, blooming flowers, crystal formation
- **Active state**: Gentle pulsing glow, rotating elements

### Environmental VFX
- **Wind**: Floating leaves, grass sway
- **Water**: Gentle ripples, sparkle reflection
- **Corruption**: Slow purple-gray mist, wilting nearby plants
- **Purification**: Color returning, flowers blooming, mist clearing
- **Transition (stairs)**: Soft glow trail, floating step particles

### UI Animation
- **Button press**: Scale down 0.95 → bounce back 1.05 → settle 1.0
- **Card play**: Arc trajectory with sparkle trail
- **Dice roll**: Tumble with soft bounce landing
- **Number change**: Count up/down with bounce
- **Panel appear**: Scale from 0.8 → 1.0 with fade

---

## 6. Typography Direction

### Font Style
- **Primary**: Rounded sans-serif (soft, friendly)
- **Headings**: Slightly playful, can be slightly hand-drawn
- **Numbers**: Clear, bold, easy to read at small sizes
- **Korean text**: Round gothic style preferred

### Text Rules
- **Never use**: Angular, military, horror, or serif fonts
- **Size hierarchy**: Clear distinction between title/body/caption
- **Weight**: Medium to Bold preferred (not thin/light)
- **Color**: Warm dark brown, never pure black
- **Stroke/outline**: Soft, warm-colored outline for readability on busy backgrounds

---

## 7. Camera & Composition (3D/2.5D)

### Camera Angle
- **Isometric-ish top-down**: ~45-60 degree angle
- **Slight tilt**: Makes world feel like a diorama/toy set
- **Fixed camera**: No free rotation in VS (maybe zoom only)
- **Portrait orientation**: Taller than wide, world extends vertically

### Composition Rules
- **Castle center**: Castle is the visual anchor
- **Wall zones visible**: Left/Front/Right walls clearly separated
- **Depth layers**: Board (inside) → Courtyard → Stairs → Wall → Outside
- **Enemy approach**: Visible path from outside toward walls
- **Clear readability**: Every gameplay element identifiable at a glance

---

## 8. Asset Checklist for New Content

When creating ANY new visual asset, verify:

- [ ] Shapes are **round**, not angular
- [ ] Colors are **warm/pastel**, not cold/neon
- [ ] Silhouette is **readable** at mobile screen size
- [ ] Style matches **toy/storybook** aesthetic
- [ ] If enemy: looks **sad/sick**, not scary/evil
- [ ] If UI: has **rounded corners** and soft shadows
- [ ] If VFX: uses **soft particles/glow**, not hard explosions
- [ ] Fits portrait **1080x2340** screen
- [ ] A child could see this and feel **safe**
- [ ] Placed next to existing assets, there's **no jarring mismatch**

---

## 9. Reference Art Direction Summary

Think of MongeulBell visuals as a blend of:
- **Animal Crossing** (warm, cozy, round characters)
- **Cookie Run** (cute fantasy, bright colors, sweet atmosphere)
- **Heartopia/두근두근타운** (storybook 3D, healing aesthetic)
- **Clash Royale** (readable tower defense layout -- but cuter and softer)

**NOT** like: Arknights, Path of Exile, Dark Souls, military TD games

---

## 10. Art Production Priority (Vertical Slice)

### Phase 1 - Now (Readability)
- Castle silhouette cleanup
- Wall zone visual separation
- Stairs emphasis
- Slot/placement markers
- Enemy approach lines
- Element color coding

### Phase 2 - Later (Polish)
- Final card illustrations
- Final spirit animations
- Final character art
- Final VFX passes
- Background detail finish
- Prop/decoration detail

**Current phase = Phase 1. Focus on readability, not final beauty.**

End.
