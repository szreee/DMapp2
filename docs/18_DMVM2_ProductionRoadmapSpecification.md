18_DMVM2_ProductionRoadmapSpecification.md
DMVM2 Production Roadmap Specification v1.0
目的

本仕様書はDMVM2の開発工程を定義する。

対象

開発順序
実装順序
テスト順序
データ投入順序
リリース順序

DMVM2の全実装は本仕様書に従う。

1. 基本方針

DMVM2は

仕様駆動開発
(Specification Driven Development)

を採用する。

実装順
仕様

↓

API固定

↓

コード生成

↓

テスト

↓

カード投入

↓

検証

↓

リリース
禁止
実装しながら仕様変更
カード実装のためのエンジン改造
許可
仕様変更

↓

仕様更新

↓

再生成
2. 開発フェーズ
Phase 0

Architecture Freeze

対象

01〜18

成果物

DMVM2_MASTER.zip

状態

アーキテクチャ凍結
3. Phase 1

Engine Skeleton

実装対象

Core
Card
Ability
RuleSystems
Actions

成果物

全クラス生成

状態

コンパイル成功
4. Phase 2

Core Engine

実装対象

GameState
RuleEngine
ObjectRegistry
EventBus
ActionQueue

目標

ゲーム開始可能
5. Phase 3

Ability Engine

実装対象

AbilityManager
AbilityResolver
EffectExecutor
ConditionEvaluator
TargetResolver

目標

能力実行可能
6. Phase 4

Rule Systems

実装対象

TriggerSystem
ReplacementSystem
SBAEngine
PrioritySystem
VictorySystem

目標

裁定処理可能
7. Phase 5

Database & DSL

実装対象

CardDatabase
AbilityDatabase
DSL Loader
Schema Validator

目標

JSONのみでカード追加可能
8. Phase 6

Test Infrastructure

実装対象

Unit Test
Integration Test
Replay Test
Official Ruling Test

目標

自動検証
9. Phase 7

Minimum Playable Game

対象

初期カードセット

推奨

基本セットのみ

カード数

100〜300

目標

対戦成立
10. Phase 8

Official Compatibility

対象

公式裁定

目標

再現率95%

以上

テスト

裁定ケース

大量投入

11. Phase 9

Network Layer

対象

サーバー

実装

Lobby
Matchmaking
Reconnect
Replay

目標

オンライン対戦
12. Phase 10

Client

対象

UE5

実装

UI
Board
Animation
Deck Builder

目標

一般プレイ可能
13. Phase 11

Full Card Import

対象

全カード

投入方法

JSON

のみ

禁止

カード固有コード

目標

全カード実装
14. Phase 12

Replay Validation

対象

全リプレイ

実行

Replay

↓

Deterministic Simulation

↓

Hash Compare

目標

完全一致
15. Phase 13

Closed Alpha

対象

開発者

目標

重大バグ除去
16. Phase 14

Closed Beta

対象

限定ユーザー

目標

裁定差異発見
17. Phase 15

Open Beta

対象

一般公開

目標

安定性確認
18. Phase 16

Release Candidate

条件

全テスト成功
裁定再現率99%以上
Replay一致
19. Phase 17

Official Release

成果物

Client
Dedicated Server
Card Database

状態

正式版
20. DMVM2完成条件

以下を全て満たした時

DMVM2完成と定義する。

Engine
100%
Test
100%
Replay
100%
Official Ruling
99%以上
Card DSL
100%
Network
100%
Full Card Import
100%
21. 最終開発原則

DMVM2は

仕様

↓

実装

↓

テスト

↓

データ投入

の順で開発する。

逆順は禁止。