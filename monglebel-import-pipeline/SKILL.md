---
name: monglebel-import-pipeline
description: "MongeulBell external file import pipeline. Automatically applies namespace wrapping, folder routing, terminology fixes, and code conventions when importing .cs files from external sources (ZIP bundles, Downloads, other AIs). Use PROACTIVELY whenever applying external code files to the MongeulBell project."
argument-hint: "[file path or zip path]"
metadata:
  author: MongeulBell
  version: 1.0.0
  tags: [unity, import, pipeline, namespace, conversion, monglebel]
  model: opus
---

# MongeulBell Import Pipeline

When importing ANY external .cs file into the MongeulBell project, apply ALL of the following transformations automatically. Never skip steps.

---

## 1. Namespace Wrapping Rule

### Runtime files (NOT in Editor folder)

**Target namespace**: `MongeulBell.HybridCastle`

Steps:
1. Check if the file already has `namespace MongeulBell.HybridCastle`
2. If YES → write as-is (no changes needed)
3. If NO → wrap the entire code body inside `namespace MongeulBell.HybridCastle { }`

Wrapping format:
```csharp
using UnityEngine;
using System.Collections.Generic;

namespace MongeulBell.HybridCastle
{
    // original class/struct/enum/interface content here
    // indented one level
}
```

