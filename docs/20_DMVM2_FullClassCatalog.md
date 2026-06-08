# 20_DMVM2_FullClassCatalog.md

# DMVM2 Full Class Catalog v1.0

## 目的

本仕様書はDMVM2に存在する全クラスの責務・依存関係・所有権・寿命・公開APIを固定する。

本仕様書は以下の上位仕様とする。

* 07_DMVM2_ClassMap
* 09A～09E API Contracts
* 11_DMVM2_ClassSkeletonSpecification

実装者は本仕様書に従う。

---

# 1. 共通ルール

## Namespace

```cpp
namespace DMVM2
{
}
```

---

## Ownership Rules

### unique_ptr

所有権あり

### shared_ptr

共有所有

### raw pointer

参照のみ

---

## Lifetime Categories

### Application Lifetime

アプリ終了まで存在

### Match Lifetime

対戦開始～終了

### Turn Lifetime

ターン中のみ

### Temporary

解決後破棄

---

# 2. Core Module

---

## GameState

### Module

Core

### Responsibility

ゲーム状態の唯一の真実を保持

### Owner

GameSession

### Lifetime

Match Lifetime

### Depends

* PlayerState
* ObjectRegistry
* TurnState

### Public API

```cpp
PlayerState& GetPlayer(PlayerID);
ObjectRegistry& GetRegistry();
```

### Private Members

```cpp
std::vector<PlayerState> Players;
ObjectRegistry Registry;
TurnState Turn;
```

### Serialization

Yes

---

## PlayerState

### Module

Core

### Responsibility

プレイヤー状態管理

### Owner

GameState

### Lifetime

Match Lifetime

### Depends

None

### Public API

```cpp
PlayerID GetID() const;
```

### Private Members

```cpp
PlayerID ID;
```

### Serialization

Yes

---

## ObjectRegistry

### Module

Core

### Responsibility

全オブジェクト管理

### Owner

GameState

### Lifetime

Match Lifetime

### Depends

* CardInstance

### Public API

```cpp
CardInstance* Find(ObjectID);
void Register(std::unique_ptr<CardInstance>);
```

### Private Members

```cpp
std::unordered_map<ObjectID,
std::unique_ptr<CardInstance>>
Objects;
```

### Ownership

Owns CardInstance

---

# 3. Card Module

---

## CardDefinition

### Responsibility

カード静的定義

### Lifetime

Application Lifetime

### Owner

CardDatabase

### Public API

```cpp
CardID GetID() const;
```

---

## CardInstance

### Responsibility

ゲーム中カード実体

### Lifetime

Match Lifetime

### Owner

ObjectRegistry

### Depends

* CardDefinition

### Public API

```cpp
ObjectID GetID() const;
CardState& State();
```

---

## ZoneManager

### Responsibility

ゾーン移動管理

### Lifetime

Match Lifetime

### Depends

* GameState
* ObjectRegistry

### Public API

```cpp
void MoveCard(
ObjectID,
ZoneType,
ZoneType);
```

---

## CardDatabase

### Responsibility

カード定義検索

### Lifetime

Application Lifetime

### Owner

Application

### Public API

```cpp
const CardDefinition*
Find(CardID) const;
```

---

# 4. Ability Module

---

## AbilityDefinition

### Responsibility

能力定義

### Owner

AbilityDatabase

### Lifetime

Application Lifetime

---

## AbilityInstance

### Responsibility

能力実体

### Owner

CardInstance

### Lifetime

Match Lifetime

---

## ConditionEvaluator

### Responsibility

条件判定

### Lifetime

Match Lifetime

---

## TargetResolver

### Responsibility

対象解決

### Lifetime

Temporary

---

## EffectExecutor

### Responsibility

効果実行

### Lifetime

Match Lifetime

---

## AbilityResolver

### Responsibility

能力解決

### Depends

* ConditionEvaluator
* TargetResolver
* EffectExecutor

---

## AbilityManager

### Responsibility

能力統括

### Depends

* AbilityResolver
* AbilityDatabase

---

# 5. Event Module

---

## GameEvent

### Responsibility

イベント表現

### Lifetime

Temporary

---

## EventQueue

### Responsibility

イベントFIFO管理

### Owner

RuleEngine

### Lifetime

Match Lifetime

---

## EventBus

### Responsibility

イベント通知

### Lifetime

Match Lifetime

---

## EventDispatcher

### Responsibility

イベント配信

### Lifetime

Match Lifetime

---

## EventListener

### Responsibility

イベント受信インターフェース

### Lifetime

Match Lifetime

---

# 6. Action Module

---

## IAction

### Responsibility

全Action基底

---

## ActionQueue

### Responsibility

Action管理

### Owner

RuleEngine

### Lifetime

Match Lifetime

---

## ActionFactory

### Responsibility

Action生成

### Lifetime

Application Lifetime

---

## DrawCardAction

### Responsibility

ドロー

### Lifetime

Temporary

---

## MoveCardAction

### Responsibility

移動

### Lifetime

Temporary

---

## DestroyAction

### Responsibility

破壊

### Lifetime

Temporary

---

## SummonAction

### Responsibility

召喚

### Lifetime

Temporary

---

## AttackAction

### Responsibility

攻撃

### Lifetime

Temporary

---

# 7. Rule Systems

---

## TriggerSystem

### Responsibility

誘発能力検出

### Owner

RuleEngine

### Lifetime

Match Lifetime

---

## ReplacementSystem

### Responsibility

置換効果適用

### Owner

RuleEngine

### Lifetime

Match Lifetime

---

## SBAEngine

### Responsibility

State Based Action

### Owner

RuleEngine

### Lifetime

Match Lifetime

---

## PrioritySystem

### Responsibility

優先権管理

### Owner

RuleEngine

### Lifetime

Match Lifetime

---

## VictorySystem

### Responsibility

勝敗判定

### Owner

RuleEngine

### Lifetime

Match Lifetime

---

## RuleEngine

### Responsibility

ゲーム実行中核

### Owner

GameSession

### Lifetime

Match Lifetime

### Depends

* ActionQueue
* EventQueue
* TriggerSystem
* ReplacementSystem
* SBAEngine
* PrioritySystem
* VictorySystem
* AbilityManager

### Must Not Depend

* UI
* Renderer
* NetworkClient

---

# 8. Database Module

---

## AbilityDatabase

Application Lifetime

---

## ExpansionDatabase

Application Lifetime

---

# 9. Serialization Module

---

## SaveGameSerializer

### Responsibility

保存

---

## LoadGameSerializer

### Responsibility

復元

---

## JsonConverter

### Responsibility

JSON変換

---

# 10. Utility Module

---

## Logger

Application Lifetime

---

## Random

Application Lifetime

---

# 11. Freeze Contract

変更禁止

* クラス名
* API名
* Ownership
* Dependency方向
* Lifetime
* Namespace

実装のみ変更可能。
