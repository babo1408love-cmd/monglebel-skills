# 01. MongeulBell Engineering SOLID + AI Golden Rules v1

This document is the **Active Reference Document #2** for MongeulBell.
Any AI or developer creating/modifying code MUST follow this document.

Goals:
- Apply SOLID practically for MongeulBell
- Make AI produce maintainable code from the start
- Lock code quality, separation, interface, and testing standards
- Enable beginner owner to supervise AI output quality

---

## 0. Core Principles

Satisfy all four simultaneously:
- Loose coupling
- High cohesion
- Safe extension
- Easy replacement

Prioritize over "clever-looking code":
- Readability
- Responsibility separation
- Small modifications possible
- Inspector-connectable
- Testable

---

## 1. SOLID Applied to MongeulBell

### S - Single Responsibility (SRP)
One class = one role.

MongeulBell examples:
- `CastleBoardController`: board movement/tile interpretation only
- `CastleWallDefenseController`: wall defense flow only
- `BoardToWallTransferService`: board -> wall transfer only
- `HybridSaveService`: save/load only
- `TopWaveHUDController`: top HUD display only

#### File Length Rules
- 300 lines: review for splitting
- 500 lines: **strong warning line**
- 500+ only allowed for: auto-generated code, value table/mapping code
- 500+ MUST document reason in comments

### O - Open/Closed (OCP)
Adding new cards/enemies/gates/towers/slots: NO large modifications to core classes.

MongeulBell examples:
- New enemy type: don't expand SporeWaveManager switch
- New card: don't modify CardHandController, connect via CardSO + Resolver
- New wall slot type: keep common flow, add only Adapter/implementation

### L - Liskov Substitution (LSP)
Composition + interfaces over inheritance.

MongeulBell examples:
- Don't force all slots into `BaseWallSlot` inheritance
- Use role interfaces: `IWallSlotReceiver`, `IPlacableTowerSlot`, `IGarrisonReceiver`
- Don't make tower slots awkwardly accept garrison requests

### I - Interface Segregation (ISP)
Small focused interfaces.

MongeulBell examples:
- `IMovableBoardToken`
- `IWallSlotReceiver`
- `IDamageable`
- `IWaveSource`
- `ISaveSnapshotProvider`
- `IHUDPresenter`

Never create one giant `IGameplayObject` interface.

### D - Dependency Inversion (DIP)
Don't tightly couple concrete classes.

MongeulBell examples:
- `CastleWallDefenseController` doesn't know specific tower prefab classes
- `BoardToWallTransferService` depends on `IWallSlotReceiver` interface
- `RewardResolver` depends on `RewardProfile` data, not UI directly
- `HybridCastleSceneController` orchestrates only, never implements details

---

## 2. Recommended Patterns

### Always Recommend
- **Strategy Pattern**: rotation, targeting, card effects, enemy behavior
- **Adapter Pattern**: legacy + new system connection, slot receiver unification
- **Factory Pattern**: card/enemy/tower/SO creation, test asset creation
- **Registry Pattern**: Content DB, ID -> asset mapping
- **Object Pool Pattern**: enemies, projectiles, effects
- **ScriptableObject Data Pattern**: cards, enemies, towers, waves, gates, balance
- **Event-driven Flow**: UI updates, result panels, tutorial step transitions

### Use Carefully
- **State Pattern**: good for run flow transitions, don't over-apply to small systems
- **Service Locator**: temporary bridge use only, no abuse

### Avoid
- Giant God Manager
- Bloated Singleton abuse
- String-based hardcoded branching
- Finding everything with `FindObjectOfType`
- Excessive responsibility in `Update()`

---

## 3. System Responsibility Separation Standard

### 3-1. Board Layer
- `CastleBoardController`: board movement, current tile, arrival call
- `BoardTileResolver`: tile ID -> actual effect interpretation
- `BoardPathSO`: path data
- `BoardTokenMover`: movement animation/interpolation

### 3-2. Transfer Layer
- `BoardToWallTransferService`: board results -> muster/stairs/wall delivery
- `StairTransferPoint`: internal stairs reference point
- `MusterAreaController`: muster area state display

### 3-3. Wall Defense Layer
- `CastleWallDefenseController`: wall overall flow
- `WallSector`: left/front/right zone unit management
- `WallTowerSlot`: tower slot body
- `GarrisonSlot`: garrison slot body
- `WallTowerSlotAdapter` / `GarrisonSlotAdapter`: external connection adapters
- `WallDefensePrefabResolver`: ID -> prefab lookup

### 3-4. Wave Layer
- `SporeWaveManager`: wave execution
- `WaveDefinitionSO`: wave data
- `EnemySpawnResolver`: enemy ID -> prefab/SO lookup
- `WallLaneAnchor`: enemy approach line reference point

### 3-5. Card Layer
- `HybridCardHandController`: hand and use flow
- `CardResolver`: card ID -> effect logic connection
- `HybridCardSO`: card data
- Individual effects as small strategy/effect classes

### 3-6. UI Layer
- `TopWaveHUDController`: top HUD only
- `BottomControlBarController`: bottom input/buttons only
- `ResultPanelController`: result panel only
- `RewardPanelController`: reward panel only

### 3-7. Save Layer
- `HybridSaveService`: save/load
- `RunSnapshot`: run state snapshot data
- `ISaveSnapshotProvider`: save target interface

