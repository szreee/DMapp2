# DMVM2 Core Rules Specification v1.0

## 目的

DMVM2は紙のデュエル・マスターズを可能な限り完全再現することを目的とする。

本仕様書はDMVM2全体の最上位ルール仕様である。

以下の全システムは本仕様書に従う。

* RuleEngine
* Event System (ETS)
* Object Composition System (OCS)
* State Based Action (SBA)
* Trigger System
* Replacement System
* Continuous Effect Layer System
* Cost System
* Zone System

---

# 1. 基本原則

## 1.1 カードは処理を持たない

禁止

```cpp
Card->OnEnterBattleZone();
Card->OnDestroy();
Card->OnAttack();
```

許可

```json
{
  "Abilities":[]
}
```

カードはデータである。

処理は全てRuleEngineが担当する。

---

## 1.2 Server Authoritative

ゲーム状態の決定権は常にサーバーが持つ。

クライアントは要求のみ送信可能。

---

## 1.3 Event Driven

全処理はイベントから開始する。

```text
Action
 ↓
Event
 ↓
Replacement
 ↓
Resolve
 ↓
Trigger
 ↓
StateCheck
```

---

# 2. ゲーム状態

GameStateはゲームの完全状態を保持する。

## 保持情報

* プレイヤー
* ゾーン
* カード
* イベントキュー
* トリガー
* 置換効果
* 継続効果
* 勝敗状態

---

# 3. ゾーン

## 基本ゾーン

* Deck
* Hand
* BattleZone
* ManaZone
* ShieldZone
* Graveyard
* Exile

---

## 原則

カードは必ず1つのゾーンのみに存在する。

禁止

```text
BattleZone
+
Graveyard
```

---

# 4. ターン進行

## Turn Structure

```text
Turn Start

↓

Untap

↓

Draw

↓

Main

↓

Attack

↓

End
```

---

## Turn Start

イベント発行

```text
TurnStart
```

---

## Untap

タップ状態解除

---

## Draw

通常ドロー

```text
Draw(1)
```

---

## Main

以下が可能

* 召喚
* 呪文
* チャージ

---

## Attack

攻撃宣言

---

## End

終了時誘発処理

---

# 5. 優先権

Priority Systemを採用する。

---

## 優先権取得

以下で発生

```text
Action Resolve後
```

```text
Trigger Resolve後
```

```text
Turn Start後
```

---

## 優先権放棄

全プレイヤー放棄で進行。

---

# 6. Event System (ETS)

全処理はイベント化する。

---

## Event構造

```cpp
struct GameEvent
{
    EventType Type;
    ObjectID Source;
    ObjectID Target;
};
```

---

## 主イベント

* Cast
* Summon
* Attack
* Block
* Draw
* Destroy
* Move
* Tap
* Untap

---

# 7. Replacement System

置換効果はイベント前に介入する。

---

## 順序

```text
Event発生

↓

Replacement

↓

Event実行
```

---

例

```text
Destroy

↓

Instead MoveToHand

↓

Destroy消滅
```

---

## 優先順位

最も新しい置換効果から適用。

---

# 8. Trigger System

イベント後に発生。

---

例

```text
Draw

↓

OnDraw Trigger
```

---

## 解決順

```text
Event

↓

Trigger Register

↓

Queue Push

↓

Resolve
```

---

# 9. Action Queue

全効果はQueueに積む。

---

FIFO方式

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

順に実行

---

# 10. Continuous Effects

常在効果。

---

例

```text
+2000 Power
```

```text
Blocker付与
```

---

特徴

永続実行しない。

必要時再計算する。

---

# 11. Continuous Effect Layers

適用順固定。

---

Layer 1

所有者変更

---

Layer 2

コントローラー変更

---

Layer 3

文明変更

---

Layer 4

種族変更

---

Layer 5

能力追加削除

---

Layer 6

パワー修正

---

Layer 7

コスト修正

---

# 12. State Based Actions (SBA)

優先権取得前に必ず実行。

---

## SBA実行タイミング

```text
Action Resolve後
```

```text
Trigger Resolve後
```

```text
Priority取得前
```

---

## SBA例

### パワー0以下

```text
Power <= 0

↓

Graveyard
```

---

### 山札0枚

```text
Deck == 0

↓

Lose
```

---

### 同名唯一性

ルールに従い整理。

---

# 13. 勝敗判定

---

## 敗北

### 山札切れ

```text
Draw不可
```

---

### 特殊敗北

カード効果

---

## 勝利

### 相手敗北

---

### 特殊勝利

カード効果

---

# 14. Rule Engine 実行順

DMVM2の最重要仕様。

全処理は以下順で実行。

```text
Action

↓

Event Generate

↓

Replacement

↓

Action Resolve

↓

Trigger Register

↓

Trigger Resolve

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

# 15. OCS適用方針

カードはComponentの集合として構築する。

---

例

```text
Card

├ CostComponent
├ CivilizationComponent
├ CreatureComponent
├ AbilityComponent
├ TriggerComponent
└ MetadataComponent
```

---

カード固有クラスは禁止。

---

# 16. 完全再現原則

紙の裁定を最優先とする。

仕様と裁定が衝突した場合

```text
公式裁定
＞
DMVM2仕様
```

を適用する。

---

本仕様書はDMVM2全仕様書の最上位ルールとして扱う。
