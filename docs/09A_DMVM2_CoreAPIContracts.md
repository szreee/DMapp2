# 09A_DMVM2_CoreAPIContracts.md

# DMVM2 Core API Contracts Specification v1.0

## 目的

本仕様書はDMVM2の中核クラスのAPI契約を定義する。

対象:

* GameState
* PlayerState
* RuleEngine
* ObjectRegistry
* AbilityManager
* AbilityResolver
* EventBus
* ActionQueue

本仕様書で定義された公開APIは原則変更禁止とする。

---

# 1. 設計原則

## 1.1 Single Source of Truth

ゲーム状態はGameStateのみが保持する。

禁止:

```cpp
RuleEngine::CurrentTurn
AbilityManager::CurrentPlayer
```

許可:

```cpp
GameState::CurrentTurn
GameState::PriorityPlayer
```

---

## 1.2 Ownership明示

全ポインタは所有権を明示する。

```cpp
unique_ptr
shared_ptr
raw pointer(non-owning)
```

---

## 1.3 Server Authoritative

RuleEngineのみ状態変更可能。

---

# 2. GameState

## 責務

ゲーム全体の完全状態を保持する。

## Ownership

所有:

* PlayerState
* TurnState
* MatchState
* Zone情報
* Queue

非所有:

* CardDefinition
* AbilityDefinition

---

## Public API

```cpp
class GameState
{
public:

    PlayerState& GetPlayer(PlayerID);

    const PlayerState& GetPlayer(PlayerID) const;

    ObjectRegistry& Registry();

    ActionQueue& Actions();

    EventQueue& Events();

    TurnState& Turn();

    MatchState& Match();

    void Reset();
};
```

---

## Private Members

```cpp
std::vector<PlayerState> Players;

TurnState TurnData;

MatchState MatchData;

ObjectRegistry RegistryData;

ActionQueue ActionQueueData;

EventQueue EventQueueData;
```

---

## Lifetime

Match開始時生成。

Match終了時破棄。

---

# 3. PlayerState

## 責務

プレイヤー固有状態管理。

---

## Public API

```cpp
class PlayerState
{
public:

    PlayerID ID() const;

    ZoneID Deck() const;

    ZoneID Hand() const;

    ZoneID BattleZone() const;

    ZoneID ManaZone() const;

    ZoneID ShieldZone() const;

    ZoneID Graveyard() const;
};
```

---

## Private Members

```cpp
PlayerID Player;

ZoneID DeckZone;

ZoneID HandZone;

ZoneID BattleZoneData;

ZoneID ManaZoneData;

ZoneID ShieldZoneData;

ZoneID GraveyardZone;
```

---

## Lifetime

GameStateと同一。

---

# 4. ObjectRegistry

## 責務

全CardInstance管理。

---

## Ownership

所有:

```cpp
CardInstance
```

---

## Public API

```cpp
class ObjectRegistry
{
public:

    ObjectID Register(
        std::unique_ptr<CardInstance>);

    CardInstance* Find(
        ObjectID);

    const CardInstance* Find(
        ObjectID) const;

    void Remove(
        ObjectID);

    void Clear();
};
```

---

## Private Members

```cpp
unordered_map<
    ObjectID,
    unique_ptr<CardInstance>>
Objects;
```

---

## Lifetime

GameStateと同一。

---

# 5. ActionQueue

## 責務

Action実行順管理。

---

## Ownership

所有:

```cpp
IAction
```

---

## Public API

```cpp
class ActionQueue
{
public:

    void Push(
        std::unique_ptr<IAction>);

    bool Empty() const;

    size_t Size() const;

    std::unique_ptr<IAction>
    Pop();

    void Clear();
};
```

---

## Private Members

```cpp
deque<
    unique_ptr<IAction>>
Queue;
```

---

## Lifetime

GameStateと同一。

---

# 6. EventBus

## 責務

イベント通知。

状態保持禁止。

---

## Public API

```cpp
class EventBus
{
public:

    void Publish(
        const GameEvent&);

    void Subscribe(
        EventType,
        IEventListener*);
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

# 7. AbilityManager

## 責務

能力収集。

能力生成。

Queue投入。

---

## Ownership

所有しない。

AbilityDefinitionはDatabase管理。

---

## Public API

```cpp
class AbilityManager
{
public:

    void ProcessEvent(
        GameState&,
        const GameEvent&);

    void RegisterTriggeredAbility(
        AbilityInstance);

    void Clear();
};
```

---

## Private Members

```cpp
deque<
    AbilityInstance>
PendingAbilities;
```

---

## Lifetime

RuleEngineと同一。

---

# 8. AbilityResolver

## 責務

Ability解決。

---

## Public API

```cpp
class AbilityResolver
{
public:

    void Resolve(
        AbilityInstance&,
        GameState&);
};
```

---

## Private Members

```cpp
ConditionEvaluator Conditions;

TargetResolver Targets;

EffectExecutor Effects;
```

---

## Lifetime

RuleEngineと同一。

---

# 9. RuleEngine

## 責務

DMVM2唯一の状態変更主体。

---

## Ownership

所有:

* EventBus
* AbilityManager
* AbilityResolver
* TriggerSystem
* ReplacementSystem
* PrioritySystem
* SBA
* VictorySystem

非所有:

* GameState

---

## Public API

```cpp
class RuleEngine
{
public:

    explicit RuleEngine(
        GameState&);

    void Run();

    void Step();

    void QueueAction(
        std::unique_ptr<IAction>);

    bool IsRunning() const;
};
```

---

## Private Members

```cpp
GameState& State;

EventBus Events;

AbilityManager Abilities;

AbilityResolver Resolver;

TriggerSystem Triggers;

ReplacementSystem Replacements;

PrioritySystem Priority;

StateBasedActionSystem SBA;

VictorySystem Victory;
```

---

## Lifetime

Match開始時生成。

Match終了時破棄。

---

# 10. 依存方向

固定。

```text
GameState

↑

ObjectRegistry

↑

ActionQueue

↑

AbilityManager

↑

AbilityResolver

↑

RuleEngine
```

逆依存禁止。

---

# 11. API凍結原則

本仕様書に定義された

* Public API
* Ownership
* Lifetime
* Dependency

はDMVM2 v1系では変更禁止とする。
