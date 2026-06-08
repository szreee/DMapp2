# 09E_DMVM2_RuleSystemsAPIContracts.md

# DMVM2 Rule Systems API Contracts Specification v1.0

## 目的

本仕様書はDMVM2におけるルールシステム層のAPI契約を定義する。

対象:

* TriggerSystem
* ReplacementSystem
* SBAEngine
* PrioritySystem
* VictorySystem

本仕様書で定義された公開APIは原則変更禁止とする。

---

# 1. 設計原則

## 1.1 RuleSystemは状態を所有しない

RuleSystemはルール評価のみ行う。

ゲーム状態の所有者は常にGameStateである。

---

禁止

```cpp
class TriggerSystem
{
    std::vector<CardInstance*> Cards;
};
```

---

許可

```cpp
TriggerSystem::ProcessEvent(
    State,
    Event,
    AbilityManager);
```

---

## 1.2 RuleSystemはAction/Eventを生成する

ルールシステムは直接状態変更を行わない。

---

禁止

```cpp
Card->Zone = Graveyard;
```

---

許可

```cpp
MoveCardAction
```

---

# 2. TriggerSystem

## 責務

イベントから誘発能力を検出する。

---

## Ownership

所有しない。

---

## Lifetime

Match Lifetime

RuleEngine所有。

---

## Public API

```cpp
class TriggerSystem
{
public:

    void ProcessEvent(
        GameState& State,
        const GameEvent& Event,
        AbilityManager& Abilitys);
};
```

---

## Private Members

なし

状態保持禁止。

---

## Dependencies

```text
GameState

AbilityManager

GameEvent
```

---

## 実行フロー

```text
Event

↓

Trigger Match

↓

Condition Check

↓

Ability Instance生成

↓

Ability Queue
```

---

# 3. ReplacementSystem

## 責務

イベント解決前に置換効果を適用する。

---

## Ownership

所有しない。

---

## Lifetime

Match Lifetime

RuleEngine所有。

---

## Public API

```cpp
class ReplacementSystem
{
public:

    bool Apply(
        GameState& State,
        GameEvent& Event);
};
```

---

## 戻り値

```cpp
true
```

イベント変更あり

```cpp
false
```

変更なし

---

## Private Members

なし

---

## Dependencies

```text
GameState

GameEvent
```

---

## 実行フロー

```text
Event

↓

Replacement Search

↓

Priority Resolve

↓

Event Rewrite
```

---

# 4. SBAEngine

## 責務

State Based Actionを実行する。

---

## Ownership

所有しない。

---

## Lifetime

Match Lifetime

RuleEngine所有。

---

## Public API

```cpp
class SBAEngine
{
public:

    bool Execute(
        GameState& State);
};
```

---

## 戻り値

```cpp
true
```

状態変化あり

```cpp
false
```

状態変化なし

---

## Private Members

なし

---

## Dependencies

```text
GameState

ActionFactory
```

---

## 実行フロー

```text
SBA Evaluate

↓

Illegal State Found

↓

Action Generate

↓

State Stable

↓

Finish
```

---

# 5. SBA Rules

## Power 0以下

```text
Power <= 0
```

↓

```text
MoveToGraveyard
```

---

## 山札切れ

```text
Draw Impossible
```

↓

```text
Lose
```

---

## 不正ゾーン状態

```text
Card Exists Multiple Zones
```

↓

```text
Repair State
```

---

## ルール固有SBA

公式裁定準拠。

---

# 6. PrioritySystem

## 責務

優先権管理。

---

## Ownership

RuleEngine所有。

---

## Lifetime

Match Lifetime

---

## Public API

```cpp
class PrioritySystem
{
public:

    void GivePriority(
        PlayerID Player);

    void PassPriority(
        PlayerID Player);

    bool AllPlayersPassed() const;

    PlayerID CurrentPlayer() const;
};
```

---

## Private Members

```cpp
PlayerID Current;

std::vector<bool> PassState;
```

---

## Dependencies

```text
PlayerState
```

---

## 実行フロー

```text
Resolve

↓

Priority Granted

↓

Pass

↓

All Pass

↓

Next Step
```

---

# 7. Priority Rules

## Priority取得

```text
Action Resolve後
```

```text
Ability Resolve後
```

```text
Turn Start後
```

---

## Priority消失

```text
Action Queue追加
```

---

# 8. VictorySystem

## 責務

勝敗判定。

---

## Ownership

RuleEngine所有。

---

## Lifetime

Match Lifetime

---

## Public API

```cpp
class VictorySystem
{
public:

    void Check(
        GameState& State);

    bool HasWinner() const;

    PlayerID Winner() const;
};
```

---

## Private Members

```cpp
bool Finished;

PlayerID WinnerID;
```

---

## Dependencies

```text
GameState
```

---

## 勝利条件

### 通常勝利

```text
Opponent Lose
```

---

### 山札切れ

```text
Draw Failure
```

↓

```text
Lose
```

---

### 特殊勝利

カード能力。

---

### 特殊敗北

カード能力。

---

# 9. Rule Systems Ownership Matrix

| System            | Owner      | Lifetime |
| ----------------- | ---------- | -------- |
| TriggerSystem     | RuleEngine | Match    |
| ReplacementSystem | RuleEngine | Match    |
| SBAEngine         | RuleEngine | Match    |
| PrioritySystem    | RuleEngine | Match    |
| VictorySystem     | RuleEngine | Match    |

---

# 10. Rule Systems Dependency Graph

```text
RuleEngine

├ TriggerSystem

├ ReplacementSystem

├ SBAEngine

├ PrioritySystem

└ VictorySystem
```

---

# 11. Rule Engine Integration

```text
Action

↓

Event

↓

ReplacementSystem

↓

Event Resolve

↓

TriggerSystem

↓

Ability Queue

↓

Ability Resolve

↓

Continuous Recalculate

↓

SBAEngine

↓

VictorySystem

↓

PrioritySystem
```

---

# 12. API Stability Contract

以下はv1.0以降変更禁止。

```text
TriggerSystem Public API

ReplacementSystem Public API

SBAEngine Public API

PrioritySystem Public API

VictorySystem Public API
```

---

変更可能なもの

```text
内部アルゴリズム

最適化

キャッシュ
```

---

# 13. 完全再現原則

```text
CoreRules
↓
AbilitySystem
↓
CardSystem
↓
ActionEventSystem
↓
RuleSystemsAPIContracts
↓
Implementation
```

実装は契約に従う。

契約を実装都合で変更することは禁止。

---

本仕様書により

* TriggerSystem
* ReplacementSystem
* SBAEngine
* PrioritySystem
* VictorySystem

の public API / private member / ownership / lifetime / dependency を凍結する。
