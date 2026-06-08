# 11_DMVM2_ClassSkeletonSpecification.md

# DMVM2 Class Skeleton Specification v1.0

## 目的

本仕様書はDMVM2に存在する全クラスのスケルトン構造を定義する。

対象:

* Header Layout
* CPP Layout
* Constructor
* Destructor
* Public API
* Private Member
* Include Policy
* Namespace Policy

本仕様書は実装前の最終固定契約である。

---

# 1. Header Rules

全Headerは以下構造を持つ。

```cpp
#pragma once

namespace DMVM2
{

class Example
{
public:

    Example();
    ~Example();

private:

};

}
```

---

# 2. CPP Rules

全CPPは以下構造を持つ。

```cpp
#include "Example.h"

namespace DMVM2
{

Example::Example()
{
}

Example::~Example()
{
}

}
```

---

# 3. Namespace

全コードは統一する。

```cpp
namespace DMVM2
{
}
```

---

# 4. Core Classes

## GameState

### Header

```cpp
class GameState
{
public:

    GameState();

    PlayerState& GetPlayer(PlayerID);

    ObjectRegistry& GetRegistry();

private:

    std::vector<PlayerState> Players;

    ObjectRegistry Registry;
};
```

---

## PlayerState

```cpp
class PlayerState
{
public:

    PlayerID GetID() const;

private:

    PlayerID ID;
};
```

---

## ObjectRegistry

```cpp
class ObjectRegistry
{
public:

    CardInstance* Find(ObjectID);

    void Register(
        std::unique_ptr<CardInstance> Card);

private:

    std::unordered_map<
        ObjectID,
        std::unique_ptr<CardInstance>>
        Objects;
};
```

---

# 5. Card Classes

## CardDefinition

```cpp
class CardDefinition
{
public:

    CardID GetID() const;

private:

    CardID ID;
};
```

---

## CardInstance

```cpp
class CardInstance
{
public:

    ObjectID GetID() const;

    CardState& State();

private:

    ObjectID ID;

    const CardDefinition* Definition;
};
```

---

## ZoneManager

```cpp
class ZoneManager
{
public:

    void MoveCard(
        ObjectID Card,
        ZoneType From,
        ZoneType To);

private:

};
```

---

## CardDatabase

```cpp
class CardDatabase
{
public:

    const CardDefinition*
    Find(CardID ID) const;

private:

    std::unordered_map<
        CardID,
        CardDefinition>
        Cards;
};
```

---

# 6. Ability Classes

## AbilityDefinition

```cpp
class AbilityDefinition
{
public:

    AbilityID GetID() const;

private:

    AbilityID ID;
};
```

---

## AbilityInstance

```cpp
class AbilityInstance
{
public:

    AbilityID GetAbilityID() const;

private:

    AbilityID Ability;
};
```

---

## AbilityManager

```cpp
class AbilityManager
{
public:

    void ProcessEvent(
        GameState& State,
        const GameEvent& Event);

private:

};
```

---

## AbilityResolver

```cpp
class AbilityResolver
{
public:

    void Resolve(
        AbilityInstance& Ability,
        GameState& State);

private:

};
```

---

## ConditionEvaluator

```cpp
class ConditionEvaluator
{
public:

    bool Evaluate(
        ConditionType Condition,
        const GameState& State,
        const GameEvent& Event);

private:

};
```

---

## TargetResolver

```cpp
class TargetResolver
{
public:

    std::vector<ObjectID>
    ResolveTargets(
        const AbilityInstance& Ability,
        GameState& State);

private:

};
```

---

## EffectExecutor

```cpp
class EffectExecutor
{
public:

    void Execute(
        const AbilityInstance& Ability,
        GameState& State);

private:

};
```

---

# 7. Action Classes

## IAction

```cpp
class IAction
{
public:

    virtual ~IAction() = default;

    virtual void Execute(
        GameState& State)=0;
};
```

---

## ActionQueue

```cpp
class ActionQueue
{
public:

    void Push(
        std::unique_ptr<IAction> Action);

    void ExecuteAll(
        GameState& State);

private:

    std::queue<
        std::unique_ptr<IAction>>
        Queue;
};
```

---

## ActionFactory

