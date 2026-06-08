# 15_DMVM2_OfficialRulingCompatibilitySpecification.md

# DMVM2 Official Ruling Compatibility Specification v1.0

## 目的

本仕様書はDMVM2における公式裁定互換性を定義する。

対象

* Official Ruling Database
* Ruling Resolution
* Priority Conflicts
* Trigger Conflicts
* Replacement Conflicts
* Historical Rulings
* Official Test Cases

DMVM2は紙のデュエル・マスターズの挙動を可能な限り完全再現する。

---

# 1. 基本原則

## 1.1 裁定最優先

DMVM2における最上位ルールは公式裁定である。

優先順位

```text
Official Ruling

＞

Core Rules

＞

Ability System

＞

Implementation
```

---

## 1.2 実装依存禁止

禁止

```cpp
if(CardID=="DM01-001")
{
}
```

裁定はデータ化する。

---

## 1.3 再現性保証

同一局面では常に同一結果になること。

---

# 2. Official Ruling Database

## 構造

```cpp
struct RulingEntry
{
    RulingID ID;

    std::string Title;

    std::string Description;

    std::string Resolution;
};
```

---

## 管理対象

```text
OfficialRulings.db
```

---

# 3. Ruling Priority

## 最優先

```text
公式Q&A
```

---

## 次順位

```text
総合ルール
```

---

## 最下位

```text
DMVM2独自仕様
```

---

# 4. Trigger Conflict Resolution

複数誘発が同時発生した場合。

---

## 原則

```text
Active Player

↓

Non Active Player
```

---

同一プレイヤー内

```text
任意順
```

---

# 5. Replacement Conflict Resolution

複数置換適用時。

---

適用順

```text
最新生成

↓

古い生成
```

---

再帰適用禁止。

---

# 6. Continuous Effect Conflict

Layer Systemを使用。

適用順

```text
Layer1 Ownership

Layer2 Controller

Layer3 Civilization

Layer4 Race

Layer5 Ability

Layer6 Power

Layer7 Cost
```

---

# 7. Simultaneous Events

同時発生イベント。

---

例

```text
複数破壊
```

---

処理

```text
Single Event Group
```

として扱う。

---

# 8. State Based Actions

裁定に従い必ず実行。

---

順序

```text
Resolve

↓

Continuous

↓

SBA

↓

Trigger
```

---

# 9. Zone Transition Rulings

移動は必ずイベント。

---

発行

```text
BeforeMove

Move

AfterMove
```

---

省略禁止。

---

# 10. Ownership Rules

Ownerは不変。

---

Controllerのみ変更可能。

---

# 11. Hidden Information Rules

対象

```text
Deck
Hand
Shield
```

---

非公開情報は許可されたプレイヤーのみ参照可能。

---

# 12. Shuffle Rules

シャッフル後の順序保証禁止。

---

実装依存禁止。

---

# 13. Search Rules

探索後

```text
Shuffle Required
```

を必ず適用。

---

# 14. Infinite Loop Rules

無限ループ検出。

---

検出時

```text
Loop Resolver
```

へ移行。

---

# 15. Mandatory Effects

強制能力。

---

プレイヤーは拒否不可。

---

# 16. Optional Effects

任意能力。

---

選択要求を生成。

---

# 17. Target Legality

解決時再チェック。

---

不正対象

```text
Fizzles
```

扱い。

---

# 18. Historical Rulings

旧裁定保持。

---

構造

```cpp
struct HistoricalRuling
{
    Date EffectiveDate;

    Date EndDate;
};
```

---

# 19. Official Replay Tests

全裁定をReplay化。

---

構造

```text
Input

↓

Replay

↓

Expected State
```

---

# 20. Official Ruling Test Suite

テスト分類

```text
Trigger

Replacement

Continuous

Priority

Zone
```

---

# 21. Compatibility Level

定義

```text
Bronze

Silver

Gold

Platinum
```

---

Platinum

```text
公式裁定100%
```

---

# 22. Regression Protection

裁定修正後

全Replay再実行。

---

失敗禁止。

---

# 23. Future Proofing

新裁定追加時

```text
Database追加
```

のみ許可。

---

Engine修正は例外扱い。

---

# 24. Validation Pipeline

```text
Ruling Import

↓

Validation

↓

Replay Generation

↓

Regression Test

↓

Release
```

---

# 25. DMVM2 Compatibility Hierarchy

```text
Official Ruling

↓

Core Rules

↓

Ability System

↓

Card System

↓

Action/Event System

↓

Database DSL

↓

Implementation
```

---

# 26. 凍結対象

本仕様書により固定されるもの

* Official Ruling Database
* Conflict Resolution Rules
* Trigger Ordering
* Replacement Ordering
* Loop Handling
* Replay Validation
* Compatibility Levels
* Regression Strategy

---

DMVM2の最終目標は

```text
Paper Duel Masters

≒

DMVM2
```

を成立させることである。
