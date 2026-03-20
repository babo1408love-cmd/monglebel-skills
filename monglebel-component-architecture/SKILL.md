---
name: monglebel-component-architecture
description: "MongeulBell reusable component architecture. Composition over inheritance -- build entities by assembling Common components. Use PROACTIVELY when creating new characters, enemies, towers, spirits, or any gameplay entity."
argument-hint: "[entity type or component question]"
metadata:
  author: MongeulBell
  version: 1.0.0
  tags: [unity, component, composition, architecture, reusable, monglebel]
  model: opus
---

# MongeulBell Component Architecture -- Reusable Building Blocks

## Core Rule

**"Common에 한 번만 만들고, 모두 가져다 조합한다."**

새 캐릭터/적/타워/정령을 만들 때:
1. Common/Components에 이미 있는 부품을 **먼저 확인**
2. 있으면 **재사용** (절대 다시 만들지 않음)
3. 없으면 Common에 **새 공통 부품으로 추가**
4. 그 엔티티만의 고유 기능만 **개별 폴더에 추가**

---

## 1. Common Interfaces (공통 계약)

모든 인터페이스는 `Common/Interfaces/`에 위치한다.
인터페이스는 작게 쪼갠다 (ISP 원칙).

```csharp
// 체력이 있으면 이걸 구현
public interface IDamageable
{
    float CurrentHealth { get; }
    float MaxHealth { get; }
    bool IsAlive { get; }
    void TakeDamage(float amount, DamageSource source);
    void OnDefeated();  // Die() 금지 -- 몽글벨은 "정화" 또는 "귀환"
}

// 이동하면 이걸 구현
public interface IMovable
{
    float MoveSpeed { get; }
    void Move(Vector3 direction);
    void Stop();
}

// 슬롯에 배치 가능하면 이걸 구현
public interface ISlotPlaceable
{
    string OccupantId { get; }
    bool CanPlaceAt(WallSlotType slotType);
}

// 공격할 수 있으면 이걸 구현
public interface IAttacker
{
    float AttackDamage { get; }
    float AttackRange { get; }
    float AttackCooldown { get; }
    void Attack(IDamageable target);
}

// 버프/디버프를 받을 수 있으면 이걸 구현
public interface IBuffable
{
    void ApplyBuff(BuffData buff);
    void RemoveBuff(string buffId);
}

// 풀링 가능하면 이걸 구현
public interface IPoolable
{
    void OnSpawnFromPool();
    void OnReturnToPool();
}
```

---

## 2. Common Components (공통 부품)

모든 공통 컴포넌트는 `Common/Components/`에 위치한다.
한 번만 만들고, 어디서든 AddComponent로 붙인다.

### HealthComponent
```csharp
// 체력 시스템 -- 적, 정령, 타워, 보스 모두 사용
public class HealthComponent : MonoBehaviour, IDamageable
{
    [SerializeField] private float m_maxHealth = 100f;
    [SerializeField] private bool m_verboseLogging = false;

    public float CurrentHealth { get; private set; }
    public float MaxHealth => m_maxHealth;
    public bool IsAlive => CurrentHealth > 0f;

    // 이벤트 -- UI나 VFX가 이걸 구독
    public event System.Action<float, float> OnHealthChanged;   // (current, max)
    public event System.Action OnDefeatedEvent;                  // 정화/귀환 시

    private void Awake() => CurrentHealth = m_maxHealth;

    public void TakeDamage(float amount, DamageSource source)
    {
        if (!IsAlive) return;
        CurrentHealth = Mathf.Max(0f, CurrentHealth - amount);
        OnHealthChanged?.Invoke(CurrentHealth, m_maxHealth);

        if (!IsAlive) OnDefeated();
    }

    public void OnDefeated()
    {
        OnDefeatedEvent?.Invoke();
        // 실제 처리는 이벤트 구독자가 함 (적=정화, 정령=귀환)
    }

    public void Heal(float amount)
    {
        CurrentHealth = Mathf.Min(m_maxHealth, CurrentHealth + amount);
        OnHealthChanged?.Invoke(CurrentHealth, m_maxHealth);
    }
}
```

### MovementComponent
```csharp
// 이동 시스템 -- NavMesh 또는 직접 이동
public class MovementComponent : MonoBehaviour, IMovable
{
    [SerializeField] private float m_moveSpeed = 3f;
    [SerializeField] private bool m_useNavMesh = false;

    public float MoveSpeed => m_moveSpeed;

    public void Move(Vector3 direction) { /* 이동 처리 */ }
    public void Stop() { /* 정지 처리 */ }
    public void SetSpeed(float newSpeed) => m_moveSpeed = newSpeed;
}
```