```cpp
class ActionFactory
{
public:

    static std::unique_ptr<IAction>
    CreateDrawAction();

    static std::unique_ptr<IAction>
    CreateDestroyAction();
};
```

---

## DrawCardAction

```cpp
class DrawCardAction :
    public IAction
{
public:

    void Execute(
        GameState& State) override;
};
```

---

## MoveCardAction

```cpp
class MoveCardAction :
    public IAction
{
public:

    void Execute(
        GameState& State) override;

private:

    ObjectID Card;
};
```

---

## DestroyAction

```cpp
class DestroyAction :
    public IAction
{
public:

    void Execute(
        GameState& State) override;
};
```

---

## SummonAction

```cpp
class SummonAction :
    public IAction
{
public:

    void Execute(
        GameState& State) override;
};
```

---

## AttackAction

```cpp
class AttackAction :
    public IAction
{
public:

    void Execute(
        GameState& State) override;
};
```

---

# 8. Event Classes

## GameEvent

```cpp
struct GameEvent
{
    EventType Type;

    ObjectID Source;

    ObjectID Target;
};
```

---

## EventQueue

```cpp
class EventQueue
{
public:

    void Push(
        const GameEvent& Event);

    GameEvent Pop();

private:

    std::queue<GameEvent> Queue;
};
```

---

## EventBus

```cpp
class EventBus
{
public:

    void Publish(
        const GameEvent& Event);

private:

};
```

---

## EventDispatcher

```cpp
class EventDispatcher
{
public:

    void Dispatch(
        const GameEvent& Event);

private:

};
```

---

## EventListener

```cpp
class EventListener
{
public:

    virtual ~EventListener()=default;

    virtual void OnEvent(
        const GameEvent& Event)=0;
};
```

---

# 9. Rule Systems

## RuleEngine

```cpp
class RuleEngine
{
public:

    void Run(
        GameState& State);

private:

    TriggerSystem Triggers;

    ReplacementSystem Replacements;

    SBAEngine SBA;

    PrioritySystem Priority;

    VictorySystem Victory;
};
```

---

## TriggerSystem

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

## ReplacementSystem

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

## SBAEngine

```cpp
class SBAEngine
{
public:

    bool Execute(
        GameState& State);
};
```

---

## PrioritySystem

```cpp
class PrioritySystem
{
public:

    void PassPriority(
        PlayerID Player);

    bool AllPlayersPassed() const;

private:

    PlayerID CurrentPlayer;
};
```

---

## VictorySystem

```cpp
class VictorySystem
{
public:

    void Check(
        GameState& State);

private:

    bool Finished;
};
```

---

# 10. Database Classes

## AbilityDatabase

```cpp
class AbilityDatabase
{
public:

    const AbilityDefinition*
    Find(AbilityID ID) const;

private:

    std::unordered_map<
        AbilityID,
        AbilityDefinition>
        Abilities;
};
```

---

## ExpansionDatabase

```cpp
class ExpansionDatabase
{
public:

    void LoadExpansion(
        const std::string& Path);
};
```

---

# 11. Serialization Classes

## SaveGameSerializer

```cpp
class SaveGameSerializer
{
public:

    bool Save(
        const GameState& State);
};
```

---

## LoadGameSerializer

```cpp
class LoadGameSerializer
{
public:

    bool Load(
        GameState& State);
};
```

---

## JsonConverter

```cpp
class JsonConverter
{
public:

    static Json ToJson(
        const GameState& State);

    static void FromJson(
        const Json& Data,
        GameState& State);
};
```

---

# 12. Utilities

## Logger

```cpp
class Logger
{
public:

    static void Info(
        const std::string& Message);

    static void Error(
        const std::string& Message);
};
```

---

## Random

```cpp
class Random
{
public:

    static int Next(
        int Min,
        int Max);
};
```

---

# 13. Freeze Contract

以下を変更禁止とする。

* クラス名
* ファイル名
* Public API
* Ownership
* Dependency方向
* Namespace

実装は自由。

APIは固定。

---

# 14. Architecture Freeze

```text
Core
 ↓
Card
 ↓
Ability
 ↓
Action
 ↓
Event
 ↓
RuleEngine
 ↓
Database
 ↓
Serialization
```

DMVM2 v1.0は本構造を正式採用する。
