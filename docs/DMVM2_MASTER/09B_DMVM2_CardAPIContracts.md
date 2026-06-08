# 09B_DMVM2_CardAPIContracts.md

# DMVM2 Card API Contracts Specification v1.0

## 目的

本仕様書はDMVM2のカードシステムにおけるAPI契約を定義する。

対象:

* CardDefinition
* CardInstance
* ZoneManager
* CardDatabase

本仕様書で定義された

* Public API
* Private Members
* Ownership
* Lifetime
* Dependencies

はDMVM2 v1系において変更禁止とする。

---

# 1. 設計原則

## 1.1 CardDefinitionは不変

CardDefinitionはゲーム開始後変更禁止。

禁止

```cpp
CardDefinition.Power = 9999;
```

許可

```cpp
CardInstance.State.PowerModifier
```

---

## 1.2 CardInstanceは状態のみ保持

CardInstanceは処理を持たない。

禁止

```cpp
CardInstance::Attack();
CardInstance::Destroy();
```

許可

```cpp
AttackAction
DestroyAction
```

---

## 1.3 Databaseは読み取り専用

CardDatabaseは生成後変更禁止。

---

# 2. CardDefinition

## 責務

カードの静的定義保持。

---

## Ownership

所有しない。

AbilityDefinitionはAbilityDatabase参照。

---

## Public API

```cpp
class CardDefinition
{
public:

    CardID GetID() const;

    const std::string&
    GetName() const;

    CardType GetType() const;

    const CostDefinition&
    GetCost() const;

    const std::vector<
        CivilizationType>&
        GetCivilizations() const;

    const std::vector<
        RaceType>&
        GetRaces() const;

    const std::vector<
        AbilityID>&
        GetAbilities() const;
};
```

---

## Private Members

```cpp
CardID ID;

std::string Name;

CardType Type;

CostDefinition Cost;

std::vector<CivilizationType>
Civilizations;

std::vector<RaceType>
Races;

std::vector<AbilityID>
Abilities;

Metadata MetadataData;
```

---

## Lifetime

CardDatabaseロード時生成。

サーバー終了時破棄。

---

# 3. CardState

## 責務

ゲーム中の動的状態保持。

---

## Public API

```cpp
struct CardState
{
    bool Tapped;

    bool FaceDown;

    bool SummoningSick;

    int Damage;

    int PowerModifier;
};
```

---

## Lifetime

CardInstanceと同一。

---

# 4. CardInstance

## 責務

ゲーム中カード実体。

---

## Ownership

所有:

```cpp
CardState
```

非所有:

```cpp
CardDefinition
```

---

## Public API

```cpp
class CardInstance
{
public:

    ObjectID GetID() const;

    const CardDefinition&
    Definition() const;

    PlayerID Owner() const;

    PlayerID Controller() const;

    ZoneType Zone() const;

    CardState& State();

    const CardState&
    State() const;

    void SetController(
        PlayerID);

    void SetZone(
        ZoneType);
};
```

---

## Private Members

```cpp
ObjectID IDData;

const CardDefinition*
DefinitionData;

PlayerID OwnerData;

PlayerID ControllerData;

ZoneType CurrentZone;

CardState StateData;
```

---

## Lifetime

ObjectRegistry登録時生成。

ゲーム終了時破棄。

---

# 5. ZoneManager

## 責務

ゾーン移動管理。

---

## Ownership

所有しない。

ObjectRegistryを参照。

---

## Public API

```cpp
class ZoneManager
{
public:

    void MoveCard(
        ObjectID Card,
        ZoneType From,
        ZoneType To);

    bool Contains(
        ObjectID Card,
        ZoneType Zone) const;

    size_t Count(
        ZoneType Zone,
        PlayerID Player) const;

    std::vector<ObjectID>
    GetCards(
        ZoneType Zone,
        PlayerID Player) const;
};
```

---

## Private Members

```cpp
ObjectRegistry*
Registry;
```

---

## Lifetime

GameStateと同一。

---

# 6. Zone Transition Contract

## 必須イベント

移動時は必ず発行。

```text
BeforeMove
Move
AfterMove
```

---

## 禁止

```cpp
Card->SetZone(
    Graveyard);
```

---

## 許可

```cpp
MoveCardAction
```

---

# 7. Ownership Contract

## Owner

変更禁止。

---

## Controller

変更可能。

---

## API

```cpp
SetController(
    PlayerID);
```

のみ許可。

---

# 8. CardDatabase

## 責務

CardDefinition管理。

---

## Ownership

所有:

```cpp
CardDefinition
```

---

## Public API

```cpp
class CardDatabase
{
public:

    void Load(
        const std::string&
        Path);

    const CardDefinition*
    Find(
        CardID ID) const;

    bool Exists(
        CardID ID) const;

    size_t Count() const;
};
```

---

## Private Members

```cpp
unordered_map<
    CardID,
    CardDefinition>
Cards;
```

---

## Lifetime

サーバー起動時生成。

サーバー終了時破棄。

---

# 9. JSON Contract

## CardDefinition

```json
{
  "CardID":1,
  "Name":"ボルシャック・ドラゴン",
  "Type":"Creature",
  "Cost":6,
  "Civilizations":["Fire"],
  "Races":["ArmoredDragon"],
  "Abilities":[
    "Bolshack_Attack"
  ]
}
```

---

## 必須項目

```text
CardID
Name
Type
Cost
Abilities
```

---

# 10. Dependency Rules

固定。

```text
CardDatabase

↓

CardDefinition

↓

CardInstance

↓

ObjectRegistry

↓

ZoneManager
```

逆依存禁止。

---

# 11. Lifetime Rules

CardDefinition

```text
Server Lifetime
```

---

CardDatabase

```text
Server Lifetime
```

---

CardInstance

```text
Match Lifetime
```

---

ZoneManager

```text
Match Lifetime
```

---

# 12. API凍結原則

本仕様書に定義された

* Public API
* Ownership
* Lifetime
* Dependency

はDMVM2 v1.xにおいて変更禁止とする。

---

# 13. 実装優先順位

```text
CardDefinition

↓

CardDatabase

↓

CardInstance

↓

ObjectRegistry

↓

ZoneManager
```

の順に実装する。

これをCard Layerの正式実装順とする。
