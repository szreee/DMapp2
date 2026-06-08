13_DMVM2_BuildPipelineSpecification.md
DMVM2 Build Pipeline Specification v1.0
目的

本仕様書はDMVM2のビルド・テスト・データ生成・リリース工程を定義する。

対象

CMake
Build Pipeline
CI/CD
Package Pipeline
Data Pipeline
Card Generation
DSL Validation
Automated Testing

本仕様書以降は開発環境を固定する。

1. 基本原則
1.1 Single Source Of Truth

全成果物は仕様書とDSLから生成される。

禁止

コード直接修正
↓
仕様未更新

許可

仕様更新
↓
DSL更新
↓
生成
1.2 Deterministic Build

同じ入力から必ず同じ成果物を生成する。

1.3 Build Reproducibility

以下を固定する。

Compiler
Engine Version
Dependencies
Schema Version
2. Build Environment
OS

対応

Windows 11
Ubuntu 24.04
Compiler
Windows
MSVC 2022
Linux
clang 18
Language
C++20

固定

3. CMake Structure
Root
DMVM2/
├ CMakeLists.txt
├ Source/
├ Tests/
├ Data/
└ Tools/
Main Target
DMVM2Core
Sub Targets
DMVM2Card
DMVM2Ability
DMVM2Rule
DMVM2Database
DMVM2Tests
4. Build Configuration
Debug
Assertions ON
Logs ON
Profiling ON
Release
Optimization ON
Logs Minimal
Assertions OFF
5. Dependency Policy
原則

外部依存最小化。

許可

nlohmann/json
GoogleTest
spdlog

禁止

巨大ゲームフレームワーク依存
6. Data Pipeline
入力
Card JSON
Ability JSON
Rule JSON
処理
Schema Validation

↓

Compile DSL

↓

Generate Binary Database
出力
Cards.db
Abilities.db
Rules.db
7. Schema Validation

全JSONはビルド前に検証。

Validation
Required Fields

Type Check

Reference Check

例

{
  "AbilityID":"Draw1"
}

存在確認

AbilityDatabase
8. Card Generation Pipeline
Input
cards/*.json
Output
GeneratedCards.bin

処理

Read

↓

Validate

↓

Compile

↓

Serialize
9. Ability Generation Pipeline
Input
abilities/*.json
Output
GeneratedAbilities.bin

処理

Parse

↓

Validate

↓

Compile DSL

↓

Serialize
10. Test Pipeline
Unit Tests

対象

Core
Card
Ability
Action
Rule
Integration Tests

対象

Turn Flow

Combat

Triggers

Replacement
Replay Tests

対象

Recorded Match
Official Ruling Tests

対象

Official Cases
11. CI Pipeline
Trigger
Push
Pull Request
Release
Flow
Checkout

↓

Build

↓

Schema Validate

↓

Unit Tests

↓

Integration Tests

↓

Replay Tests

↓

Package
12. Coverage Policy

最低値

Core 95%

RuleEngine 95%

Ability 90%

Card 90%

失敗時

Build Failed
13. Static Analysis

実施

clang-tidy

警告

Treat As Error
14. Package Pipeline
Output
DMVM2.exe

Data/

Cards.db

Abilities.db
Package
zip
Release
Version Tag
15. Save Data Pipeline

保存対象

GameState

Players

Registry

Queues

形式

JSON

将来

Binary Save

対応

16. Replay Pipeline

記録

Player Input

のみ。

再現

Input Replay

↓

Deterministic Simulation
17. Versioning

形式

Major.Minor.Patch

例

1.0.0
18. Release Criteria

以下を全て満たすこと。

Build Success

All Tests Pass

Schema Validation Pass

Replay Pass

Ruling Pass
19. Data Compatibility
Forward Compatibility

不要

Backward Compatibility

Minor Version内で保証

20. 完全再現原則
CoreRules
↓
AbilitySystem
↓
CardSystem
↓
ActionEventSystem
↓
DatabaseDSL
↓
Implementation
↓
BuildPipeline
13で凍結されるもの
CMake構造
Build手順
CI/CD手順
Card生成手順
Ability生成手順
DSL検証手順
Package構造
Release条件
Version管理
Replay生成手順