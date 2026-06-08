# 21_DMVM2_SourceGenerationStrategy.md

# DMVM2 Source Generation Strategy v2.0

## 目的

本仕様書はDMVM2の全ソースコード生成方法を定義する。

対象

* Source Generation
* Package Format
* Deployment Script
* ChatGPT Free Plan対応
* Source Expansion

DMVM2 v1.0の正式なコード生成手順とする。

---

# 1. 基本方針

DMVM2は直接ソースを生成しない。

まずPackageを生成する。

---

生成手順

```text
仕様書

↓

Package生成

↓

Package保存

↓

Deploy Script

↓

ソース展開

↓

コンパイル
```

---

# 2. Package方式

生成物は以下形式とする。

```text
DMVM2_PACKAGE_01.txt

DMVM2_PACKAGE_02.txt

DMVM2_PACKAGE_03.txt

...
```

---

## 理由

ChatGPT Free Planでは

1回答の出力量に制限がある。

そのため

```text
全300ファイル
```

を一度に出力することは不可能。

---

Package単位で分割する。

---

# 3. Package Size Rule

1 Packageあたり

```text
フリープラン1回答で
安全に出力可能な量
```

を上限とする。

---

目安

```text
10〜20ファイル
```

---

または

```text
500〜1200行
```

程度。

---

# 4. Package Naming

形式

```text
DMVM2_PACKAGE_XX.txt
```

---

例

```text
DMVM2_PACKAGE_01.txt

DMVM2_PACKAGE_02.txt

DMVM2_PACKAGE_03.txt
```

---

# 5. Package Contents

Packageは複数ファイルを含む。

---

例

```text
=== FILE:
Source/DMVM2/Core/Types.h
===

<内容>

=== FILE:
Source/DMVM2/Core/Types.cpp
===

<内容>
```

---

# 6. File Header Rule

全ファイルは

FILEタグで開始する。

---

形式

```text
=== FILE:
Relative/Path/File.ext
===
```

---

例

```text
=== FILE:
Source/DMVM2/Core/GameState.h
===
```

---

# 7. Package Structure

## PACKAGE_01

Core Foundation

---

内容

```text
Types

Enums

GameState

PlayerState

GameContext
```

---

## PACKAGE_02

Registry

---

内容

```text
ObjectRegistry

ObjectID
```

---

## PACKAGE_03

Card

---

内容

```text
CardDefinition

CardInstance

CardState
```

---

## PACKAGE_04

Zone

---

内容

```text
ZoneManager

ZoneType
```

---

## PACKAGE_05

Database

---

内容

```text
CardDatabase

AbilityDatabase

ExpansionDatabase
```

---

## PACKAGE_06

Ability Core

---

内容

```text
AbilityDefinition

AbilityInstance
```

---

## PACKAGE_07

Ability Resolution

---

内容

```text
AbilityManager

AbilityResolver
```

---

## PACKAGE_08

Ability Support

---

内容

```text
ConditionEvaluator

TargetResolver

EffectExecutor
```

---

## PACKAGE_09

Action Core

---

内容

```text
IAction

ActionQueue

ActionFactory
```

---

## PACKAGE_10

Action Implementations

---

内容

```text
DrawCardAction

MoveCardAction

DestroyAction

SummonAction

AttackAction
```

---

## PACKAGE_11

Event Core

---

内容

```text
GameEvent

EventQueue

EventBus
```

---

## PACKAGE_12

Event Dispatch

---

内容

```text
EventDispatcher

EventListener
```

---

## PACKAGE_13

Rule Engine

---

内容

```text
RuleEngine
```

---

## PACKAGE_14

Rule Systems

---

内容

```text
TriggerSystem

ReplacementSystem
```

---

## PACKAGE_15

Rule Support

---

内容

```text
SBAEngine

PrioritySystem

VictorySystem
```

---

## PACKAGE_16

Serialization

---

内容

```text
JsonConverter

SaveGameSerializer

LoadGameSerializer
```

---

## PACKAGE_17

Utilities

---

内容

```text
Logger

Random
```

---

## PACKAGE_18

Tests

---

内容

```text
UnitTest

IntegrationTest

ReplayTest
```

---

## PACKAGE_19

Build

---

内容

```text
CMake

CI/CD

Packaging
```

---

## PACKAGE_20

Data

---

内容

```text
Card DSL

Ability DSL

JSON Schema
```

---

# 8. Deployment Script

展開スクリプトを1本だけ作成する。

---

名称

```text
extract_package.py
```

---

# 9. Deployment Rule

以下コマンドで実行する。

```bash
python extract_package.py
    DMVM2_PACKAGE_01.txt
```

---

結果

```text
Source/
 └ DMVM2/
    ├ Core/
    │ ├ Types.h
    │ ├ Types.cpp
    │ ├ GameState.h
    │ └ GameState.cpp
```

自動生成。

---

# 10. Multiple Package Support

複数Package同時展開可能。

---

例

```bash
python extract_package.py \
    DMVM2_PACKAGE_01.txt \
    DMVM2_PACKAGE_02.txt \
    DMVM2_PACKAGE_03.txt
```

---

# 11. Final Build Flow

```text
20仕様書

↓

20 Package

↓

extract_package.py

↓

全ソース生成

↓

CMake

↓

Build
```

---

# 12. Freeze Contract

以下を固定する。

* Package形式
* FILEタグ形式
* Package命名規則
* Deploy方式
* extract_package.py

以後変更禁止。

DMVM2 v1.0は本方式を正式採用する。
