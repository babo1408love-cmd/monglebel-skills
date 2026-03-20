---
name: monglebel-gamedev
description: "MongeulBell Unity game development rules. Loads project identity, locked interpretations, SOLID engineering rules, and Vertical Slice scope. Use PROACTIVELY when working on any MongeulBell game code, assets, or design."
argument-hint: "[topic or question]"
metadata:
  author: MongeulBell
  version: 1.0.0
  tags: [unity, gamedev, mobile, tower-defense, board-game, korean, monglebel]
  model: opus
---

# MongeulBell Game Development Rules

This skill loads the complete MongeulBell project rules. Every AI working on this project MUST follow these rules.

## Reference Files

For full detailed rules, see companion files in this directory:
- `project-rules.md` -- Project golden rules (identity, locked interpretations, VS scope, style, priorities)
- `engineering-rules.md` -- Engineering SOLID golden rules (patterns, naming, anti-patterns, testing)

---

## 1. Project Identity

MongeulBell is a **portrait mobile 2D hybrid strategy game**.
Main identity (never change):
- Board movement (command board, NOT battlefield)
- Tower defense-style defense battles
- Strategic choices via cards and dice

Current stage: **Vertical Slice** (small but high-quality playable demo).
Prioritize **consistency and polish** over feature count.

---

## 2. Locked Spatial Interpretation

These interpretations are LOCKED. Do not change without explicit approval:
- **Inside the castle** = Board command panel, preparation space, muster area
- **On the castle wall** = Actual defense battlefield
- **Outside the castle** = Enemy approach lines
- **Internal stairs** = Connection from inside castle (muster area) to wall top
- The board is a **command panel**, NOT a battlefield
- Actual towers and troops are deployed **on the castle wall**
- Board results move **through internal stairs** to the wall
- Enemies approach the wall **from outside the castle**

---

## 3. Absolute Rules (Never Change)

1. Enemies are **purified**, not killed
2. Spirits **return home**, not die
3. Tone: **cute, warm, cozy, hopeful**
4. Platform: **portrait mobile screen** (1080x2340, 19.5:9)
5. Main identity: **Board + Tower Defense**
6. No PvP in current build
7. 5-element cycle: Water -> Fire -> Wind -> Earth -> Light -> Water
8. Existing documents: preserve originals, add new directions **additively**
9. Feature count < **Consistency and polish**

---

## 4. Vertical Slice Scope

Included:
- 1 region, board movement, wall defense, 8 cards, 2 tower types
- 2 spirits, 3 enemy types + 1 mini-boss
- Basic save/load, basic home/growth/retry flow
- Simple FTUE tutorial

Excluded: PvP, guilds, season pass, live events, forced ads, paid gacha, multiplayer, large server architecture

---

## 5. SOLID Engineering Rules

### SRP (Single Responsibility)
- One class = one role
- 300 lines soft limit, 500 lines hard warning
- SceneController orchestrates only, never implements logic directly

### OCP (Open/Closed)
- New cards/enemies/towers: add SO + Resolver, don't modify core switch statements
- Use Strategy, Adapter, Factory patterns

### LSP (Liskov Substitution)
- Prefer interfaces + composition over deep inheritance
- Role interfaces: `IWallSlotReceiver`, `IPlacableTowerSlot`, `IGarrisonReceiver`

### ISP (Interface Segregation)
- Small focused interfaces: `IMovableBoardToken`, `IDamageable`, `IWaveSource`, `ISaveSnapshotProvider`

### DIP (Dependency Inversion)
- Depend on interfaces/profiles/SO, not concrete classes
- `BoardToWallTransferService` depends on `IWallSlotReceiver`, not specific slot types

---

## 6. Naming Conventions

- **Classes**: Use role names -- Controller, Service, Resolver, Adapter, Factory, Registry, Profile, Definition
- **Interfaces**: `I` prefix -- `IWallSlotReceiver`
- **Private fields**: `m_` prefix -- `m_boardController`
- **Public properties**: PascalCase -- `CurrentWave`, `MoveSpeed`
- **SerializeField**: Always `private + [SerializeField]`
- **String IDs**: `Card_PurifySpark`, `Enemy_SporeDrop`, `Tower_WaterSeed`

---

## 7. Recommended Patterns

**Always use**: Strategy, Adapter, Factory, Registry, Object Pool, ScriptableObject Data, Event-driven Flow
**Use carefully**: State Pattern, Service Locator (bridge only)
**Avoid**: God Manager, Singleton abuse, string hardcoding, FindObjectOfType everywhere, heavy Update()

---

## 8. System Responsibility Separation

| Layer | Key Classes |
|-------|------------|
| Board | CastleBoardController, BoardTileResolver, BoardPathSO |
| Transfer | BoardToWallTransferService, StairTransferPoint, MusterAreaController |
| Wall Defense | WallSector, WallTowerSlot, GarrisonSlot, WallDefensePrefabResolver |
| Wave | SporeWaveManager, WaveDefinitionSO, EnemySpawnResolver, WallLaneAnchor |
| Card | HybridCardHandController, CardResolver, HybridCardSO |
| UI | TopWaveHUDController, BottomControlBarController, ResultPanelController |
| Save | HybridSaveService, RunSnapshot, ISaveSnapshotProvider |
| Orchestration | HybridCastleSceneController (thin orchestrator ONLY) |

---

## 9. Terminology

| English | MongeulBell |
|---------|-------------|
| kill | purify (purification) |
| death | purify / liberation |
| died | returned home |
| enemy | spore-infected creature |
| battle | purification battle / defense battle |
| victory | purification success |
| defeat | purification failure |

---

## 10. Style Direction

**Keywords**: cute, round, cozy, sparkly, toy-like, pastel, friendly, readable
**Forbidden**: gore, blood, horror, dark/grim, military-style UI, overly realistic decay, aggressive silhouettes

---

## 11. AI Response Format

Every response should follow:
1. Current situation summary
2. This task's goal
3. Files/systems to touch
4. Smallest safe change proposal
5. Expected result after change
6. Next step suggestion

Additional rules:
- Korean as primary language
- Explain for beginners
- Include click locations and execution order when possible
- Suggest safe next step rather than asking questions
- Never speak uncertain things as confirmed facts
