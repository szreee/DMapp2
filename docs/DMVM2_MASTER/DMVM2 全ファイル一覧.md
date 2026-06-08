DMVM2 全ファイル一覧

概算

Source/DMVM2/

Core/
Card/
Ability/
Action/
Event/
Rule/
Database/
Serialization/
Network/
Client/
Utility/

Tests/

総数

約250～350ファイル
Batch 01

Core Foundation

生成プロンプト名

DMVM2_BATCH_01_CORE_FOUNDATION

ファイル

Core/GameState.h
Core/GameState.cpp

Core/PlayerState.h
Core/PlayerState.cpp

Core/Types.h
Core/Types.cpp

Core/Enums.h
Core/Enums.cpp

Core/GameContext.h
Core/GameContext.cpp
Batch 02

Object Registry

DMVM2_BATCH_02_REGISTRY
Core/ObjectRegistry.h
Core/ObjectRegistry.cpp

Core/ObjectFactory.h
Core/ObjectFactory.cpp
Batch 03

Card Model

DMVM2_BATCH_03_CARD_MODEL
Card/CardDefinition.h
Card/CardDefinition.cpp

Card/CardInstance.h
Card/CardInstance.cpp

Card/CardState.h
Card/CardState.cpp

Card/CardTypes.h
Card/CardTypes.cpp
Batch 04

Zone System

DMVM2_BATCH_04_ZONE_SYSTEM
Card/ZoneManager.h
Card/ZoneManager.cpp

Card/Zone.h
Card/Zone.cpp

Card/ZoneTransition.h
Card/ZoneTransition.cpp
Batch 05

Ability Core

DMVM2_BATCH_05_ABILITY_CORE
Ability/AbilityDefinition.h
Ability/AbilityDefinition.cpp

Ability/AbilityInstance.h
Ability/AbilityInstance.cpp

Ability/AbilityManager.h
Ability/AbilityManager.cpp

Ability/AbilityResolver.h
Ability/AbilityResolver.cpp
Batch 06

Condition System

DMVM2_BATCH_06_CONDITION_SYSTEM
Ability/ConditionEvaluator.h
Ability/ConditionEvaluator.cpp

Ability/ConditionTypes.h
Ability/ConditionTypes.cpp
Batch 07

Target System

DMVM2_BATCH_07_TARGET_SYSTEM
Ability/TargetResolver.h
Ability/TargetResolver.cpp

Ability/TargetSelector.h
Ability/TargetSelector.cpp
Batch 08

Effect System

DMVM2_BATCH_08_EFFECT_SYSTEM
Ability/EffectExecutor.h
Ability/EffectExecutor.cpp

Ability/EffectDefinition.h
Ability/EffectDefinition.cpp

Ability/EffectTypes.h
Ability/EffectTypes.cpp
Batch 09

Action Framework

DMVM2_BATCH_09_ACTION_FRAMEWORK
Action/IAction.h

Action/ActionQueue.h
Action/ActionQueue.cpp

Action/ActionFactory.h
Action/ActionFactory.cpp
Batch 10

Core Actions

DMVM2_BATCH_10_CORE_ACTIONS
DrawCardAction
MoveCardAction
DestroyAction
SummonAction
AttackAction
TapAction
UntapAction
CastSpellAction
Batch 11

Event Framework

DMVM2_BATCH_11_EVENT_FRAMEWORK
Event/GameEvent.h
Event/GameEvent.cpp

Event/EventQueue.h
Event/EventQueue.cpp

Event/EventBus.h
Event/EventBus.cpp

Event/EventDispatcher.h
Event/EventDispatcher.cpp

Event/EventListener.h
Batch 12

Rule Engine

DMVM2_BATCH_12_RULE_ENGINE
Rule/RuleEngine.h
Rule/RuleEngine.cpp
Batch 13

Trigger System

DMVM2_BATCH_13_TRIGGER_SYSTEM
Rule/TriggerSystem.h
Rule/TriggerSystem.cpp
Batch 14

Replacement System

DMVM2_BATCH_14_REPLACEMENT_SYSTEM
Rule/ReplacementSystem.h
Rule/ReplacementSystem.cpp
Batch 15

SBA System

DMVM2_BATCH_15_SBA_SYSTEM
Rule/SBAEngine.h
Rule/SBAEngine.cpp
Batch 16

Priority System

DMVM2_BATCH_16_PRIORITY_SYSTEM
Rule/PrioritySystem.h
Rule/PrioritySystem.cpp
Batch 17

Victory System

DMVM2_BATCH_17_VICTORY_SYSTEM
Rule/VictorySystem.h
Rule/VictorySystem.cpp
Batch 18

Databases

DMVM2_BATCH_18_DATABASES
Database/CardDatabase.h
Database/CardDatabase.cpp

Database/AbilityDatabase.h
Database/AbilityDatabase.cpp

Database/ExpansionDatabase.h
Database/ExpansionDatabase.cpp
Batch 19

Serialization

DMVM2_BATCH_19_SERIALIZATION
SaveGameSerializer
LoadGameSerializer
JsonConverter
Batch 20

Network

DMVM2_BATCH_20_NETWORK
GameServer
MatchServer
SessionManager
ClientConnection
Packet
PacketSerializer
Batch 21

Client

DMVM2_BATCH_21_CLIENT
ClientGameState
ReplaySystem
UIBridge
InputManager
Batch 22

Utilities

DMVM2_BATCH_22_UTILITIES
Logger
Random
FileSystem
Timer
Batch 23

Tests

DMVM2_BATCH_23_TESTS
UnitTests
IntegrationTests
ReplayTests
OfficialRulingTests