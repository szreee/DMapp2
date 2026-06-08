# 09D_DMVM2_ActionEventAPIContracts.md

# DMVM2 Action & Event API Contracts Specification v1.0

## 目的

本仕様書はDMVM2におけるAction/Event層のAPI契約を定義する。

対象:

* IAction
* ActionFactory
* DrawCardAction
* MoveCardAction
* DestroyAction
* SummonAction
* AttackAction
* GameEvent
* EventQueue
* EventDispatcher
* EventListener

本仕様書で定義された公開APIは原則変更禁止とする。

---

# 1. 設計原則

## 1.1 Actionは意図を表現する

Actionは「何をしたいか」を表現する。

禁止:

```cpp
Player.Hand.Add(Card);
```

```cpp
Card.MoveToZone();
```

許可:

```cpp
DrawCardAction
```

```cpp
MoveCardAction
```

---

## 1.2 Eventは事実を表現する

Action実行後に発生した事実をEventとして記録する。

例:

```text
DrawCardAction

↓

CardDrawn Event
```

---

## 1.3 Direct Mutation禁止

Action以外による状態変更は禁止。

---

# 2. IAction

## 責務

全Actionの基底契約。

---

## Ownership

所有しない。

GameStateはRuleEngine所有。

---

## Public API

```cpp
class IAction
{
public:

    virtual ~IAction() = default;

    virtual ActionType Type() const = 0;

    virtual void Execute(
        GameState& State) = 0;
};
```

---

## Lifetime

ActionQueue投入後、

実行完了時破棄。

---

# 3. ActionFactory

## 責務

Action生成。

---

## Ownership

生成したActionの所有権を呼び出し側へ移譲。

---

## Public API

```cpp
class ActionFactory
{
public:

    static std::unique_ptr<IAction>
    CreateDrawCard(
        PlayerID Player,
        int Count);

    static std::unique_ptr<IAction>
    CreateMoveCard(
        ObjectID Card,
        ZoneType From,
        ZoneType To);

    static std::unique_ptr<IAction>
    CreateDestroy(
        ObjectID Card);

    static std::unique_ptr<IAction>
    CreateSummon(
        ObjectID Card);

    static std::unique_ptr<IAction>
    CreateAttack(
        ObjectID Attacker,
        ObjectID Target);
};
```

---

## Lifetime

Static Utility。

状態保持禁止。

---

# 4. DrawCardAction

## 責務

カードを引く。

---

## Public API

```cpp
class DrawCardAction
    : public IAction
{
public:

    DrawCardAction(
        PlayerID Player,
        int Count);

    ActionType Type() const override;

    void Execute(
        GameState& State) override;
};
```

---

## Private Members

```cpp
PlayerID Player;

int Count;
```

---

## 発行Event

```text
CardDrawn
```

---

# 5. MoveCardAction

## 責務

ゾーン移動。

---

## Public API

```cpp
class MoveCardAction
    : public IAction
{
public:

    MoveCardAction(
        ObjectID Card,
        ZoneType From,
        ZoneType To);

    ActionType Type() const override;

    void Execute(
        GameState& State) override;
};
```

---

## Private Members

```cpp
ObjectID Card;

ZoneType FromZone;

ZoneType ToZone;
```

---

## 発行Event

```text
BeforeMove

Move

AfterMove
```

---

# 6. DestroyAction

## 責務

破壊処理。

---

## Public API

```cpp
class DestroyAction
    : public IAction
{
public:

    explicit DestroyAction(
        ObjectID Card);

    ActionType Type() const override;

    void Execute(
        GameState& State) override;
};
```

---

## Private Members

```cpp
ObjectID TargetCard;
```

---

## 発行Event

```text
Destroy
```

---

## 備考

実際の墓地移動は

MoveCardActionへ委譲する。

---

# 7. SummonAction

## 責務

召喚処理。

---

## Public API

