# 19_DMVM2_ImplementationOrderSpecification.md

# DMVM2 Implementation Order Specification v1.0

## 目的

本仕様書はDMVM2の実装順序を固定する。

対象

* Source生成
* UnitTest生成
* Integration生成
* CI生成
* Card DSL生成
* Server生成
* Client生成

全実装は本仕様書に従う。

---

# 1. 基本原則

## 1.1 下位依存禁止

未実装モジュールへ依存してはならない。

禁止

```text
Client
↓
RuleEngine未実装
```

---

## 1.2 実装順序固定

実装順は変更してはならない。

---

## 1.3 インターフェース先行

必ずAPI Contractから実装する。

順序

```text
Specification

↓

API Contract

↓

Class Skeleton

↓

Implementation
```

---

# 2. Phase 0

## Specification Freeze

対象

```text
01
02
03
04
05
06
07
08
09A
09B
09C
09D
09E
10
11
12
13
14
15
16
17
18
19
```

完了条件

```text
仕様変更停止
```

---

# 3. Phase 1

## Core Foundation

対象

```text
GameState
PlayerState
ObjectRegistry
ZoneManager
CardDatabase
AbilityDatabase
```

依存

なし

---

完了条件

```text
Build Success
```

---

# 4. Phase 2

## Event Infrastructure

対象

```text
GameEvent
EventQueue
EventDispatcher
EventBus
EventListener
```

依存

```text
Phase1
```

---

# 5. Phase 3

## Action System

対象

```text
IAction
ActionFactory

DrawCardAction
MoveCardAction
DestroyAction
SummonAction
AttackAction
```

依存

```text
Phase2
```

---

# 6. Phase 4

## Ability Foundation

対象

```text
AbilityDefinition
AbilityInstance
ConditionEvaluator
TargetResolver
EffectExecutor
```

依存

```text
Phase1
Phase2
```

---

# 7. Phase 5

## Rule Systems

対象

```text
TriggerSystem
ReplacementSystem
SBAEngine
PrioritySystem
VictorySystem
```

依存

```text
Phase4
```

---

# 8. Phase 6

## Ability Runtime

対象

```text
AbilityResolver
AbilityManager
```

依存

```text
Phase5
```

---

# 9. Phase 7

## Rule Engine

対象

```text
RuleEngine
```

統合

```text
Action
Event
Ability
Rule
```

---

完了条件

```text
1ターン進行可能
```

---

# 10. Phase 8

## DSL Runtime

対象

```text
CardLoader
AbilityLoader
JSON Validator
DSL Parser
```

依存

```text
RuleEngine
```

---

# 11. Phase 9

## Card Runtime

対象

```text
Creature
Spell
CrossGear
Fortress
D2Field
DGField
```

DSLから生成。

---

完了条件

```text
カードコードゼロ
```

---

# 12. Phase 10

## Replay System

対象

```text
ReplayRecorder
ReplayPlayer
SnapshotSerializer
```

---

# 13. Phase 11

## Test Infrastructure

対象

```text
Unit Test
Integration Test
Replay Test
Official Ruling Test
```

---

# 14. Phase 12

## Network Server

対象

```text
GameServer
MatchServer
LobbyServer
AccountServer
```

依存

```text
RuleEngine
```

---

# 15. Phase 13

## Client

対象

```text
UI
DeckBuilder
Collection
ReplayViewer
```

依存

```text
Network
```

---

# 16. Phase 14

## Production Pipeline

対象

```text
CMake
CI
CD
Package
Asset Pipeline
```

---

# 17. Phase 15

## Official Compatibility

対象

```text
Official Ruling Test
```

目標

```text
100%
```

---

# 18. 実装禁止事項

禁止

```cpp
class BolshackDragon
{
};
```

---

禁止

```cpp
Card->Attack();
```

---

禁止

```cpp
Player->Draw();
```

---

必須

```cpp
Action
↓
Event
↓
RuleEngine
```

---

# 19. Definition of Done

DMVM2完成条件

* 全仕様書実装済
* 全テスト成功
* Replay一致
* Server動作
* Client動作
* DSLのみでカード追加可能
* 公式裁定互換

---

# 20. 最終到達状態

DMVM2は

```text
RuleEngine

+
Card DSL

+
Official Ruling Tests
```

のみで新カードを実装できる状態を完成形とする。

仕様書追加以外でアーキテクチャ変更は禁止。
