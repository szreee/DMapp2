03_DMVM2_CardSystem.md
DMVM2 Card System Specification v1.0
目的

本仕様書はDMVM2におけるカードデータモデルを定義する。

対象

CardDefinition
CardInstance
ObjectRegistry
OCS
Zone System
Metadata System
Civilization System
Race System
Cost System

全カードは本仕様書に従う。

1. 基本原則
1.1 カードはデータ

禁止

class BolshackDragon
{
};
class AquaSurfer
{
};

カード固有クラスは禁止。

許可

{
  "CardID":1,
  "Name":"Bolshack Dragon"
}
1.2 OCS採用

DMVM2は

Object Composition System

を採用する。

カードはComponentの集合として構築する。

2. Card Definition

カードの静的情報。

定義
struct CardDefinition
{
    CardID ID;

    std::string Name;

    std::vector<CivilizationType>
        Civilizations;

    std::vector<RaceType>
        Races;

    std::vector<std::string>
        AbilityIDs;

    CostDefinition Cost;

    CardType Type;
};
特徴

ゲーム中変更されない。

共有可能。

例

{
  "CardID":1,
  "Name":"ボルシャック・ドラゴン",
  "Cost":6
}
3. Card Instance

ゲーム中の実体。

定義
struct CardInstance
{
    ObjectID ID;

    CardDefinition* Definition;

    PlayerID Owner;

    PlayerID Controller;

    ZoneType CurrentZone;

    CardState State;

    std::vector<
        AbilityInstance>
        Abilities;
};
特徴

ゲーム中変化する。

例

タップ状態

パワー修正

コントロール変更
4. Card State

動的状態。

struct CardState
{
    bool Tapped;

    bool FaceDown;

    bool SummoningSick;

    int Damage;

    int PowerModifier;
};
原則

状態のみ保持。

能力は保持しない。

5. Object Registry

全オブジェクト管理。

定義
class ObjectRegistry
{
public:

    CardInstance* Find(
        ObjectID ID);

    void Register(
        CardInstance* Card);
};
原則

カード検索は必ずRegistry経由。

禁止

Player.Hand[3]

許可

Registry.Find(ID)
6. Component System

カードを構成する要素。

CostComponent
ManaCost
CivilizationComponent
Fire
Water
Nature
Light
Darkness
RaceComponent
Human
Dragon
CyberLord
AbilityComponent
AbilityID
MetadataComponent
Name
Flavor
Artist
7. Card Type
enum class CardType
{
    Creature,

    Spell,

    CrossGear,

    Fortress,

    PsychoCreature,

    Gacharange,

    D2Field,

    DGField
};
複合タイプ対応
Creature + Spell

許可。

8. Civilization System
enum class CivilizationType
{
    Light,

    Water,

    Darkness,

    Fire,

    Nature,

    Zero
};
多文明対応
Fire
Water

同時保持可能。

9. Race System
enum class RaceType
{
    Human,

    Dragon,

    CyberLord,

    AngelCommand,

    DemonCommand
};
複数種族対応
アーマード・ドラゴン

＋

アース・ドラゴン

可能。

10. Cost System
struct CostDefinition
{
    int ManaCost;
};

将来的拡張

AlternativeCost

ReducedCost

AdditionalCost
11. Zone System

カードは常に1つのゾーンに存在する。

ZoneType
enum class ZoneType
{
    Deck,

    Hand,

    BattleZone,

    ManaZone,

    ShieldZone,

    Graveyard,

    Exile
};
禁止
BattleZone

+

Graveyard
12. Zone Transition

移動はイベント化する。

Move

例

BattleZone

↓

Graveyard

発行イベント

BeforeMove

Move

AfterMove
13. Ownership
Owner

元の持ち主。

変化しない。

Controller

現在の支配者。

変化可能。

例

精神操作
14. Metadata System

ゲーム処理に不要な情報。

struct Metadata
{
    std::string Name;

    std::string FlavorText;

    std::string Artist;

    std::string SetCode;
};
原則

ルール処理で使用しない。

15. Card Database

全カード定義を管理。

class CardDatabase
{
public:

    const CardDefinition*
    Find(CardID ID);
};
特徴

読み取り専用。

16. Serialization

保存可能であること。

JSON
{
  "ID":1001,
  "Owner":1,
  "Zone":"BattleZone"
}
Save対象
CardInstance
Player
GameState
17. 実行原則

カードは状態を持つ。

処理を持たない。

禁止

Card->Attack();

許可

AttackAction
18. DMVM2標準データフロー
CardDefinition

↓

CardInstance

↓

ObjectRegistry

↓

GameState

↓

RuleEngine
19. 完全再現原則

カードの挙動は

AbilitySystem

および

CoreRules

に従う。

CoreRules

↓

AbilitySystem

↓

CardSystem

本仕様書により

CardDefinition
CardInstance
ObjectRegistry
Zone
Component

の構造を凍結する。