### Self-referencing using removal
If a file has `using MongeulBell.HybridCastle;` AND it is now INSIDE that namespace, **remove that using line** (it's redundant and causes warnings).

### Fully-qualified references inside namespace
If a file uses `MongeulBell.HybridCastle.SomeType` and is now inside the namespace, **simplify to just `SomeType`**.

### Editor files

Editor files (.cs files that go in the `Editor/` folder) follow different rules:
1. **Do NOT wrap in namespace** — keep in global scope
2. **Add `using MongeulBell.HybridCastle;`** at the top if the file references any HybridCastle types
3. Keep any `#if UNITY_EDITOR` guards as-is

---

## 2. Folder Routing Rule

| File Type | Destination Folder |
|-----------|-------------------|
| Runtime MonoBehaviour | `Assets/_MongleBell/Scripts/HybridCastle/` |
| Runtime ScriptableObject | `Assets/_MongleBell/Scripts/HybridCastle/` |
| Runtime interface | `Assets/_MongleBell/Scripts/HybridCastle/` |
| Runtime enum | `Assets/_MongleBell/Scripts/HybridCastle/` |
| Runtime data class | `Assets/_MongleBell/Scripts/HybridCastle/` |
| Editor script | `Assets/_MongleBell/Scripts/Editor/` |
| Editor with `[MenuItem]` | `Assets/_MongleBell/Scripts/Editor/` |
| Editor with `[CustomEditor]` | `Assets/_MongleBell/Scripts/Editor/` |

### How to detect Editor files
A file is an Editor file if ANY of these are true:
- Filename contains "Editor" or "AssetGenerator" or "Wizard"
- File has `using UnityEditor;` (primary indicator)
- File has `[MenuItem(...)]` attribute
- File has `[CustomEditor(...)]` attribute
- File has `#if UNITY_EDITOR` wrapping the entire content
- Source ZIP had it in an `Editor/` subfolder

---

## 3. Terminology Check

After wrapping, scan the code for forbidden terminology and fix:

| Found | Replace With | Context |
|-------|-------------|---------|
| `Die()` | `OnDefeated()` | Method name for enemy/spirit defeat |
| `Kill()` | `Purify()` | Method name for enemy elimination |
| `isDead` | `isDefeated` or `!IsAlive` | Boolean check |
| `OnDeath` | `OnDefeated` | Event name |
| `DeathEvent` | `DefeatedEvent` | Event name |
| `health <= 0` | Keep as-is | Logic is fine, just rename associated methods |
| `Destroy(gameObject)` | `ReturnToPool()` if pooled | Object lifecycle |

**Exception**: Do NOT rename Unity built-in methods like `OnDestroy()`, `Destroy()` for cleanup, etc.
**Exception**: Do NOT rename if the term is in a comment explaining the concept.

---

## 4. Code Convention Check

Apply MongeulBell coding conventions:

### Field naming
- Private fields: must have `m_` prefix → `m_moveSpeed`, `m_health`
- If found `private float speed;` → rename to `private float m_speed;`
- SerializeField: `[SerializeField] private float m_moveSpeed;`

### Property naming
- Public properties: PascalCase → `MoveSpeed`, `CurrentHealth`

### Header attributes
- Group related fields with `[Header("Section Name")]`

### Null checks
- All public methods that use serialized references should null-check them
- Log with `Debug.LogError($"[{nameof(ClassName)}] Reference is missing.", this);`

---

## 5. Import Process Checklist

When importing external files, follow this exact order:

```
Step 1: Identify files
  └── List all .cs files from source (ZIP, folder, Downloads)
  └── Separate into Runtime vs Editor

Step 2: Read each file
  └── Check existing namespace
  └── Check if it's an Editor file

Step 3: Transform
  └── Apply namespace wrapping (Runtime) or using addition (Editor)
  └── Remove self-referencing usings
  └── Simplify fully-qualified references
  └── Check terminology
  └── Check field naming conventions

Step 4: Write to project
  └── Runtime → Assets/_MongleBell/Scripts/HybridCastle/
  └── Editor → Assets/_MongleBell/Scripts/Editor/

Step 5: Verify
  └── Count files written matches files read
  └── No duplicate filenames with existing files
  └── Report summary table
```

---

## 6. SceneController Connection Check

After importing new files, check if `HybridCastleSceneController.cs` needs updates:

- New Service/Manager? → May need a `[SerializeField]` field + connection method
- New SO data? → May need Inspector reference
- New debug tester? → Usually standalone, no SceneController change needed
- New verifier? → Usually standalone

If SceneController update is needed, add:
1. `[Header("V## Section Name")]` with version number
2. `[SerializeField] private TypeName m_fieldName;`
3. Public method for external access if needed
4. Keep SceneController thin — orchestration only

---

## 7. WallSystemSceneSetup.cs Update Check

After importing, check if `WallSystemSceneSetup.cs` (the editor auto-setup script) needs to create new objects:

- New Service → Add GameObject creation + component
- New Verifier → Add to debug tester object
- New Debug Tester → Add to debug tester object
- Update menu text version range (e.g., "V11~V14" → "V11~V16")

---

## 8. MEMORY.md Update

After successful import, update `MEMORY.md`:
- Add new version section with file count
- List key classes and their roles
- Update HybridCastle total file count
- Note what connects to what
- Add "Next" pointer for the following version

---

## 9. Example: Importing a V15 Bundle

```
Input: MongeulBell_v15_EnemyRoster_Bundle.zip

1. Unzip → 9 .cs files found (8 runtime + 1 editor)

2. Runtime files (8):
   - HybridEnemyArchetype.cs → wrap in namespace
   - IPurifiableEnemy.cs → wrap in namespace
   - IWallPressureReceiver.cs → wrap in namespace
   - HybridEnemyDefinitionSO.cs → wrap in namespace
   - HybridEnemyActor.cs → wrap + remove self-ref using
   - WallPressureDebugReceiver.cs → wrap in namespace
   - V15EnemyRosterSetupVerifier.cs → wrap + remove self-ref using
   - V15EnemyRosterDebugTester.cs → wrap + remove self-ref using
   All → write to HybridCastle/

3. Editor file (1):
   - V15EnemyDefinitionAssetGenerator.cs → keep global, add using
   → write to Editor/

4. Verify: 9 files written ✓
5. Update SceneController if needed
6. Update WallSystemSceneSetup if needed
7. Update MEMORY.md
```

---

## 10. Common Mistakes to Avoid

| Mistake | Why Bad | Correct |
|---------|---------|---------|
| Forgetting namespace wrap | Types won't resolve | Always wrap runtime files |
| Wrapping editor files in namespace | MenuItem may not work | Keep editor files global |
| Leaving self-ref using | Compiler warning | Remove redundant using |
| Writing to wrong folder | Unity won't find Editor scripts | Check file type carefully |
| Skipping terminology check | Breaks MongeulBell tone | Always scan for Die/Kill |
| Not updating MEMORY.md | Next session loses context | Always update after import |

End.
