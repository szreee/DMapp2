05_DMVM2_DatabaseAndDSL.md
DMVM2 Database & DSL Specification v1.0
目的

本仕様書はDMVM2におけるデータ管理とDSL(Data Specification Language)を定義する。

対象

Card Database
Ability Database
JSON Schema
DSL
Save / Load
Expansion System
Data Driven Design

全カード追加は本仕様書に従う。

1. 基本原則
1.1 Data Driven

DMVM2は完全データ駆動を採用する。

禁止

class BolshackDragon
{
};
if(CardName=="Bolshack")
{
}

許可

{
  "CardID":1001
}
1.2 コード修正禁止

新カード追加時

Engine変更

禁止

必要なのは

Card JSON
Ability JSON

のみ。

2. Database構成

DMVM2は2種類のDBを持つ。

Card Database

カード定義

Ability Database

能力定義

3. Card Database
API
class CardDatabase
{
public:

    const CardDefinition*
    Find(CardID ID);
};
保持内容
{
  "CardID":1001,
  "Name":"ボルシャック・ドラゴン",
  "Cost":6
}
特徴

読み取り専用

4. Ability Database
API
class AbilityDatabase
{
public:

    const AbilityDefinition*
    Find(std::string ID);
};
例
{
  "AbilityID":"BOLSHACK_ATTACK"
}
5. Card JSON Schema

カード定義形式。

基本構造
{
  "CardID":1001,

  "Name":"ボルシャック・ドラゴン",

  "Type":["Creature"],

  "Civilizations":["Fire"],

  "Races":["ArmoredDragon"],

  "Cost":6,

  "Power":6000,

  "AbilityIDs":
  [
      "BOLSHACK_ATTACK"
  ]
}
必須項目
CardID
Name
Type
Cost
AbilityIDs
6. Ability JSON Schema

能力定義形式。

基本構造
{
  "AbilityID":"BOLSHACK_ATTACK",

  "Type":"Triggered",

  "Trigger":"Attack",

  "Condition":"Always",

  "Target":"OpponentCreature",

  "Effect":"Destroy"
}
特徴

コード不要

7. DSL

DMVM2能力記述言語。

目的

AbilityDefinition

を簡潔に表現する。

8. Trigger DSL

例

{
  "Trigger":"Attack"
}
{
  "Trigger":"EnterBattleZone"
}
{
  "Trigger":"Destroyed"
}
9. Condition DSL
{
  "Condition":"Always"
}
{
  "Condition":"Power>=5000"
}
{
  "Condition":"Controller=Self"
}
10. Target DSL
{
  "Target":"Self"
}
{
  "Target":"OpponentCreature"
}
{
  "Target":"AnyPlayer"
}
11. Effect DSL
Draw
{
  "Effect":"Draw",
  "Value":1
}
Destroy
{
  "Effect":"Destroy"
}
ReturnToHand
{
  "Effect":"ReturnToHand"
}
AddPower
{
  "Effect":"AddPower",
  "Value":2000
}
SearchDeck
{
  "Effect":"SearchDeck"
}
12. Composite Effect

複数効果対応。

{
  "Effects":
  [
    {
      "Effect":"Draw",
      "Value":1
    },
    {
      "Effect":"Destroy"
    }
  ]
}

順番維持。

13. Cost DSL
{
  "Cost":
  {
      "Mana":2
  }
}
{
  "Cost":
  {
      "TapSelf":true
  }
}
{
  "Cost":
  {
      "Discard":1
  }
}
14. Replacement DSL
{
  "Replacement":
  {
      "Match":"Destroy",
      "Replace":"ReturnToHand"
  }
}

例

破壊されるかわりに
手札へ戻す
15. Continuous Effect DSL
{
  "Continuous":
  {
      "Target":"SelfCreature",

      "Effect":"AddPower",

      "Value":2000
  }
}

再計算前提。

16. Expansion Pack System

追加カード対応。

Pack構造
BaseSet

DM01

DM02

DM03
読み込み
Base

↓

Expansion

↓

Merge
17. Save System

保存対象

GameState
CardInstance
Player
Queue
18. Save Format

JSON

{
  "Turn":5,

  "ActivePlayer":1
}

将来

Binary

対応可能。

19. Versioning

各データにVersionを持つ。

{
  "Version":"1.0"
}
Migration
v1

↓

v2

↓

Converter
20. Validation

起動時に全JSON検証。

例

AbilityID重複
CardID重複
存在しないAbilityID

エラー。

21. Data Loading Flow
Card JSON

↓

Ability JSON

↓

Validation

↓

Database

↓

GameState

↓

RuleEngine
22. Repository Structure
DMVM2/

├ docs/

├ data/

│  ├ cards/
│  ├ abilities/
│  └ sets/

├ saves/

├ src/

└ tests/
23. 完全再現原則

実装はデータを実行するだけ。

CoreRules

↓

AbilitySystem

↓

CardSystem

↓

ActionEventSystem

↓

DatabaseDSL

仕様優先順位

公式裁定

＞

01_CoreRules

＞

02_AbilitySystem

＞

03_CardSystem

＞

04_ActionEventSystem

＞

05_DatabaseDSL

＞

実装