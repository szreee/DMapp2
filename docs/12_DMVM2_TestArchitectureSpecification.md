12_DMVM2_TestArchitectureSpecification.md
DMVM2 Test Architecture Specification v1.0
目的

本仕様書はDMVM2のテスト構造を定義する。

対象

Unit Test
Integration Test
Replay Test
Official Ruling Test
Regression Test
Performance Test

全テストは本仕様書に従う。

1. 基本原則
1.1 テストは仕様を検証する

禁止

コードに合わせてテストを書く

許可

仕様に合わせてテストを書く

優先順位

Specification

＞

Test

＞

Implementation
1.2 実装変更でテストを書き換えない

正しい動作が変わらない限り

テスト変更禁止。

1.3 Replay First

DMVM2は再現性を保証する。

同一入力

↓

同一結果

を保証する。

2. テスト階層
Unit Test

↓

Integration Test

↓

Replay Test

↓

Official Ruling Test

↓

Performance Test
3. Unit Test

個別クラス検証。

対象
GameState
RuleEngine
ActionQueue
EventQueue
ObjectRegistry
AbilityResolver
原則

外部依存禁止。

例
DrawCardAction

↓

Hand +1
4. Integration Test

複数システム統合検証。

対象
Action
↓
Event
↓
Trigger
↓
Ability
例
Aqua Surfer

召喚

↓

バウンス能力誘発

↓

対象選択

↓

手札へ戻る

期待結果

成功


---

# 5. Replay Test

DMVM2最重要テスト。

---

## 目的

ゲーム再現保証。

---

## 構造

```text
Input Log

↓

Replay

↓

Final State

比較

保存内容
{
  "Turn":5,
  "Action":"Summon",
  "Card":1001
}
判定

最終GameState一致。

6. Official Ruling Test

公式裁定再現。

最優先テスト
Official Ruling
＞
Replay
＞
Integration
＞
Unit
データ構造
{
  "CaseID":"DMRULING001",
  "Description":"Aqua Surfer",
  "ExpectedResult":"ReturnToHand"
}
判定

裁定結果完全一致。

7. Regression Test

不具合再発防止。

構造
Bug

↓

Test Case

↓

Permanent Suite
原則

修正した不具合は

必ず回帰テスト化。

8. Performance Test

性能保証。

測定対象
RuleEngine
AbilityResolver
Replay
指標
Action/sec
Event/sec
Ability Resolve Time
Memory Usage
9. Test Data System
保存場所
Tests/Data

構成

Cards
Abilities
Replay
Rulings
10. Unit Test Structure
Tests/Unit
GameStateTests.cpp
RuleEngineTests.cpp
AbilityResolverTests.cpp
ObjectRegistryTests.cpp
11. Integration Test Structure
Tests/Integration
SummonTests.cpp
BattleTests.cpp
TriggerTests.cpp
ReplacementTests.cpp
12. Replay Test Structure
Tests/Replay
ReplayRunner.cpp

入力

Replay001.json

出力

ExpectedState.json
13. Official Ruling Structure
Tests/Rulings
DMRULING001.json
DMRULING002.json
構成
{
  "InitialState":{},
  "Actions":[],
  "ExpectedState":{}
}
14. Golden State Validation

最終状態比較。

比較対象

Players
Zones
Cards
Effects
Triggers

完全一致。

15. Replay Determinism

決定論保証。

禁止

rand()

許可

SeededRandom
判定

同Seed

↓

同結果

16. Continuous Integration

全Pushで実行。

実行順

Unit

↓

Integration

↓

Replay

↓

Ruling

失敗時

Build Fail
17. Coverage Policy

対象

RuleEngine
AbilityResolver
TriggerSystem
ReplacementSystem

最低

90%
18. Test Naming Rule

形式

Class_Action_Expected

例

RuleEngine_DrawCard_HandIncreased
19. Completion Criteria

DMVM2完成条件。

必須

Unit PASS
Integration PASS
Replay PASS
Official Ruling PASS

加えて

Replay Deterministic
20. DMVM2品質保証モデル
Specification

↓

Implementation

↓

Unit

↓

Integration

↓

Replay

↓

Official Ruling

↓

Release
21. 完全再現原則

DMVM2は

紙DM

の再現を目的とする。

従って

Official Ruling Test

を最終判定とする。

凍結対象

本仕様書により以下を凍結する。

Unit Test Architecture
Integration Test Architecture
Replay System
Official Ruling Validation
Regression Framework
Performance Framework
CI Validation Rules