# DMVM2 Ability System Specification v1.0

## 目的

本仕様書はDMVM2における能力システムを定義する。

対象

* Triggered Ability
* Activated Ability
* Static Ability
* Continuous Effect
* Replacement Effect

全てのカード能力は本仕様書に従う。

---

# 1. 基本原則

## 1.1 能力はデータである

禁止

```cpp
class BolshackDragon
{
    void OnAttack();
};
```

許可

```json
{
  "AbilityID":"Bolshack_Attack"
}
```

カード固有コードは禁止。

---

## 1.2 Ability Definition

能力はAbilityDefinitionとして管理する。

```cpp
struct AbilityDefinition
{
    TriggerType Trigger;
    ConditionType Condition;
    TargetSelector Target;
    EffectType Effect;
    CostDefinition Cost;
};
```

---

# 2. Ability Categories

能力は5種類に分類される。

## Triggered Ability

イベント後に誘発。

例

```text
攻撃した時
```

```text
破壊された時
```

---

## Activated Ability

プレイヤーが起動する。

例

```text
タップ:
カードを1枚引く
```

---

## Static Ability

常時適用。

例

```text
スピードアタッカー
```

```text
ブロッカー
```

---

## Continuous Effect

継続的に状態を書き換える。

例

```text
自分のクリーチャーは+2000
```

---

## Replacement Effect

イベントを書き換える。

例

```text
破壊されるかわりに手札へ戻す
```

---

# 3. Ability Definition

```cpp
struct AbilityDefinition
{
    AbilityType Type;

    TriggerType Trigger;

    ConditionType Condition;

    TargetSelectorType TargetSelector;

    EffectType Effect;

    CostDefinition Cost;

    ZoneCondition Zone;
};
```

---

# 4. Trigger System

## Trigger Registration

イベント発生後に評価する。

```text
Event Resolve

↓

Trigger Check

↓

Queue Register
```

---

## Trigger Matching

```cpp
bool Matches(
    TriggerType Trigger,
    GameEvent Event);
```

例

```text
CardDrawn
```

↓

```text
OnDraw
```

---

# 5. Condition System

追加条件判定。

```cpp
bool Evaluate(
    ConditionType Condition,
    GameState State,
    GameEvent Event);
```

---

## 基本条件

### Always

常に真

---

### Self

自分が起こした

---

### Opponent

相手が起こした

---

### Attacking

攻撃中

---

### Blocked

ブロックされた

---

### PowerGreater

指定値以上

---

# 6. Zone Condition

能力が有効なゾーン。

```cpp
enum class ZoneCondition
{
    Any,

    BattleZone,

    Hand,

    Graveyard,

    ManaZone,

    ShieldZone,

    Deck
};
```

---

## 判定

```cpp
bool CheckZone(
    ObjectID Card,
    ZoneCondition Zone);
```

---

# 7. Cost System

能力起動前に支払う。

---

## Cost Definition

```cpp
struct CostDefinition
{
    int Mana;

    int TapCount;

    bool Sacrifice;

    bool Discard;
};
```

---

## 実行順

```text
Activate

↓

Pay Cost

↓

Ability Resolve
```

---

支払えない場合

```text
発動失敗
```

---

# 8. Target Selection

対象選択システム。

---

## Target Selector

```cpp
enum class TargetSelectorType
{
    None,

    Self,

    Opponent,

    Creature,

    Player,

    AnyCard
};
```

---

## Resolve

```cpp
TargetResolver
```

が対象を確定する。

---

# 9. Effect System

実際の能力解決。

---

## 基本構造

```cpp
EffectSystem::Execute(
    Effect,
    Value,
    State,
    Event,
    Engine);
```

---

## 標準効果

### Draw

```text
カードを引く
```

---

### Destroy

```text
破壊
```

---

### ReturnToHand

```text
手札へ戻す
```

---

### AddPower

```text
パワー増加
```

---

### AddAbility

```text
能力付与
```

---

### SearchDeck

```text
山札探索
```

---

### Shuffle

```text
山札シャッフル
```

---

### PrintMessage

デバッグ専用

---

# 10. Replacement Effects

イベント発生前に介入。

---

例

```text
Destroy

↓

Replace

↓

MoveToHand
```

---

## 優先順位

後から適用されたものを優先。

---

# 11. Continuous Effects

状態を直接保持しない。

---

禁止

```cpp
Creature.Power += 2000;
```

---

許可

```cpp
ContinuousEffect
```

から再計算。

---

# 12. Ability Queue

能力もQueueで管理。

---

```text
Ability Register

↓

Ability Queue

↓

Resolve
```

---

FIFO方式。

---

# 13. Ability Resolver

能力解決の中核。

---

```cpp
AbilityResolver
```

処理順

```text
Trigger

↓

Condition

↓

Zone

↓

Cost

↓

Target

↓

Effect
```

---

# 14. Ability Manager

全能力処理統括。

---

責務

* Trigger収集
* Ability生成
* Queue投入
* Resolver呼び出し

---

## API

```cpp
AbilityManager::ProcessEvent(
    State,
    Event,
    Engine,
    Registry);
```

---

# 15. Ability Executor

個別能力の実行。

---

```cpp
AbilityExecutor::ProcessEvent(
    State,
    Event,
    Engine,
    Registry);
```

---

役割

```text
AbilityInstance

↓

AbilityDefinition

↓

Resolve
```

---

# 16. DSL対応

将来的にJSONからロード可能とする。

例

```json
{
  "trigger":"Attack",
  "condition":"Always",
  "target":"OpponentCreature",
  "effect":"Destroy"
}
```

---

# 17. 実行順

能力解決の最終順序。

```text
Event

↓

Trigger Match

↓

Condition Check

↓

Zone Check

↓

Cost Check

↓

Target Resolve

↓

Replacement

↓

Effect Execute

↓

Continuous Recalculate

↓

SBA

↓

Victory Check
```

---

# 18. 完全再現原則

能力の裁定は常に公式裁定を優先する。

```text
公式裁定
＞
DMVM2仕様
＞
実装
```

能力システムはCoreRulesに従う。
