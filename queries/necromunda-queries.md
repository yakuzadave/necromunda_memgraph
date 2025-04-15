# Necromunda Database Organization Queries

This document contains the Cypher queries used to improve the organization of the Necromunda data in Memgraph.

## 1. Standardizing Faction Names

### Update fighters with "Escher" to use "House Escher"
```cypher
MATCH (f:Fighter)
WHERE f.faction = 'Escher'
SET f.faction = 'House Escher'
RETURN count(*) as UpdatedFighters
```

### Standardize faction names across all faction nodes
```cypher
// Create a mapping of correct faction names
WITH [
  {old: 'Escher', new: 'House Escher'},
  {old: 'Cawdor', new: 'House Cawdor'},
  {old: 'Delaque', new: 'House Delaque'},
  {old: 'Van Saar', new: 'House Van Saar'}
] AS corrections

// Update faction nodes
UNWIND corrections AS correction
MATCH (f:Faction)
WHERE f.name = correction.old
SET f.name = correction.new
RETURN correction.old AS OldName, correction.new AS NewName, count(*) AS UpdatedNodes
```

## 2. Standardizing Node Labels

### Add FACTION label to all Faction nodes
```cypher
MATCH (f:Faction)
WHERE NOT f:FACTION
SET f:FACTION
RETURN count(*) as AddedFACTIONLabel
```

## 3. Creating FighterType Hierarchy

### Create basic fighter type nodes
```cypher
WITH [
  'Leader', 'Champion', 'Ganger', 'Juve', 'Prospect', 'Specialist', 'Brute', 'Exotic Beast'
] AS baseTypes

UNWIND baseTypes AS baseType
MERGE (ft:FighterType {name: baseType})
RETURN baseType AS CreatedBaseTypes
```

### Link fighters to their proper types
```cypher
MATCH (f:Fighter)
WHERE f.ganger_type IS NOT NULL
WITH f, 
CASE 
  WHEN f.ganger_type CONTAINS '(Leader)' THEN 'Leader'
  WHEN f.ganger_type CONTAINS '(Champion)' THEN 'Champion'
  WHEN f.ganger_type CONTAINS '(Ganger)' THEN 'Ganger'
  WHEN f.ganger_type CONTAINS '(Juve)' THEN 'Juve'
  WHEN f.ganger_type CONTAINS '(Prospect)' THEN 'Prospect'
  WHEN f.ganger_type CONTAINS '(Specialist)' THEN 'Specialist'
  WHEN f.ganger_type CONTAINS '(Brute)' THEN 'Brute'
  WHEN f.ganger_type CONTAINS '(Exotic Beast)' THEN 'Exotic Beast'
  WHEN f.ganger_type CONTAINS 'Leader' OR f.ganger_type CONTAINS 'Matriarch' OR f.ganger_type CONTAINS 'Chieftain' OR 
       f.ganger_type CONTAINS 'Master' OR f.ganger_type CONTAINS 'Priest' THEN 'Leader'
  WHEN f.ganger_type CONTAINS 'Champion' THEN 'Champion'
  WHEN f.ganger_type CONTAINS 'Ganger' THEN 'Ganger'
  WHEN f.ganger_type CONTAINS 'Juve' THEN 'Juve'
  ELSE 'Ganger'
END AS baseType

MATCH (ft:FighterType {name: baseType})
MERGE (f)-[:IS_TYPE]->(ft)
```

### Handle fighters with NULL ganger_type
```cypher
MATCH (f:Fighter)
WHERE f.ganger_type IS NULL
MATCH (ft:FighterType {name: 'Ganger'})
MERGE (f)-[:IS_TYPE]->(ft)
```

## 4. Improving Weapon Organization

### Create weapon categories
```cypher
WITH ['Basic', 'Heavy', 'Pistol', 'Special', 'Melee', 'Grenades', 'Ammunition', 'Wargear'] AS categories
UNWIND categories AS category
MERGE (wc:WeaponCategory {name: category})
RETURN category AS CreatedCategories
```

### Categorize weapons
```cypher
WITH [
  {weapon: 'Lasgun', category: 'Basic'},
  {weapon: 'Autogun', category: 'Basic'},
  {weapon: 'Boltgun', category: 'Basic'},
  {weapon: 'Combat shotgun', category: 'Basic'},
  {weapon: 'Stub gun', category: 'Pistol'},
  {weapon: 'Laspistol', category: 'Pistol'},
  {weapon: 'Bolt pistol', category: 'Pistol'},
  {weapon: 'Knife', category: 'Melee'},
  {weapon: 'Chain axe', category: 'Melee'},
  {weapon: 'Power sword', category: 'Melee'},
  {weapon: 'Heavy stubber', category: 'Heavy'},
  {weapon: 'Missile launcher', category: 'Heavy'},
  {weapon: 'Plasma gun', category: 'Special'},
  {weapon: 'Melta gun', category: 'Special'},
  {weapon: 'Frag grenade', category: 'Grenades'},
  {weapon: 'Krak grenade', category: 'Grenades'}
] AS weaponCategories

UNWIND weaponCategories AS mapping
MATCH (w:Weapon {name: mapping.weapon})
MATCH (c:WeaponCategory {name: mapping.category})
MERGE (w)-[:BELONGS_TO_CATEGORY]->(c)
```

## 5. Creating Campaign Structure

