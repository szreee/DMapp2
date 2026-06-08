# 10_DMVM2_SourceTreeSpecification.md

# DMVM2 Source Tree Specification v1.0

## 目的

本仕様書はDMVM2のSource配下の全ソースファイル構成を定義する。

対象:

* .h
* .cpp
* Module構造
* Dependency構造
* Ownership境界

本仕様書に記載されたファイル構成はv1系で固定とする。

---

# 1. Root Structure

```text
Source/

└ DMVM2/

    ├ Core/

    ├ Card/

    ├ Ability/

    ├ Action/

    ├ Event/

    ├ RuleEngine/

    ├ Database/

    ├ Serialization/

    ├ Utilities/
```

---

# 2. Core Module

責務:

* 基本状態
* ID
* Enum
* Match情報

---

## Files

```text
Core/

├ GameState.h
├ GameState.cpp

├ PlayerState.h
├ PlayerState.cpp

├ TurnState.h

├ MatchState.h

├ ObjectRegistry.h
├ ObjectRegistry.cpp

├ IDs.h

├ Enums.h

├ Types.h
```

---

## Ownership

```text
GameState

├ PlayerState
├ TurnState
├ MatchState
├ ObjectRegistry
├ ActionQueue
└ EventQueue
```

---

# 3. Card Module

責務:

* カード定義
* カード実体
* ゾーン管理

---

## Files

```text
Card/

├ CardDefinition.h
├ CardDefinition.cpp

├ CardInstance.h
├ CardInstance.cpp

├ CardState.h

├ ZoneManager.h
├ ZoneManager.cpp

├ Metadata.h

├ CostDefinition.h
```

---

## Dependencies

```text
Card

↓

Core
```

---

# 4. Ability Module

責務:

* 能力定義
* 誘発
* 条件
* 対象
* 効果

---

## Files

```text
Ability/

├ AbilityDefinition.h

├ AbilityInstance.h

├ AbilityManager.h
├ AbilityManager.cpp

├ AbilityResolver.h
├ AbilityResolver.cpp

├ ConditionEvaluator.h
├ ConditionEvaluator.cpp

├ TargetResolver.h
├ TargetResolver.cpp

├ EffectExecutor.h
├ EffectExecutor.cpp
```

---

## Dependencies

```text
Ability

↓

Card

↓

Core
```

---

# 5. Action Module

責務:

* Action生成
* Queue管理

---

## Files

```text
Action/

├ IAction.h

├ ActionQueue.h
├ ActionQueue.cpp

├ ActionFactory.h
├ ActionFactory.cpp

├ DrawCardAction.h
├ DrawCardAction.cpp

├ MoveCardAction.h
├ MoveCardAction.cpp

├ DestroyAction.h
├ DestroyAction.cpp

├ SummonAction.h
├ SummonAction.cpp

├ CastSpellAction.h
├ CastSpellAction.cpp

├ AttackAction.h
├ AttackAction.cpp

├ TapAction.h
├ TapAction.cpp

├ UntapAction.h
├ UntapAction.cpp
```

---

## Dependencies

```text
Action

↓

Ability

↓

Card
```

---

# 6. Event Module

責務:

* Event管理
* Event配信

---

## Files

```text
Event/

├ GameEvent.h

├ EventQueue.h
├ EventQueue.cpp

├ EventBus.h
├ EventBus.cpp

├ EventDispatcher.h
├ EventDispatcher.cpp

├ EventListener.h
```

---

## Dependencies

```text
Event

↓

Action

↓

Ability
```

---

# 7. RuleEngine Module

責務:

* ルール実行
* SBA
* 勝敗
* 優先権

---

## Files

```text
RuleEngine/

├ RuleEngine.h
├ RuleEngine.cpp

├ TriggerSystem.h
├ TriggerSystem.cpp

├ ReplacementSystem.h
├ ReplacementSystem.cpp

├ SBAEngine.h
├ SBAEngine.cpp

├ PrioritySystem.h
├ PrioritySystem.cpp

├ VictorySystem.h
├ VictorySystem.cpp

├ ContinuousEffectSystem.h
├ ContinuousEffectSystem.cpp
```

---

## Dependencies

```text
RuleEngine

↓

Event

↓

Action

↓

Ability

↓

Card

↓

Core
```

---

# 8. Database Module

責務:

* 静的定義管理

---

## Files

```text
Database/

├ CardDatabase.h
├ CardDatabase.cpp

├ AbilityDatabase.h
├ AbilityDatabase.cpp

├ ExpansionDatabase.h
├ ExpansionDatabase.cpp
```

---

## Ownership

```text
CardDefinition

AbilityDefinition
```

---

## Lifetime

```text
Server Lifetime
```

---

# 9. Serialization Module

責務:

* Save
* Load
* JSON変換

---

## Files

```text
Serialization/

├ SaveGameSerializer.h
├ SaveGameSerializer.cpp

├ LoadGameSerializer.h
├ LoadGameSerializer.cpp

├ JsonConverter.h
├ JsonConverter.cpp
```

---

# 10. Utilities Module

責務:

* 共通処理

---

## Files

```text
Utilities/

├ Logger.h
├ Logger.cpp

├ Assertions.h

├ Random.h
├ Random.cpp

├ StringUtils.h
├ StringUtils.cpp
```

---

# 11. Dependency Contract

許可:

```text
RuleEngine
 ↓
Event
 ↓
Action
 ↓
Ability
 ↓
Card
 ↓
Core
```

---

禁止:

```text
Card
 ↑
RuleEngine
```

```text
Core
 ↑
Ability
```

```text
Database
 ↑
RuleEngine
```

---

# 12. Generated Source Count

## Header

```text
約45ファイル
```

---

## CPP

```text
約35ファイル
```

---

## Total

```text
約80ソース
```

---

# 13. Implementation Freeze

本仕様書により

* Sourceフォルダ構成
* 全クラス配置
* ファイル名
* モジュール境界
* 依存方向

を凍結する。

---

# 14. Build Order

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

---

# 15. Final Architecture

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
```

DMVM2の全コードはこの構造に従う。
