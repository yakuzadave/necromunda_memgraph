---
title: Optimizing Memgraph Schema for Necromunda Data
description: Enhance your Necromunda gameplay by optimizing the Memgraph schema for efficient data management and improved game mechanics.
date: 2025-04-15T15:45:44.453Z
preview: ""
draft: true
tags:
    - campaign structure
    - data management
    - data querying
    - database design
    - equipment relationships
    - fighter types
    - game design
    - game mechanics
    - game scenarios
    - Memgraph
    - Necromunda
    - performance optimization.
    - schema optimization
    - tabletop gaming
categories:
    - campaign structure
    - data management
    - database schema
    - equipment organization
    - fighter relationships.
    - game mechanics
    - gameplay enhancement
    - Memgraph
    - Necromunda
    - optimization
slug: optimizing-memgraph-schema-necromunda-data
---

# Necromunda Database Schema Structure

## Node Types

The database contains the following primary node types:

### Core Game Elements

| Node Label        | Description                                   | Count |
| ----------------- | --------------------------------------------- | ----- |
| `Fighter:Ganger`  | Fighter models with the Ganger role           | 75    |
| `Fighter`         | Base fighter models                           | 10    |
| `FighterType`     | Types of fighters (Leader, Champion, etc.)    | 14    |
| `Faction:FACTION` | House factions and other faction types        | 22    |
| `Gang`            | Gang groups that fighters belong to           | 5     |
| `Skill`           | Skills that fighters can acquire              | 17    |
| `SkillCategory`   | Categories of skills (Combat, Ferocity, etc.) | 5     |

### Equipment & Weapons

| Node Label            | Description                                | Count |
| --------------------- | ------------------------------------------ | ----- |
| `RangedWeapon:Weapon` | Ranged weapons                             | 27    |
| `MeleeWeapon:Weapon`  | Melee weapons                              | 28    |
| `Armor:EQUIPMENT`     | Armor types with equipment properties      | 18    |
| `Armor`               | Basic armor types                          | 10    |
| `Equipment`           | General equipment items                    | 8     |
| `Grenade`             | Explosive weapons                          | 20    |
| `WeaponCategory`      | Categories of weapons (Basic, Heavy, etc.) | 8     |
| `WeaponType`          | Types of weapons                           | 11    |
| `WeaponTrait`         | Special traits weapons can have            | 22    |
| `ArmorType`           | Types of armor                             | 3     |

### Campaign & Scenarios

| Node Label           | Description                      | Count |
| -------------------- | -------------------------------- | ----- |
| `Campaign`           | Campaign structures              | 2     |
| `CampaignStage`      | Stages within campaigns          | 5     |
| `Scenario:SCENARIO`  | Game scenarios                   | 142   |
| `Scenario`           | Additional scenario types        | 6     |
| `ScenarioCollection` | Collections of related scenarios | 8     |
| `ScenarioRule`       | Rules specific to scenarios      | 7     |
| `ScenarioReward`     | Rewards for completing scenarios | 3     |
| `ScenarioCategory`   | Categories of scenarios          | 3     |
| `BattleType`         | Types of battles                 | 7     |
| `RewardType`         | Types of rewards                 | 9     |

### Environment & Terrain

| Node Label             | Description                  | Count |
| ---------------------- | ---------------------------- | ----- |
| `Environment`          | Environment settings         | 6     |
| `TerrainType`          | Types of terrain             | 14    |
| `Rules:TERRAINFEATURE` | Rules related to terrain     | 11    |
| `EQUIPMENT:Rules`      | Equipment with special rules | 22    |
| `TerrainRequirement`   | Requirements for terrain     | 2     |
| `ModelRequirement`     | Requirements for models      | 1     |

### Game Mechanics

| Node Label      | Description                   | Count |
| --------------- | ----------------------------- | ----- |
| `GameMechanic`  | Core game mechanics           | 12    |
| `Action`        | Actions fighters can take     | 5     |
| `Injury:ACTION` | Injury actions/effects        | 251   |
| `Injury`        | Basic injury types            | 103   |
| `Combat`        | Combat mechanics              | 1     |
| `Condition`     | Conditions affecting fighters | 1     |
| `Event`         | Game events                   | 6     |
| `Category`      | Generic categories            | 5     |
| `Category:ROLE` | Role categories               | 5     |

### Collections & Organization

| Node Label                  | Description                 | Count |
| --------------------------- | --------------------------- | ----- |
| `Collection`                | General collections         | 31    |
| `Collection:FACTION`        | Faction collections         | 6     |
| `Collection:TERRAINFEATURE` | Terrain feature collections | 3     |
| `Context`                   | Context nodes               | 4     |
| `ACTION:Context`            | Action contexts             | 4     |
| `Context:TERRAINFEATURE`    | Terrain contexts            | 4     |

