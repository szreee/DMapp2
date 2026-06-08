# 21_DMVM2_SourceGenerationStrategy.md

# DMVM2 Source Generation Strategy Specification v1.0

## 目的

本仕様書はDMVM2における実装生成戦略を定義する。

対象

* ChatGPT Free Plan
* LLM生成コード
* Source生成
* ファイル展開方式
* 大規模コード生成

本仕様書はDMVM2開発時の標準生成手順として採用する。

---

# 1. 背景

DMVM2は最終的に

* 20以上の仕様書
* 300前後のソースファイル
* 数万行規模コード

になる。

ChatGPT Free Planでは

* 出力長制限
* 会話長制限
* 添付制限

が存在する。

そのため

「1ファイルずつ生成」

方式は禁止する。

---

# 2. 標準生成フロー

DMVM2は以下の流れで生成する。

```text
仕様書群

↓

全ソース設計確定

↓

大型TXT生成

↓

展開スクリプト生成

↓

ローカル展開

↓

コンパイル

↓

テスト
```

---

# 3. ソース生成単位

生成単位は

ファイル

ではなく

モジュール

とする。

---

禁止

```text
GameState.h

GameState.cpp

PlayerState.h

PlayerState.cpp
```

個別生成

---

許可

```text
DMVM2_BATCH_01_CORE.txt
```

にまとめる

---

# 4. 大型TXT方式

各TXTは

複数ファイルを内包する。

例

```text
=== FILE:
Source/Core/GameState.h
===

...

=== FILE:
Source/Core/GameState.cpp
===

...

=== FILE:
Source/Core/PlayerState.h
===

...
```

---

# 5. TXT分割数

DMVM2標準では

10個前後

に分割する。

---

## 推奨構成

### BATCH_01

Core

---

### BATCH_02

Card

---

### BATCH_03

Ability

---

### BATCH_04

Action

---

### BATCH_05

Event

---

### BATCH_06

RuleEngine

---

### BATCH_07

Database

---

### BATCH_08

Serialization

---

### BATCH_09

Client

---

### BATCH_10

Tests

---

# 6. 展開スクリプト

TXTから自動展開する。

---

入力

```text
DMVM2_BATCH_01_CORE.txt
```

---

出力

```text
GameState.h

GameState.cpp

PlayerState.h

PlayerState.cpp
```

---

# 7. 展開方式

TXT内部形式

```text
=== FILE:
Source/Core/GameState.h
===

内容

=== END FILE ===
```

---

展開スクリプトが

自動で

* フォルダ生成
* ファイル生成

を行う。

---

# 8. ChatGPT運用

ChatGPTには

毎回

DMVM2_MASTER.zip

を渡す。

---

その上で

```text
DMVM2_BATCH_01_CORE
を生成
```

のように依頼する。

---

# 9. コピペ回数

従来

```text
300ファイル
↓
300回コピペ
```

---

新方式

```text
10バッチ
↓
10回保存
```

---

削減率

約97%

---

# 10. 開発原則

LLMは

完成形アーキテクチャ

を前提として生成する。

---

原則

```text
仕様書

↓

全体設計

↓

コード生成
```

---

禁止

```text
とりあえず実装

↓

後で設計
```

---

# 11. 凍結事項

DMVM2では

以下を固定する。

* 仕様書群
* SourceTree
* API Contract
* FullClassCatalog
* Batch構成
* 展開形式

---

# 12. 最終成果物

最終成果物は

```text
DMVM2_MASTER.zip

├ Docs
├ Source
├ Tests
├ Tools
├ Data
├ Scripts
```

である。

---

# 13. DMVM2標準実装モデル

```text
20仕様書

↓

FullClassCatalog

↓

10 Source Batches

↓

Expand Script

↓

Source Tree

↓

Build

↓

Test

↓

Release
```

本仕様書をDMVM2の正式なソース生成戦略として採用する。