```cpp
class SummonAction
    : public IAction
{
public:

    explicit SummonAction(
        ObjectID Card);

    ActionType Type() const override;

    void Execute(
        GameState& State) override;
};
```

---

## Private Members

```cpp
ObjectID Card;
```

---

## 発行Event

```text
Summon
```

---

# 8. AttackAction

## 責務

攻撃宣言。

---

## Public API

```cpp
class AttackAction
    : public IAction
{
public:

    AttackAction(
        ObjectID Attacker,
        ObjectID Target);

    ActionType Type() const override;

    void Execute(
        GameState& State) override;
};
```

---

## Private Members

```cpp
ObjectID AttackerID;

ObjectID TargetID;
```

---

## 発行Event

```text
Attack
```

---

# 9. GameEvent

## 責務

ゲーム内事実の表現。

---

## Public API

```cpp
struct GameEvent
{
    EventID ID;

    EventType Type;

    ObjectID Source;

    ObjectID Target;

    PlayerID Controller;

    uint64_t Timestamp;
};
```

---

## Ownership

値型。

コピー可能。

---

## Lifetime

EventQueue解決完了まで保持。

---

# 10. EventQueue

## 責務

イベント順序保証。

---

## Ownership

GameEventを所有。

---

## Public API

```cpp
class EventQueue
{
public:

    void Push(
        const GameEvent& Event);

    bool Empty() const;

    size_t Size() const;

    GameEvent Pop();

    void Clear();
};
```

---

## Private Members

```cpp
deque<GameEvent>
Queue;
```

---

## Lifetime

GameStateと同一。

---

# 11. EventListener

## 責務

イベント受信契約。

---

## Public API

```cpp
class IEventListener
{
public:

    virtual ~IEventListener()
        = default;

    virtual void OnEvent(
        const GameEvent&)
        = 0;
};
```

---

## 実装対象

```text
AbilityManager

TriggerSystem

ReplacementSystem
```

---

# 12. EventDispatcher

## 責務

イベント配信。

---

## Ownership

Listenerを所有しない。

---

## Public API

```cpp
class EventDispatcher
{
public:

    void Subscribe(
        EventType,
        IEventListener*);

    void Unsubscribe(
        EventType,
        IEventListener*);

    void Dispatch(
        const GameEvent&);
};
```

---

## Private Members

```cpp
unordered_map<
    EventType,
    vector<IEventListener*>>
Listeners;
```

---

## Lifetime

RuleEngineと同一。

---

# 13. ActionType

```cpp
enum class ActionType
{
    DrawCard,

    MoveCard,

    Destroy,

    Summon,

    Attack,

    CastSpell,

    Tap,

    Untap
};
```

---

# 14. EventType

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

    BeforeMove,

    AfterMove,

    Tap,

    Untap,

    TurnStart,

    TurnEnd
};
```

---

# 15. Ownership Summary

## RuleEngine所有

```text
EventDispatcher
```

---

## ActionQueue所有

```text
IAction
```

---

## EventQueue所有

```text
GameEvent
```

---

## 非所有

```text
GameState&
ObjectRegistry&
```

---

# 16. Lifetime Summary

## Match Lifetime

```text
RuleEngine

EventDispatcher
```

---

## Turn Lifetime

```text
GameEvent
```

---

## Queue Lifetime

```text
IAction
```

---

# 17. Action/Event実行モデル

```text
Player Input

↓

IAction

↓

ActionQueue

↓

Execute

↓

GameEvent

↓

EventQueue

↓

EventDispatcher

↓

TriggerSystem

↓

AbilityManager

↓

AbilityResolver

↓

SBA

↓

VictoryCheck
```

---

# 18. 完全再現原則

Action/Event層はルールを実装するのみ。

```text
CoreRules
↓
AbilitySystem
↓
CardSystem
↓
ActionEventSystem
↓
ActionEventAPIContracts
```

実装は本契約に従う。

契約を実装都合で変更することは禁止。
