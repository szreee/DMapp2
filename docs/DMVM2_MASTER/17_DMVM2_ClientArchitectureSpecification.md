# 17_DMVM2_ClientArchitectureSpecification.md

# DMVM2 Client Architecture Specification v1.0

## 目的

本仕様書はDMVM2クライアントアーキテクチャを定義する。

対象

* UE5 Client
* UI System
* Input System
* Presentation Layer
* Scene Management
* Animation System
* Audio System
* Client Networking
* Replay Viewer
* Spectator Viewer

DMVM2クライアントは本仕様書に従う。

---

# 1. 基本原則

## 1.1 Thin Client

クライアントはゲームルールを持たない。

禁止

```cpp
ResolveTrigger();
```

```cpp
ApplyReplacement();
```

```cpp
CheckVictory();
```

許可

```cpp
DisplayState();
```

```cpp
SendActionRequest();
```

---

## 1.2 Server Authoritative

サーバーが唯一の真実である。

```text
Client

↓

Request

↓

Server

↓

State Update
```

---

## 1.3 Presentation Only

クライアントの責務

* 表示
* 入力
* 演出
* 音声

のみ。

---

# 2. Client Layer Structure

```text
Presentation

↓

ViewModel

↓

Network Layer

↓

Server
```

---

# 3. Module Structure

```text
DMVM2Client

├ UI
├ Network
├ Replay
├ Audio
├ Effects
├ Input
├ Scene
└ Presentation
```

---

# 4. Scene System

## Scene Types

```cpp
enum class SceneType
{
    Boot,

    Login,

    MainMenu,

    DeckBuilder,

    Matchmaking,

    Game,

    Replay,

    Settings
};
```

---

# 5. Game Scene

責務

* Battle UI
* Animation
* State Visualization

ルール処理禁止。

---

# 6. Input System

## Input Manager

```cpp
class InputManager
{
};
```

---

責務

* マウス
* キーボード
* Gamepad

統一管理。

---

# 7. Action Request Flow

```text
Click

↓

InputManager

↓

ActionRequest

↓

NetworkClient

↓

Server
```

---

# 8. Network Client

## 責務

* 接続
* 認証
* Action送信
* State受信

---

## API

```cpp
class NetworkClient
{
public:

    void Connect();

    void Send();

    void Disconnect();
};
```

---

# 9. Client State Cache

保持可能。

---

目的

表示高速化。

---

禁止

```text
Rule Resolution
```

---

# 10. View Model Layer

UIとStateを分離。

---

```text
GameState

↓

ViewModel

↓

Widget
```

---

# 11. Widget Architecture

全画面はWidgetで構築。

---

例

```text
MainMenuWidget

GameWidget

DeckBuilderWidget

ReplayWidget
```

---

# 12. Battle UI

表示対象

```text
Hand

BattleZone

ManaZone

ShieldZone

Graveyard
```

---

# 13. Card View

## CardViewModel

```cpp
class CardViewModel
{
};
```

---

保持

* Name
* Cost
* Power
* Race
* Civilization

---

# 14. Card Actor

3D表示専用。

---

禁止

```cpp
CardActor::ResolveAbility();
```

---

# 15. Animation System

演出専用。

---

例

```text
Draw

Attack

Destroy

Summon
```

---

# 16. Effect System

視覚効果。

---

例

```text
Explosion

Glow

Beam

Aura
```

---

# 17. Audio System

管理

```cpp
AudioManager
```

---

対象

* BGM
* Voice
* SFX

---

# 18. Replay Viewer

Replay表示専用。

---

入力

```text
Replay File
```

---

出力

```text
Visualized Match
```

---

# 19. Spectator Viewer

観戦モード。

---

閲覧制限

```text
Hand Hidden

Deck Hidden
```

---

# 20. Deck Builder

責務

* デッキ編集
* 検索
* フィルタ

---

ルール処理禁止。

---

# 21. Asset System

管理対象

```text
Card Art

Animation

Audio

UI
```

---

# 22. Localization

対応

```text
Japanese

English
```

---

拡張可能。

---

# 23. Settings System

保存

```text
Volume

Resolution

Language

Graphics
```

---

# 24. Error Handling

通信失敗

↓

Reconnect UI

---

同期失敗

↓

State Resync

---

# 25. Performance Target

目標

```text
60 FPS
```

---

通信量

```text
Delta Sync Only
```

---

# 26. Client Execution Flow

```text
User Input

↓

Action Request

↓

Server

↓

State Update

↓

ViewModel Update

↓

Animation

↓

Render
```

---

# 27. UE5 Structure

```text
Source/DMVM2Client

├ Core
├ UI
├ Network
├ Audio
├ Replay
├ Spectator
├ Effects
└ Scene
```

---

# 28. Replay Consistency

ReplayはAction列から再生。

---

State差異禁止。

---

# 29. Future Extensions

追加可能

* Mobile
* Console
* VR Spectator

---

RuleEngine変更禁止。

---

# 30. Architecture Hierarchy

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

↓

ClientArchitecture
```

---

# 31. 凍結対象

本仕様書により固定されるもの

* UI Architecture
* Input Architecture
* Network Client
* ViewModel Layer
* Replay Viewer
* Spectator Viewer
* Scene System
* Animation System
* Audio System

---

DMVM2 Clientは

```text
Presentation

≠

Game Logic
```

を絶対原則とする。
