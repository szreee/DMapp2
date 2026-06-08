14_DMVM2_CardDSLSpecification.md
DMVM2 Card DSL Specification v1.0
目的

本仕様書はDMVM2におけるカードDSLを定義する。

対象

Card JSON
Ability JSON
DSL Validation
Expansion Data
Data Compiler

全カードデータは本仕様書に従う。

1. 基本原則
1.1 Data Driven

カード追加でコード変更は禁止。

禁止

class BolshackDragon
{
};

許可

{
  "CardID":"DM-01-001"
}
1.2 DSL First

カードはDSLで定義する。

Card JSON

↓

Validate

↓

Compile

↓

Database
1.3 Engine Independence

RuleEngineはカード名を知らない。

禁止

if(CardName=="Bolshack")

許可

AbilityID
2. Card JSON Structure
基本構造
{
  "CardID":"",
  "Name":"",
  "Type":"",
  "Civilizations":[],
  "Races":[],
  "Cost":0,
  "Power":0,
  "Abilities":[]
}
3. Required Fields

必須

CardID
Name
Type
Cost
Abilities

不足時

Validation Error
4. CardID
原則

全カード一意。

例

{
  "CardID":"DM01-001"
}

重複禁止。

5. Name

例

{
  "Name":"ボルシャック・ドラゴン"
}

ゲームロジック禁止。

表示専用。

6. Card Type
{
  "Type":"Creature"
}

許可

Creature
Spell
CrossGear
Fortress
PsychicCreature
D2Field
DGField

複合

{
  "Type":[
    "Creature",
    "Spell"
  ]
}
7. Civilization
単文明
{
  "Civilizations":["Fire"]
}
多文明
{
  "Civilizations":
  [
    "Fire",
    "Water"
  ]
}
8. Race
{
  "Races":
  [
    "ArmoredDragon"
  ]
}

複数種族

{
  "Races":
  [
    "ArmoredDragon",
    "EarthDragon"
  ]
}
9. Cost
{
  "Cost":6
}

将来拡張

{
  "AdditionalCost":{},
  "AlternativeCost":{}
}
10. Power

クリーチャーのみ。

{
  "Power":6000
}

呪文

{
  "Power":0
}
11. Ability Reference

カードはAbilityIDのみ保持。

{
  "Abilities":
  [
    "BA001",
    "BA002"
  ]
}

Ability実体は禁止。

禁止

{
  "Abilities":
  [
    {
      "Trigger":"Attack"
    }
  ]
}
12. Ability DSL
基本構造
{
  "AbilityID":"BA001",

  "Type":"Triggered",

  "Trigger":"Attack",

  "Condition":"Always",

  "Target":"OpponentCreature",

  "Effect":"Destroy"
}
13. Ability Types
Triggered
Activated
Static
Continuous
Replacement
14. Trigger DSL

例

{
  "Trigger":"Attack"
}

許可

Attack
Block
Destroy
Move
Draw
Cast
Summon
TurnStart
TurnEnd
15. Condition DSL

例

{
  "Condition":"Always"
}

許可

Always
Self
Opponent
PowerGreater
Attacking
Blocked
16. Target DSL

例

{
  "Target":"Self"
}

許可

Self
Opponent
Player
Creature
AnyCard
None
17. Effect DSL

例

{
  "Effect":"Destroy"
}

標準

Draw
Destroy
Move
ReturnToHand
AddPower
AddAbility
SearchDeck
Shuffle
18. Composite Effects

複数効果。

{
  "Effects":
  [
    {
      "Effect":"Draw",
      "Value":1
    },
    {
      "Effect":"AddPower",
      "Value":2000
    }
  ]
}

順番維持。

19. Continuous Effect DSL
{
  "Type":"Continuous",

  "Target":"Self",

  "Effect":"AddPower",

  "Value":2000
}

Layer System使用。

20. Replacement DSL
{
  "Type":"Replacement",

  "Trigger":"Destroy",

  "Effect":"ReturnToHand"
}
21. Expansion Structure
Data/

Cards/

Abilities/

Expansions/
22. Expansion Metadata
{
  "ExpansionID":"DM01",

  "Name":"基本セット"
}
23. Validation Rules
Card Validation

確認

CardID Unique

Ability Exists

Race Valid

Civilization Valid
Ability Validation

確認

Trigger Valid

Target Valid

Effect Valid
24. Compilation
JSON

↓

Parse

↓

Validate

↓

Compile

↓

Binary Database
25. Generated Databases

出力

Cards.db
Abilities.db
Expansion.db
26. Versioning

各DSLはSchemaVersion保持。

{
  "SchemaVersion":"1.0"
}
27. Backward Compatibility

Minor Version内保証。

28. Debug DSL

開発専用

{
  "Effect":"PrintMessage"
}

Releaseで無効。

29. 完全再現原則
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
CardDSL
30. 凍結対象

本仕様書により固定されるもの

Card JSON形式
Ability JSON形式
Trigger DSL
Effect DSL
Target DSL
Condition DSL
Validation Rules
Expansion形式
Data Compiler入力形式