### Memory & Documentation

| Node Label                         | Description                   | Count |
| ---------------------------------- | ----------------------------- | ----- |
| `Memory`                           | Base memory nodes             | 221   |
| `FIGHTER:FIGHTER_MEMORY`           | Fighter memories              | 42    |
| `GAMEMECHANIC:GAMEMECHANIC_MEMORY` | Game mechanic memories        | 32    |
| `Documentation`                    | Documentation nodes           | 1     |
| `DatabaseSchema:Documentation`     | Database schema documentation | 1     |
| `QueryPattern`                     | Query pattern documentation   | 3     |

## Relationship Types

The database contains the following primary relationship types:

### Hierarchical Relationships

| Relationship  | Description                                     | Count |
| ------------- | ----------------------------------------------- | ----- |
| `BELONGS_TO`  | Entity belongs to a parent entity               | 458   |
| `IS_TYPE`     | Entity is of a specific type                    | 224   |
| `MEMBER_OF`   | Fighter is a member of a gang/faction           | 10    |
| `HAS_FIGHTER` | Gang has a specific fighter                     | 6     |
| `COMMANDS`    | Superior fighter type commands subordinate type | 10    |
| `CONTROLS`    | Leader controls special fighter types           | 10    |

### Equipment Relationships

| Relationship       | Description                    | Count |
| ------------------ | ------------------------------ | ----- |
| `EQUIPPED_WITH`    | Fighter is equipped with gear  | 31    |
| `USES`             | Fighter uses a specific weapon | 2     |
| `CAN_USE`          | Entity can use equipment       | 75    |
| `EQUIPMENT_OPTION` | Available equipment option     | 342   |
| `HAS_TRAIT`        | Weapon has a specific trait    | 27    |

### Skill Relationships

| Relationship          | Description                 | Count |
| --------------------- | --------------------------- | ----- |
| `HAS_SKILL`           | Fighter has a skill         | 93    |
| `BELONGS_TO_CATEGORY` | Skill belongs to a category | 11    |

### Campaign & Scenario Relationships

| Relationship        | Description                       | Count |
| ------------------- | --------------------------------- | ----- |
| `HAS_STAGE`         | Campaign has a stage              | 5     |
| `NEXT_STAGE`        | Stage leads to another stage      | 4     |
| `INCLUDES_SCENARIO` | Stage includes specific scenarios | 4     |
| `OCCURS_IN`         | Scenario occurs in environment    | 11    |
| `HAS_RULE`          | Entity has specific rules         | 408   |
| `HAS_REWARD`        | Scenario has rewards              | 3     |
| `HAS_OPTION`        | Rule has options                  | 4     |

### Game Mechanics Relationships

| Relationship  | Description                | Count |
| ------------- | -------------------------- | ----- |
| `SUFFERED`    | Fighter suffered injury    | 353   |
| `PERFORMED`   | Fighter performed action   | 4     |
| `TARGETED`    | Action targeted fighter    | 4     |
| `ENGAGED_IN`  | Fighter engaged in combat  | 3     |
| `OCCURRED_IN` | Action occurred in turn    | 3     |
| `GOVERNED_BY` | Entity governed by rules   | 159   |
| `INCLUDES`    | Entity includes sub-entity | 161   |

### Memory Relationships

| Relationship   | Description              | Count |
| -------------- | ------------------------ | ----- |
| `REFERENCES`   | Memory references entity | 1932  |
| `TEMPLATE_FOR` | Template for fighter     | 471   |
| `CATEGORIZES`  | Categorizes entities     | 145   |

## Schema Improvements Added

The following improvements were made to the original schema:

1. **Standardized Faction Naming**: Normalized faction names (e.g., "Escher" to "House Escher")

2. **Fighter Type Hierarchy**: Created clear fighter types (Leader, Champion, Ganger, etc.) with proper hierarchical relationships

3. **Weapon Categorization**: Added weapon categories (Basic, Heavy, Pistol, etc.) and linked weapons to these categories

4. **Skill Organization**: Linked skills to their appropriate skill categories (Combat, Ferocity, Cunning, etc.)

5. **Campaign Structure**: Created a campaign progression model with sequential stages and linked scenarios

6. **Gang Hierarchy**: Established command relationships between fighter types reflecting the gang structure

This improved schema enables more effective querying for game mechanics, campaign progression, fighter relationships, and equipment options.
