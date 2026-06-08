06_DMVM2_ProjectStructure.md

目的：

フォルダ構成凍結
モジュール構成凍結
依存方向凍結
命名規則凍結
最終ディレクトリ構成
DMVM2/

├ Docs/

├ Source/

│  ├ Core/
│  │
│  ├ RuleEngine/
│  │
│  ├ Action/
│  │
│  ├ Event/
│  │
│  ├ Ability/
│  │
│  ├ Card/
│  │
│  ├ Database/
│  │
│  ├ Serialization/
│  │
│  ├ Networking/
│  │
│  └ Utilities/

├ Data/

│  ├ Cards/
│  ├ Abilities/
│  ├ Expansions/

├ Tests/

└ Tools/
依存方向
Core
 ↑

Card
 ↑

Ability
 ↑

Action/Event
 ↑

RuleEngine

禁止

RuleEngine ← Card
モジュール責務
Core
GameState
PlayerState
Enums
IDs
Card
CardDefinition
CardInstance
ObjectRegistry
ZoneManager
Ability
AbilityManager
AbilityResolver
ConditionSystem
TargetSystem
EffectSystem
Event
EventBus
EventQueue
GameEvent
Action
IAction
ActionQueue
RuleEngine
TurnSystem
PrioritySystem
ReplacementSystem
TriggerSystem
SBA
VictorySystem