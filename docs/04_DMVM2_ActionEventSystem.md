# 04_DMVM2_ActionEventSystem.md

# DMVM2 Action & Event System Specification v1.0

## 目的

本仕様書はDMVM2における実行基盤を定義する。

対象

* Action System
* Event System (ETS)
* Event Bus
* Event Queue
* Rule Engine
* Trigger Registration
* Replacement Processing

全ゲーム処理は本仕様書に従う。

---

# 1. 基本原則

## 1.1 Action First

ゲーム内処理は必ずActionから開始する。

禁止

```cpp
Player.Draw();
```

```cpp
Creature.Destroy();
```

許可

```cpp
DrawCardAction
```

```cpp
DestroyAction
```

---

## 1.2 Event Driven

Actionは必ずEventを発行する。

```text
Action

↓

Event

↓

RuleEngine
```

---

## 1.3 Direct State Mutation禁止

禁止

```cpp
Player.Hand.Add(Card);
```

許可

```cpp
MoveCardAction
```

---

# 2. Action System

## IAction

全Actionの基底。

```cpp
class IAction
{
public:

    virtual ~IAction() = default;

    virtual void Execute(
        GameState& State) = 0;
};
```

---

## Action責務

Actionは

* 状態変更
* Event発行

のみ行う。

---

# 3. 標準Action

## DrawCardAction

カードを引く。

---

## MoveCardAction

ゾーン移動。

---

## SummonAction

召喚。

---

## CastSpellAction

呪文使用。

---

## AttackAction

攻撃宣言。

---

## DestroyAction

破壊。

---

## TapAction

タップ。

---

## UntapAction

アンタップ。

---

# 4. Action Queue

全ActionはQueue管理。

---

## 構造

```cpp
class ActionQueue
{
public:

    void Push(
        std::unique_ptr<IAction> Action);

    bool Empty() const;

    void ExecuteAll(
        GameState& State);
};
```

---

## FIFO

```text
Action1

Action2

Action3
```

↓

```text
Action1

Action2

Action3
```

順に実行。

---

## 実行中追加

許可。

```text
Action1

↓

Action追加

↓

Queue末尾
```

---

# 5. Event System

全ActionはEventを発行する。

---

## GameEvent

```cpp
struct GameEvent
{
    EventType Type;

    ObjectID Source;

    ObjectID Target;

    PlayerID Controller;
};
```

---

## EventType

```cpp
enum class EventType
{
    Draw,

    Summon,

    Cast,

    Attack,

    Block,

    Destroy,

    Move,

    Tap,

    Untap,

    TurnStart,

    TurnEnd
};
```

---

# 6. Event Queue

イベントもQueue管理。

---

## EventQueue

```cpp
class EventQueue
{
public:

    void Push(
        const GameEvent& Event);

    bool Empty() const;

    GameEvent Pop();
};
```

---

## FIFO

イベントは発生順で解決。

---

# 7. Event Bus

イベント配信機構。

---

## 責務

* Trigger通知
* Replacement通知
* Ability通知

---

## API

```cpp
EventBus::Publish(
    Event);
```

---

# 8. Trigger Registration

イベント解決後に行う。

---

## 手順

```text
Event Resolve

↓

Trigger Search

↓

Ability生成

↓

AbilityQueue
```

---

# 9. Replacement Processing

イベント解決前に行う。

---

## 手順

```text
Event

↓

Replacement Check

↓

Replace

↓

Resolve
```

---

## 例

```text
Destroy
```

↓

```text
MoveToHand
```

---

# 10. Rule Engine

DMVM2中核。

---

## 責務

* Action処理
* Event処理
* Trigger登録
* Replacement処理
* SBA実行
* 勝敗判定

---

## API

```cpp
class RuleEngine
{
public:

    void Run(
        GameState& State);

    void QueueAction(
        std::unique_ptr<IAction> Action);
};
```

---

# 11. Rule Engine 実行順

最重要仕様。

---

```text
ActionQueue

↓

Action Execute

↓

Event Generate

↓

Replacement

↓

Event Resolve

↓

Trigger Register

↓

Ability Queue

↓

Ability Resolve

↓

Continuous Recalculate

↓

SBA

↓

Victory Check

↓

Priority
```

---

# 12. Event Generation

Actionは必ずイベントを生成。

---

例

```text
DrawCardAction
```

↓

```text
CardDrawn Event
```

---

例

```text
DestroyAction
```

↓

```text
Destroy Event
```

---

# 13. Event Ordering

複数イベントは順番維持。

---

```text
A

↓

B

↓

C
```

---

順序変更禁止。

---

# 14. Nested Actions

許可。

---

例

```text
Draw
```

↓

```text
OnDraw
```

↓

```text
Draw
```

---

Queueに追加して処理。

再帰禁止。

---

# 15. Trigger Interaction

Triggerは直接実行しない。

---

禁止

```cpp
Trigger->Execute();
```

---

許可

```text
Trigger

↓

Ability Queue

↓

Resolve
```

---

# 16. Replacement Interaction

ReplacementはEventを変更する。

---

禁止

```text
Destroy

↓

Destroy
```

---

許可

```text
Destroy

↓

MoveToHand
```

---

# 17. Continuous Effect Interaction

Action中に直接反映しない。

---

```text
Ability Resolve

↓

Recalculate
```

---

# 18. SBA Interaction

毎回実行。

---

タイミング

```text
Ability Resolve後
```

```text
Action Resolve後
```

---

# 19. Priority Interaction

RuleEngineが管理。

---

```text
Resolve

↓

Priority
```

---

全員Pass

↓

次処理

---

# 20. Debug Logging

全処理を記録可能。

---

例

```text
Action:
DrawCard
```

```text
Event:
CardDrawn
```

```text
Trigger:
DrawOnDraw
```

---

# 21. Serialization

保存対象。

---

```text
ActionQueue
```

```text
EventQueue
```

```text
GameState
```

---

# 22. 完全再現原則

エンジンは裁定を実装するだけ。

---

```text
CoreRules

↓

AbilitySystem

↓

CardSystem

↓

ActionEventSystem
```

---

実装が仕様に従う。

仕様が実装に従うことは禁止。

---

# 23. DMVM2実行モデル

```text
Player Input

↓

Action

↓

Event

↓

Replacement

↓

Trigger

↓

Ability

↓

Continuous

↓

SBA

↓

Victory

↓

Priority

↓

Next Action
```

---

本仕様書により

* IAction
* ActionQueue
* EventQueue
* EventBus
* GameEvent
* RuleEngine

の構造を凍結する。
