07_DMVM2_ClassMap.md

目的：

全クラス一覧を凍結する
Core
GameState
PlayerState
TurnState
MatchState
Card
CardDefinition
CardInstance
CardDatabase

ObjectRegistry

ZoneManager
Ability
AbilityDefinition

AbilityInstance

AbilityManager

AbilityResolver

ConditionEvaluator

TargetResolver

EffectExecutor
Event
GameEvent

EventBus

EventQueue
Action
IAction

ActionQueue

DrawCardAction

MoveCardAction

SummonAction

CastSpellAction

AttackAction

DestroyAction

TapAction

UntapAction
RuleEngine
RuleEngine

TriggerSystem

ReplacementSystem

ContinuousEffectSystem

StateBasedActionSystem

VictorySystem

PrioritySystem
Database
CardDatabase

AbilityDatabase

ExpansionDatabase
Serialization
SaveGameSerializer

LoadGameSerializer

JsonConverter