### AttackComponent
```csharp
// 공격 시스템 -- 타워, 정령, 적 모두 사용
public class AttackComponent : MonoBehaviour, IAttacker
{
    [SerializeField] private float m_attackDamage = 10f;
    [SerializeField] private float m_attackRange = 5f;
    [SerializeField] private float m_attackCooldown = 1f;

    public float AttackDamage => m_attackDamage;
    public float AttackRange => m_attackRange;
    public float AttackCooldown => m_attackCooldown;

    public void Attack(IDamageable target) { /* 공격 처리 */ }
}
```

---

## 3. Entity Composition (엔티티 조합)

### Spirit (정령) = 이동 + 체력 + 공격 + 귀환
```
Spirit_WaterSeed
├── HealthComponent       ← Common (공통)
├── MovementComponent     ← Common (공통)
├── AttackComponent       ← Common (공통)
├── SpiritIdentity        ← Spirit 전용 (속성, 레벨, SO 연결)
└── SpiritReturnHandler   ← Spirit 전용 (귀환 연출)
```

### Enemy (포자 감염 생물) = 이동 + 체력 + AI + 정화
```
Enemy_SporeDrop
├── HealthComponent       ← Common (동일!)
├── MovementComponent     ← Common (동일!)
├── EnemyAI               ← Enemy 전용 (경로 따라 이동)
├── EnemyIdentity         ← Enemy 전용 (SO 연결, 속성)
└── PurifyHandler         ← Enemy 전용 (정화 연출)
```

### Boss (미니보스) = 이동 + 체력 + AI + 정화 + 특수기술
```
Boss_CorruptedGuardian
├── HealthComponent       ← Common (동일!)
├── MovementComponent     ← Common (동일!)
├── EnemyAI               ← Enemy (동일!)
├── EnemyIdentity         ← Enemy (동일!)
├── PurifyHandler         ← Enemy (동일!)
└── BossSpecialAttack     ← Boss 전용 (보스만의 패턴)
```

### Tower (타워) = 체력 + 공격 + 슬롯배치
```
Tower_WaterSeed
├── HealthComponent       ← Common (동일!)
├── AttackComponent       ← Common (동일!)
├── TowerIdentity         ← Tower 전용 (SO, 레벨, 업그레이드)
├── TowerTargetting       ← Tower 전용 (가장 가까운 적 탐색)
└── WallTowerSlotAdapter  ← Tower 전용 (슬롯 연결)
```

### Projectile (투사체) = 이동 + 풀링
```
Projectile_WaterOrb
├── MovementComponent     ← Common (동일!)
├── PoolableComponent     ← Common (동일!)
└── ProjectileHit         ← Projectile 전용 (충돌 시 데미지)
```

---

## 4. Folder Structure (폴더 구조)

```
Assets/_MongleBell/Scripts/
│
├── Common/                    ← 공통 (모두 사용)
│   ├── Interfaces/
│   │   ├── IDamageable.cs
│   │   ├── IMovable.cs
│   │   ├── IAttacker.cs
│   │   ├── ISlotPlaceable.cs
│   │   ├── IBuffable.cs
│   │   └── IPoolable.cs
│   ├── Components/
│   │   ├── HealthComponent.cs
│   │   ├── MovementComponent.cs
│   │   ├── AttackComponent.cs
│   │   ├── PoolableComponent.cs
│   │   └── BuffReceiver.cs
│   └── Data/
│       ├── DamageSource.cs
│       └── BuffData.cs
│
├── Spirit/                    ← 정령 전용
│   ├── SpiritIdentity.cs
│   ├── SpiritReturnHandler.cs
│   └── SpiritSkill.cs
│
├── Enemy/                     ← 적 전용
│   ├── EnemyAI.cs
│   ├── EnemyIdentity.cs
│   ├── PurifyHandler.cs
│   └── BossSpecialAttack.cs
│
├── Tower/                     ← 타워 전용
│   ├── TowerIdentity.cs
│   ├── TowerTargetting.cs
│   └── TowerUpgradeHandler.cs
│
├── Projectile/                ← 투사체 전용
│   └── ProjectileHit.cs
│
└── Board/                     ← 보드 전용
    ├── BoardToken.cs
    └── BoardTokenMover.cs
```

---

## 5. Decision Flowchart (새 기능 추가 시)

