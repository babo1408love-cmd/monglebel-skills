# 00. MongeulBell Project Golden Rules v1

This document is the **Active Reference Document #1** for MongeulBell.
Any AI, developer, or automation tool must read and follow this document first.

---

## 0. Usage Principles

1. This document is **Active Reference #1 (highest priority)**.
2. `01_MongeulBell_Engineering_SOLID_AI_GoldenRules_v1.md` is **Active Reference #2**.
3. Other existing docs go to `90_Archive/` -- never delete.
4. Conflict resolution order:
   - Priority 1: This document
   - Priority 2: Engineering SOLID document
   - Priority 3: Current scene/data/code state
   - Priority 4: Archive documents
5. Archive docs are reference only, never override current direction.

---

## 1. Absolute Project Identity

MongeulBell is a **portrait mobile 2D hybrid strategy game**.
Player moves on World Tree board, encounters Spore Gates, uses cards/dice/spirits/defense structures to purify corruption.

Main identity (exactly three):
- Board movement
- Tower defense-style defense battle
- Strategic choices via cards and dice

Sub-modes may exist but the game's face is always **Board + Defense Battle**.

---

## 2. Current Stage Goal

Current goal: **Vertical Slice**, not 1.0 release.

Vertical Slice definition:
- Small but actually playable
- Feels high-quality
- Game identity understood at a glance
- Higher consistency than feature count

### VS Scope
- 1 region
- Portrait mobile screen
- Castle interior board movement
- Wall-top defense battle
- 8 cards
- 2 tower types
- 2 spirit types
- 3 enemy types + 1 mini-boss
- Basic save/load
- Basic home/growth/retry flow
- Simple FTUE tutorial

### Currently Excluded
- PvP, Guilds, Season Pass, Live Events
- Forced ads, Paid random gacha
- Multiplayer, Large-scale server architecture

---

## 3. Absolute Unchangeable Rules

1. Enemies are **purified**, not killed
2. Spirits don't die, they **return home**
3. Overall tone: **cute, warm, adorable, hopeful**
4. Platform: **portrait mobile screen**
5. Main identity: **Board + Defense Battle**
6. No PvP in current build
7. 5-element cycle maintained: Water -> Fire -> Wind -> Earth -> Light -> Water
8. Original documents preserved
9. New directions added **additively** only
10. **Consistency and polish** > feature count

---

## 4. Locked System Interpretations

### 4-1. Spatial Interpretation
- **Inside castle**: Board command panel, preparation space, muster area
- **On castle wall**: Actual defense battlefield
- **Outside castle**: Enemy approach lines
- **Internal stairs**: Connection from inside muster area to wall top

### 4-2. Core Battlefield Interpretation
Previous "board center is battlefield" interpretation is **deprecated**.
Current interpretation:
- Board is a **command panel**, NOT a battlefield
- Actual towers and troops are deployed **on the castle wall**
- Board results move **through internal stairs** to wall top
- Enemies approach from **outside the castle** toward the wall

### 4-3. Screen Layout
- Top: Wave HUD / Resources / Life / Key status
- Center: Entire castle
  - Castle interior board
  - Courtyard/muster area
  - Internal stairs
  - Left/Front/Right wall defense zones
  - External enemy approach lines
- Bottom: Dice / Spirit / Card / Key controls

### 4-4. Wall Defense Zones (VS scope)
- Left wall zone x1
- Front wall zone x1 (main battlefield)
- Right wall zone x1
- Front wall is the main battlefield; Left/Right provide expansion and choice

### 4-5. Internal Stairs Rules
- Stairs are NOT castle exit points
- Stairs go: **inside castle -> wall top**
- Stairs are the key connector between board and defense battle
- Stairs MUST be clearly visible

---

## 5. World Setting and Emotion

MongeulBell is a fantasy of **purification and recovery**, not destruction and slaughter.
Player recovers the wounded World Tree, not destroys.

- Spirits are friends/companions, not soldiers
- Enemies are spore-corrupted creatures, not pure evil
- Purification is closer to recovery than punishment
- Corruption expressed as sadness/distortion, not disgust
- Bosses symbolize the world's wounds to be healed

---

## 6. Dice/Card Enhancement Rules

Two-dice system is currently valid:
- Movement die (blue): Board movement spaces
- Card die (purple): Card cost cap for this turn

Turn flow:
1. Roll 2 dice
2. Move by movement die
3. Process arrival tile event
4. Use cards within card die cost
5. Send results to muster area, then stairs to wall
6. Proceed to Spore Gate or defense phase

Minimum board tile types for VS:
Start, Gold, Card, Spirit, Spore Gate, Bonus

---

## 7. Style Direction

**Keywords**: cute, round, cozy, bright, sparkly, toy-like, soft colors, child-friendly emotions
**Visual rules**: Round silhouettes, large readable shapes, pastel/warm colors, non-threatening enemies, portrait-readable UI
**Forbidden**: gore, blood, disgusting organic expressions, horror, overly realistic decay, military cold UI, cruel/cynical dialogue

---

## 8. Graphics Upgrade Timing

### OK to do now
- Castle silhouette cleanup
- Wall defense zone visualization
- Internal stairs emphasis
- Slot rings / pedestals / placement markers
- Enemy approach line display
- Board shrink + command panel feel
- Element color separation (water/fire/spirit/gate)

### NOT yet
- Final card illustrations
- Final spirit animations
- Final character art
- Final VFX
- Full background finish
- Detail props

**Now = 1st graphics upgrade phase. NOT final graphics polish phase.**

---

## 9. Screen Structure Standard

Base resolution: 1080 x 2340, portrait 19.5:9

Recommended split:
- TopZone: ~10%
- GameZone: ~72%
- ControlZone: ~18%

Principles:
- Large info chunks over small text
- Show only current-turn-relevant info
- Bottom: thumb-friendly controls
- Top: easy to read, Bottom: easy to tap

---

## 10. Terminology Unification

- kill -> purification
- death -> purification / liberation
- died -> returned home
- enemy -> spore-infected creature
- battle -> purification battle / defense battle
- victory -> purification success
- defeat -> purification failure

---

## 11. AI Response & Work Rules

Every AI responds in this order:
1. Current situation summary
2. This task's goal
3. Files/systems to touch
4. Smallest safe change proposal
5. Expected result after change
6. Next step suggestion

Additional:
- Primary language: Korean
- Beginner-friendly explanations
- Include click locations and execution order
- Suggest safe next step rather than asking
- Don't state uncertain things as facts
- Prioritize reproducibility and maintainability
- Explain WHY when proposing direction changes

---

## 12. Current Work Priorities

1. Internal stairs interpretation correction completion
2. Wall defense layout completion
3. Slot / enemy approach line / wave testing
4. Card 8 / Enemy 4 / Gate 2 / Tower 2 actual connection
5. Save/reward/retry/tutorial stabilization
6. 1st graphics upgrade
7. Playtest and balance adjustment
8. Vertical Slice lock

---

## 13. Archive Rules

Never delete existing documents. Separate as:
- `00_Active/`: This document + Engineering SOLID document
- `90_Archive/`: Previous v1-v10 process documents, past experiments, old handoffs, reference outputs

Archive docs are reference only. NOT the basis for overturning current work.

---

## 14. Summary for Any AI

Any AI reading this MUST:
- Not change MongeulBell's identity
- Prioritize Vertical Slice as the top goal
- Interpret board as command panel, wall top as actual battlefield
- Interpret internal stairs as inside castle -> wall top
- Prioritize 1st readability improvement for graphics
- Respect Archive, but work by this document + Engineering SOLID rules

End.
