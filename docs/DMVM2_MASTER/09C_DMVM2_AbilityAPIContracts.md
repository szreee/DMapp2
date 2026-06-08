# 09C_DMVM2_AbilityAPIContracts.md

# DMVM2 Ability API Contracts Specification v1.0

## 目的

本仕様書はDMVM2の能力システムにおける公開API契約を固定する。

対象:

* AbilityDefinition
* AbilityInstance
* ConditionEvaluator
* TargetResolver
* EffectExecutor

本仕様書完成後、上記クラスのpublic API変更は禁止とする。

---

# 1. AbilityDefinition

## 役割

能力の静的定義。

ゲーム中に変更されない。

CardDatabaseおよびAbilityDatabaseから取得される。

---

## Ownership

```text
AbilityDatabase
  └ owns AbilityDefinition
```

読み取り専用。

---

## Lifetime

```text
Game Boot

↓

Load

↓

Game Shutdown
```

---

## Public API

```cpp
class AbilityDefinition
{
public:

    AbilityID GetID() const;

    AbilityType GetType() const;

    TriggerDefinition GetTrigger() const;

    ConditionDefinition GetCondition() const;

    TargetDefinition GetTarget() const;

    EffectDefinition GetEffect() const;

    CostDefinition GetCost() const;

    ZoneCondition GetZoneCondition() const;

    bool IsReplacement() const;

    bool IsContinuous() const;
};
```

---

## Private Members

```cpp
AbilityID m_ID;

AbilityType m_Type;

TriggerDefinition m_Trigger;

ConditionDefinition m_Condition;

TargetDefinition m_Target;

EffectDefinition m_Effect;

CostDefinition m_Cost;

ZoneCondition m_ZoneCondition;
```

---

## Dependency

```text
TriggerDefinition
ConditionDefinition
TargetDefinition
EffectDefinition
CostDefinition
```

---

# 2. AbilityInstance

## 役割

ゲーム中の能力実体。

AbilityDefinition参照を保持する。

---

## Ownership

```text
CardInstance
 └ owns AbilityInstance
```

---

## Lifetime

```text
Card Create

↓

Ability Create

↓

Card Destroy
```

---

## Public API

```cpp
class AbilityInstance
{
public:

    AbilityID GetAbilityID() const;

    const AbilityDefinition&
        GetDefinition() const;

    ObjectID GetSource() const;

    PlayerID GetController() const;

    bool IsEnabled() const;

    void SetEnabled(
        bool Enabled);

    uint64_t GetTimestamp() const;
};
```

---

## Private Members

```cpp
AbilityID m_AbilityID;

const AbilityDefinition*
    m_Definition;

ObjectID m_Source;

PlayerID m_Controller;

bool m_Enabled;

uint64_t m_Timestamp;
```

---

## Dependency

```text
AbilityDefinition
CardInstance
GameState
```

---

# 3. ConditionEvaluator

## 役割

Condition DSL評価。

AbilityResolverからのみ呼ばれる。

---

## Ownership

```text
AbilityManager owns
ConditionEvaluator
```

---

## Lifetime

```text
Engine Start

↓

Engine Shutdown
```

---

## Public API

```cpp
class ConditionEvaluator
{
public:

    bool Evaluate(
        const ConditionDefinition& Condition,
        const GameState& State,
        const GameEvent& Event,
        const AbilityInstance& Ability) const;

    bool EvaluateNode(
        const ConditionNode& Node,
        const GameState& State,
        const GameEvent& Event,
        const AbilityInstance& Ability) const;
};
```

---

## Private Members

```cpp
ConditionRegistry* m_Registry;
```

---

## Dependency

```text
GameState
GameEvent
AbilityInstance
ConditionRegistry
```

---

## Thread Safety

```text
Read Only

Thread Safe
```

---

# 4. TargetResolver

## 役割

対象選択解決。

DSLを実際のObjectID群へ変換する。

---

## Ownership

```text
AbilityManager owns
TargetResolver
```

---

## Lifetime

```text
Engine Start

↓

Engine Shutdown
```

---

## Public API

```cpp
class TargetResolver
{
public:

    TargetList Resolve(
        const TargetDefinition& Target,
        const GameState& State,
        const AbilityInstance& Ability,
        const GameEvent& Event) const;

    bool IsValidTarget(
        ObjectID Object,
        const TargetDefinition& Target,
        const GameState& State) const;
};
```

---

## Private Members

```cpp
ObjectRegistry* m_Registry;
```

---

## Dependency

```text
GameState
ObjectRegistry
AbilityInstance
GameEvent
```

---

## Output

```cpp
using TargetList =
    std::vector<ObjectID>;
```

---

# 5. EffectExecutor

## 役割

Effect DSLをActionへ変換する。

RuleEngineへActionを投入する唯一の能力実行層。

---

## Ownership

```text
AbilityManager owns
EffectExecutor
```

---

## Lifetime

```text
Engine Start

↓

Engine Shutdown
```

---

## Public API

```cpp
class EffectExecutor
{
public:

    void Execute(
        const EffectDefinition& Effect,
        const AbilityContext& Context,
        RuleEngine& Engine);

    void ExecuteNode(
        const EffectNode& Node,
        const AbilityContext& Context,
        RuleEngine& Engine);
};
```

---

## Private Members

```cpp
ActionFactory* m_ActionFactory;
```

---

## Dependency

```text
RuleEngine
AbilityContext
ActionFactory
EffectDefinition
```

---

# 6. AbilityContext

## 役割

能力解決時の共通実行コンテキスト。

---

## Public API

```cpp
struct AbilityContext
{
    GameState* State;

    AbilityInstance* Ability;

    GameEvent* Event;

    TargetList Targets;

    ObjectID Source;

    PlayerID Controller;
};
```

---

## Ownership

```text
AbilityResolver owns
AbilityContext
```

スタック上に生成。

---

# 7. Resolver Interaction

能力解決順序固定。

```text
AbilityInstance

↓

AbilityDefinition

↓

ConditionEvaluator

↓

TargetResolver

↓

AbilityContext

↓

EffectExecutor

↓

ActionQueue
```

---

# 8. Forbidden Dependencies

以下は禁止。

```text
AbilityDefinition
 ↓
RuleEngine
```

```text
AbilityDefinition
 ↓
GameState
```

```text
ConditionEvaluator
 ↓
EffectExecutor
```

```text
TargetResolver
 ↓
RuleEngine
```

---

# 9. Mutability Rules

## AbilityDefinition

```text
Immutable
```

---

## AbilityInstance

変更可能:

```text
Enabled
Timestamp
Controller
```

---

## ConditionEvaluator

```text
Stateless
```

---

## TargetResolver

```text
Stateless
```

---

## EffectExecutor

```text
Stateless
```

---

# 10. Final Dependency Graph

```text
AbilityDefinition
        ↓
AbilityInstance
        ↓
AbilityResolver
    ├── ConditionEvaluator
    ├── TargetResolver
    └── EffectExecutor
                ↓
           RuleEngine
                ↓
           ActionQueue
```

---

# 完全再現原則

```text
CoreRules
    ↓
AbilitySystem
    ↓
Ability API Contracts
    ↓
Implementation
```

実装は本契約に従う。

本契約の変更はDMVM2仕様変更として扱う。