### Create campaign structure nodes
```cypher
CREATE (c:Campaign {name: 'Dominion Campaign', description: 'A campaign for control of hive territory'})
CREATE (s1:CampaignStage {name: 'Gang Creation', stage_number: 1})
CREATE (s2:CampaignStage {name: 'Turf War', stage_number: 2})
CREATE (s3:CampaignStage {name: 'Expansion', stage_number: 3})
CREATE (s4:CampaignStage {name: 'Dominance', stage_number: 4})
CREATE (s5:CampaignStage {name: 'Finale', stage_number: 5})

// Create relationships between campaign and stages
CREATE (c)-[:HAS_STAGE]->(s1)
CREATE (c)-[:HAS_STAGE]->(s2)
CREATE (c)-[:HAS_STAGE]->(s3)
CREATE (c)-[:HAS_STAGE]->(s4)
CREATE (c)-[:HAS_STAGE]->(s5)

// Create sequential relationships between stages
CREATE (s1)-[:NEXT_STAGE]->(s2)
CREATE (s2)-[:NEXT_STAGE]->(s3)
CREATE (s3)-[:NEXT_STAGE]->(s4)
CREATE (s4)-[:NEXT_STAGE]->(s5)
```

## 6. Linking Scenarios to Campaign Stages

### Link scenarios to Turf War stage
```cypher
MATCH (stage:CampaignStage {name: 'Turf War'})
MATCH (s:Scenario) 
WHERE s.name IN ['Border Dispute', 'Battle Of The Riftways', 'Tunnel Skirmish']
MERGE (stage)-[:INCLUDES_SCENARIO]->(s)
```

### Link scenarios to Expansion stage
```cypher
MATCH (stage:CampaignStage {name: 'Expansion'})
MATCH (s:Scenario) 
WHERE s.name IN ['Ambush', 'Assassinate', 'Forgotten Riches']
MERGE (stage)-[:INCLUDES_SCENARIO]->(s)
```

## 7. Organizing Skills by Category

### Categorize skills
```cypher
WITH [
  {skill: 'Nerves of Steel', category: 'Ferocity'},
  {skill: 'Fast Shot', category: 'Combat'},
  {skill: 'Gunfighter', category: 'Combat'},
  {skill: 'Marksman', category: 'Combat'},
  {skill: 'Hip Shooting', category: 'Combat'},
  {skill: 'Counter Attack', category: 'Combat'},
  {skill: 'Combat Master', category: 'Combat'},
  {skill: 'Berserker', category: 'Ferocity'},
  {skill: 'Step Aside', category: 'Cunning'},
  {skill: 'Spring Up', category: 'Cunning'},
  {skill: 'Overseer', category: 'Leadership'},
  {skill: 'Rally', category: 'Leadership'},
  {skill: 'Stimmer', category: 'Savant'},
  {skill: 'Medic', category: 'Savant'}
] AS skillCategories

// Link skills to their categories
UNWIND skillCategories AS mapping
MATCH (s:Skill {name: mapping.skill})
MATCH (c:SkillCategory {name: mapping.category})
MERGE (s)-[:BELONGS_TO_CATEGORY]->(c)
```

## 8. Creating Gang Hierarchy Model

### Create gang hierarchy relationships
```cypher
MATCH (l:FighterType {name: 'Leader'})
MATCH (c:FighterType {name: 'Champion'})
MATCH (g:FighterType {name: 'Ganger'})
MATCH (j:FighterType {name: 'Juve'})
MATCH (p:FighterType {name: 'Prospect'})
MATCH (s:FighterType {name: 'Specialist'})
MATCH (b:FighterType {name: 'Brute'})
MATCH (e:FighterType {name: 'Exotic Beast'})

// Create hierarchy relationships
MERGE (l)-[:COMMANDS]->(c)
MERGE (c)-[:COMMANDS]->(g)
MERGE (c)-[:COMMANDS]->(s)
MERGE (g)-[:COMMANDS]->(j)
MERGE (g)-[:COMMANDS]->(p)
MERGE (l)-[:CONTROLS]->(b)
MERGE (l)-[:CONTROLS]->(e)
```

## 9. Useful Queries for Checking Structure

### Check faction structure
```cypher
MATCH (f:FACTION)
RETURN f.name AS FactionName
ORDER BY FactionName
```

### Check fighter type hierarchy
```cypher
MATCH (ft1:FighterType)-[:COMMANDS|CONTROLS]->(ft2:FighterType)
RETURN ft1.name AS Superior, ft2.name AS Subordinate
```

### Check campaign structure
```cypher
MATCH (c:Campaign)-[:HAS_STAGE]->(s:CampaignStage)
RETURN c.name AS Campaign, s.name AS Stage, s.stage_number AS StageNumber
ORDER BY s.stage_number
```

### Check scenarios linked to campaign stages
```cypher
MATCH (s:CampaignStage)-[:INCLUDES_SCENARIO]->(sc:Scenario)
RETURN s.name AS Stage, collect(sc.name) AS Scenarios
```

### Check skills by category
```cypher
MATCH (s:Skill)-[:BELONGS_TO_CATEGORY]->(c:SkillCategory)
RETURN c.name AS Category, collect(s.name) AS Skills
```

### Check weapons by category
```cypher
MATCH (w:Weapon)-[:BELONGS_TO_CATEGORY]->(c:WeaponCategory)
RETURN c.name AS Category, collect(w.name) AS Weapons
```
