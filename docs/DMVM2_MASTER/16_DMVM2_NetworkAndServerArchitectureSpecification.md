# 16_DMVM2_NetworkAndServerArchitectureSpecification.md

# DMVM2 Network & Server Architecture Specification v1.0

## 目的

本仕様書はDMVM2における通信基盤およびサーバーアーキテクチャを定義する。

対象

* Dedicated Server
* Match Server
* Game Session
* Client Protocol
* Replay Synchronization
* Authentication
* Persistence
* Spectator
* Ranking

DMVM2のオンライン対戦機能は本仕様書に従う。

---

# 1. 基本原則

## 1.1 Server Authoritative

ゲーム状態の決定権は常にサーバーが持つ。

禁止

```text
Client Damage Resolve
```

```text
Client Trigger Resolve
```

許可

```text
Client Request

↓

Server Validation

↓

Server Execute
```

---

## 1.2 Client Prediction禁止

DMVM2はTCGである。

FPSのような予測処理は禁止。

---

## 1.3 Single Source Of Truth

唯一の真実はServer GameState。

---

# 2. システム構成

```text
Client

↓

Gateway

↓

Matchmaker

↓

Game Server

↓

Database
```

---

# 3. Dedicated Game Server

1試合につき1 GameSession。

---

```cpp
class GameServer
{
};
```

---

責務

* RuleEngine保持
* GameState保持
* Replay生成
* Client同期

---

# 4. Game Session

## 定義

```cpp
class GameSession
{
};
```

---

保持

* SessionID
* Players
* GameState
* Replay
* StartTime

---

# 5. Matchmaking

責務

* ランク戦
* フリー対戦
* ルーム戦

---

結果

```text
PlayerA

+

PlayerB

↓

GameSession
```

---

# 6. Authentication

認証は試合開始前のみ。

---

保持

```cpp
UserID
```

```cpp
SessionToken
```

---

# 7. Client Connection

接続方式

```text
TCP
```

または

```text
WebSocket
```

---

UDP使用禁止。

---

# 8. Message Protocol

全通信はMessage化。

---

## Client → Server

```json
{
  "Type":"ActionRequest"
}
```

---

## Server → Client

```json
{
  "Type":"GameEvent"
}
```

---

# 9. Action Request

クライアントはAction要求のみ送信。

---

例

```json
{
  "Action":"Summon",
  "Card":123
}
```

---

# 10. Validation

サーバー側で必ず検証。

---

```text
Receive

↓

Validate

↓

Execute
```

---

失敗

```text
Reject
```

---

# 11. GameState Synchronization

同期方式

```text
Delta Sync
```

---

全State送信禁止。

---

# 12. Snapshot System

定期スナップショット保存。

---

構造

```cpp
GameSnapshot
```

---

用途

* 再接続
* リプレイ
* デバッグ

---

# 13. Replay System

ReplayはAction列。

---

保存

```text
ActionLog
```

---

禁止

```text
Frame Recording
```

---

# 14. Replay Reconstruction

再生時

```text
Initial State

↓

Action Replay

↓

Final State
```

---

一致必須。

---

# 15. Spectator System

観戦機能。

---

閲覧可能

* BattleZone
* ManaZone
* Graveyard

---

閲覧禁止

* Hand
* Deck

---

# 16. Disconnect Handling

切断時

```text
Grace Timer
```

開始。

---

再接続成功

```text
Resume
```

---

失敗

```text
Lose
```

---

# 17. Reconnect Flow

```text
Client Connect

↓

Auth

↓

Snapshot Send

↓

Resume
```

---

# 18. Persistence

保存対象

* Account
* Deck
* Ranking
* Replay

---

保存しない

* Active Session

---

# 19. Ranking System

保持

```cpp
Rating
```

---

更新

試合終了時のみ。

---

# 20. Anti Cheat

クライアントは信用しない。

---

禁止

```text
Client Resolve
```

```text
Client Draw
```

```text
Client Trigger
```

---

# 21. Logging

保存

```text
Action
```

```text
Event
```

```text
Ability
```

```text
Replay
```

---

# 22. Error Recovery

例外発生時

```text
Snapshot Restore
```

---

# 23. Scalability

目標

```text
1 Session

=

1 RuleEngine
```

---

Session間共有禁止。

---

# 24. Deployment

構成

```text
Gateway

Matchmaker

GameServer

Database
```

分離可能。

---

# 25. Version Compatibility

ClientVersion

ServerVersion

一致必須。

---

# 26. Network Execution Model

```text
Player Input

↓

Action Request

↓

Server Validation

↓

RuleEngine

↓

GameState Update

↓

Event Broadcast

↓

Client Update
```

---

# 27. Replay Verification

試合終了後

```text
Replay

↓

Rebuild

↓

State Compare
```

---

一致必須。

---

# 28. DMVM2 Online Hierarchy

```text
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

OfficialRuling

↓

NetworkArchitecture
```

---

# 29. 凍結対象

本仕様書により固定されるもの

* GameServer
* GameSession
* Matchmaker
* ReplaySystem
* SnapshotSystem
* ClientProtocol
* SynchronizationModel
* AuthenticationFlow
* SpectatorSystem

---

DMVM2のオンライン対戦は

```text
Server Authoritative

+

Deterministic Replay

+

Action Based Synchronization
```

を唯一の実装方針とする。