### 3-8. Scene Orchestration Layer
- `HybridCastleSceneController`: scene-wide flow coordination ONLY

**Forbidden**: Putting board logic, wave logic, card logic, save logic, UI display logic into `HybridCastleSceneController`

---

## 4. Data-First Principle

MongeulBell is a data-driven project.
Numbers and content go into data assets, not code.

Must externalize:
- Card costs, Tower prices, Enemy HP/speed
- Wave compositions, Spore Gate types
- Balance initial values, FTUE text

Recommended assets:
- `HybridCardSO`, `HybridEnemySO`, `HybridTowerSO`
- `HybridGateWaveSO`, `HybridBoardPathSO`
- `HybridBalanceProfileSO`
- `WallDefensePrefabCatalogSO`, `WallSlotBindingProfileSO`
- `WallDefenseWaveTestProfileSO`

---

## 5. Naming Conventions

### Classes
- Role must be visible in the name
- Minimize `Manager`
- Prefer: Controller, Resolver, Service, Presenter, Adapter, Factory, Registry, Profile, Definition

### Interfaces
- Start with `I`: `IWallSlotReceiver`, `ISaveSnapshotProvider`

### Private Fields
- `m_` prefix: `m_boardController`

### Public Properties/Fields
- PascalCase: `CurrentWave`, `MoveSpeed`

### SerializeField
- Always `private + [SerializeField]`

### String ID Format
- `Card_PurifySpark`, `Enemy_SporeDrop`, `Tower_WaterSeed`, `Gate_Calm`, `BoardPath_WaterBranch_FTUE`

---

## 6. Code Quality Rules

1. No magic numbers
2. Null checks required
3. Clear `Debug.LogError` or warning logs on failure
4. Protect exception paths with comments or validation
5. Comments explain **why**, not what
6. Use `[Header]`, `[Tooltip]` for Inspector dependencies
7. Legacy bridges are temporary, never become permanent core
8. Keep `Update()` lightweight
9. No `FindObjectOfType` / `Resources.Load` / runtime Instantiate abuse
10. Separate testable pure logic where possible

---

## 7. Anti-Pattern Ban List

Refactoring warning if you see:
- One class handles board + UI + save + wave simultaneously
- `if (isPlayer) ... else ...` type branching abuse
- Adding new card requires modifying existing switch statement
- Slot directly references concrete prefab class
- Controller continuously searches Scene objects
- One string typo breaks everything
- Too many Inspector manual connections to reproduce
- Untestable pure static utility abuse
- Legacy bridge added without documentation

---

## 8. Testing Rules

MongeulBell is a beginner-owner project. Prioritize **small high-efficiency tests**.

### Must Test
- Reward calculation
- Save/load
- Wave data parsing/interpretation
- Card cost conditions
- Slot connection verification
- Registry lookup

### Recommended Test Types
- EditMode tests: data/interpretation/calculation
- Simple PlayMode smoke tests: scene loading/core flow verification

### Minimum Test Rule
For every new feature, perform at least 1 of:
- Add test code
- Add Verifier
- Add manual reproduction checklist

---

## 9. AI Output Format Rules

All AI proposing code must include:
1. Brief explanation of why structure matches SRP/OCP/DIP
2. Created/modified file list
3. Whether it's a dangerous change or safe additive change
4. Whether Inspector connections are needed
5. Test method
6. Next small step suggestion

---

## 10. Beginner Owner Supervision Questions

### Structure Check Questions
- Does this class have only one role?
- Must existing code be modified to add this feature?
- Can't we separate with an interface?
- Can this logic be externalized as a data asset?
- Should this be a Resolver/Service, not a Controller?

### Refactoring Request Questions
- This class violates SRP, please split it.
- Check if Strategy Pattern fits better here.
- Judge if an Adapter is needed here.
- Add test code for save/reward calculations.
- Too many Inspector manual connections, reduce with auto-verifier.

---

## 11. MongeulBell-Specific Additional Principles

### 11-1. Scene Controller Must Be Thin
`HybridCastleSceneController` only orchestrates.
Never: direct calculation, targeting, save processing, wave generation.

### 11-2. Slots Separated by Role
Tower slots and garrison slots are NOT mixed.
Use interfaces or Adapters for common processing.

### 11-3. Stairs Are Key System Connectors
Board -> Muster -> Internal Stairs -> Wall transfer is a separate service.
Never hack this flow as temporary code in SceneController.

### 11-4. Separate Graphics from Logic
Visual position/effect display and actual data/judgment logic MUST NOT be mixed.
Example:
- Wall placement logic = Data/Service
- Wall slot highlight = Presentation/UI/VFX

### 11-5. Legacy Bridge Lifecycle Management
Temporary bridges allowed, but must document one of:
- When to remove
- What new structure replaces it
- Why it's needed now

---

## 12. Done Criteria

A new feature is "done" only when:
- Feature actually works
- Responsibility separation is intact
- Inspector connections are reproducible
- Minimum verification method exists
- Adding next feature won't require core class surgery
- Makes current Vertical Slice goal clearer

---

## 13. Summary for Any AI Reading This

Any AI reading this MUST:
- See the big picture, modify small and safe
- Strongly enforce SRP/OCP/DIP
- Prioritize ScriptableObject data-driven design
- Connect new systems via Adapter/Strategy/Pool/Registry
- Separate scene orchestrator from actual processors
- Create structures easy for beginner owner to supervise
- Make maintainable code over pretty-looking code

End.