```
새 기능이 필요하다
    │
    ▼
Common/Components에 이미 있나?
    │
   YES ──→ AddComponent로 붙인다. 끝.
    │
    NO
    ▼
이 기능을 2개 이상 엔티티가 쓸 수 있나?
    │
   YES ──→ Common/Components에 새 공통 부품 생성
    │        → 인터페이스도 Common/Interfaces에 추가
    │        → 끝.
    │
    NO
    ▼
해당 엔티티 폴더에만 전용 스크립트 생성
    → 끝.
```

---

## 6. Naming Rules (네이밍)

| 종류 | 규칙 | 예시 |
|------|------|------|
| 인터페이스 | `I` + 역할 | `IDamageable`, `IMovable` |
| 공통 컴포넌트 | 역할 + `Component` | `HealthComponent`, `AttackComponent` |
| 전용 스크립트 | 엔티티 + 역할 | `SpiritReturnHandler`, `EnemyAI` |
| 이벤트 | `On` + 과거분사 | `OnDefeatedEvent`, `OnHealthChanged` |
| 데이터 | 역할 + `Data` / `SO` | `BuffData`, `HybridEnemySO` |

---

## 7. MongeulBell Terminology in Code

몽글벨 규칙에 맞는 코드 용어:

| 금지 | 사용 | 이유 |
|------|------|------|
| `Die()` | `OnDefeated()` | 적=정화, 정령=귀환. "죽음" 없음 |
| `Kill()` | `Purify()` | 적은 정화된다 |
| `Death` | `Defeated` / `Purified` | 죽음 표현 금지 |
| `isDead` | `isDefeated` / `!IsAlive` | 동일 |
| `Destroy()` | `ReturnToPool()` | 오브젝트 풀링 사용 |
| `Spawn()` | `Emerge()` / `SpawnFromPool()` | 풀에서 꺼냄 |
| `HP` | `Health` / `CurrentHealth` | 풀네임 사용 |
| `enemy.Kill()` | `purifyHandler.Purify()` | 정화 전용 핸들러 |

---

## 8. Anti-Patterns (하면 안 되는 것)

```
WRONG: 매번 새로 만들기
──────────────────────
Player.cs     → 체력 코드 작성
Enemy.cs      → 체력 코드 복사-붙여넣기
Boss.cs       → 체력 코드 또 복사-붙여넣기
Tower.cs      → 체력 코드 또또 복사-붙여넣기
→ 체력 계산 버그 → 4곳 다 수정해야 함

RIGHT: 공통 부품 재사용
──────────────────────
HealthComponent.cs → 한 번만 작성
Player  → AddComponent<HealthComponent>
Enemy   → AddComponent<HealthComponent>
Boss    → AddComponent<HealthComponent>
Tower   → AddComponent<HealthComponent>
→ 체력 계산 버그 → 1곳만 수정하면 전부 해결
```

```
WRONG: 거대 상속 트리
──────────────────────
BaseEntity
  └── LivingEntity
        ├── PlayerEntity
        ├── EnemyEntity
        │     └── BossEntity
        └── SpiritEntity
→ 새 타입 추가할 때마다 상속 구조 고민

RIGHT: 조합
──────────────────────
아무 빈 GameObject에 필요한 Component를 붙인다.
→ 새 타입 = 기존 부품 조합 + 전용 스크립트 1개
```

---

## 9. Claude Code Usage (AI 활용법)

새 엔티티를 요청할 때 이렇게 말하면 됩니다:

```
"새로운 Fire 정령을 만들어줘.
Common/Components에 있는 HealthComponent, MovementComponent,
AttackComponent를 재사용하고,
Fire 정령만의 고유 스킬(FirePurifySkill)만 새로 만들어줘."
```

AI는 반드시:
1. Common/ 폴더를 먼저 확인
2. 이미 있는 컴포넌트는 재사용
3. 없는 공통 기능이면 Common에 새로 추가
4. 고유 기능만 해당 엔티티 폴더에 생성

---

## 10. Checklist (새 엔티티 만들 때)

- [ ] Common/Components 확인했나? (이미 있는 부품 재사용)
- [ ] 인터페이스로 계약 정의했나? (IDamageable 등)
- [ ] `Die()`, `Kill()` 대신 몽글벨 용어 사용했나?
- [ ] 오브젝트 풀링 적용했나? (Destroy 금지)
- [ ] 한 컴포넌트가 한 역할만 하나? (SRP)
- [ ] SO 데이터로 수치를 뺐나? (매직넘버 금지)
- [ ] 전용 스크립트는 해당 폴더에만 있나?

End.
