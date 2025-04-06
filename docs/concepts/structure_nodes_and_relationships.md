# Structuring Nodes and Relationships
In Necromunda, key entities such as Fighters, Skills, Equipment, Gangs, and Territories can be represented as nodes in Memgraph. Each node should have properties that encapsulate its attributes. Relationships between these nodes represent the interactions and associations in the game.

Ganger Nodes
Fighters are central to Necromunda and can be represented with the label :Fighter. Essential properties include:​
Memgraph

- `name`: The fighter's name.​
- `type`: Role within the gang (e.g., Leader, Champion, Ganger, Juve).​
- `gang`: The gang to which the fighter belongs.​
- `faction`: The faction of the gang (e.g., House Escher, House Goliath).​
- `experience`: Experience points gained through battles and missions.​
- `skills`: List of skills the fighter has acquired.​
- `equipment`: List of items the fighter possesses (e.g., weapons, armor).​
- `weapons`: List of weapons the fighter can use.​
- `armor`: List of armor pieces the fighter can wear.​
- `wounds`: Number of wounds the fighter can sustain before being incapacitated.​
- `movement`: Movement speed of the fighter (in inches).​
- `ws`: Weapon Skill rating (combat skill).​
- `bs`: Ballistic Skill rating (ranged combat skill).​
- `s`: Strength rating (physical strength).​
- `t`: Toughness rating (resilience).​
- `w`: Willpower rating (mental fortitude).​
- `i`: Initiative rating (reaction speed).​
- `a`: Attacks that the fighter can make (in each round). For example `1` means that they can make one attack per action. ​​
- `ld`: Leadership rating (command ability).​
- `cl`: Cool rating (ability to remain calm under pressure).​
- `wil`: Will rating (mental resilience).​
- `int`: Intelligence rating (problem-solving ability).​
- `status`: Current condition (e.g., Active, Pinned, Seriously Injured, Out of Action).​
- `injuries`: List of sustained injuries.​

## Cypher Query to Create a Fighter Node:
```cypher
MERGE (f:Fighter {name: 'Goliath Brute', type: 'Ganger', gang: 'House Goliath', faction: 'House Goliath', experience: 0, skills: [], equipment: [], weapons: [], armor: [], wounds: 1, movement: 4, ws: 3, bs: 5, s: 4, t: 4, w: 1, i: 3, a: 1, ld: 7, cl: 6, wil: 5, int: 4, status: 'Active', injuries: []})
RETURN f
```

## Skills Nodes
Skills can be represented with the label :Skill. Each skill should have properties that define its effects and requirements. Essential properties include:​
- `name`: The name of the skill (e.g., Quick Shot, Dodge).​
- `type`: Type of skill (e.g., Combat, Agility, Leadership).​
- `description`: Description of the skill's effects and benefits.​
- `requirements`: List of requirements to acquire the skill (e.g., minimum experience, specific fighter type).​
- `cost`: Cost in experience points to acquire the skill.​
- `effects`: List of effects the skill provides (e.g., +1 to WS, reroll 1s).​
- `duration`: Duration of the skill's effects (e.g., permanent, until end of battle).​
- `target`: Target of the skill (e.g., self, enemy).​
- `activation`: Activation method (e.g., during combat, at the start of the game).​
- `limitations`: Any limitations or restrictions on the skill (e.g., can only be used once per game).​
- `fluff`: Flavor text or background information about the skill (optional).​
- `source`: Source of the skill (e.g., rulebook, expansion).​
- `gangs`: List of gangs that can use the skill (e.g., House Escher, House Goliath).​

### Cypher Query to Create a Skill Node:
```cypher
MERGE (s:Skill {name: 'Quick Shot', type: 'Combat', description: 'Allows the fighter to shoot before moving.', requirements: ['Experience 1'], cost: 2, effects: ['+1 to BS'], duration: 'Permanent', target: 'Self', activation: 'During Combat', limitations: ['Can only be used once per game'], fluff: 'A quick reflex allows the fighter to shoot before moving.', source: 'Rulebook', gangs: ['House Goliath']})
RETURN s
```

## Equipment Nodes
Equipment can be represented with the label :Equipment. Each piece of equipment should have properties that define its attributes and effects. Essential properties include:​
- `name`: The name of the equipment (e.g., Boltgun, Flamer).​
- `type`: Type of equipment (e.g., Weapon, Armor, Item).​
- `description`: Description of the equipment's effects and benefits.​
- `requirements`: List of requirements to use the equipment (e.g., minimum strength, specific fighter type).​
- `cost`: Cost in credits to acquire the equipment.​
- `effects`: List of effects the equipment provides (e.g., +1 to damage, +2 to armor save).​
- `range`: Range of the equipment (e.g., 12", 24").​
- `damage`: Damage dealt by the equipment (e.g., 1d6, 2d6).​
- `armor_save`: Armor save provided by the equipment (e.g., 4+, 5+).​
- `weight`: Weight of the equipment (e.g., Light, Heavy).​
- `limitations`: Any limitations or restrictions on the equipment (e.g., can only be used by certain fighter types).​
- `fluff`: Flavor text or background information about the equipment (optional).​
- `source`: Source of the equipment (e.g., rulebook, expansion).​
- `gangs`: List of gangs that can use the equipment (e.g., House Escher, House Goliath).​
- `rarity`: Rarity of the equipment (e.g., Common, Rare).​
- `availability`: Availability of the equipment (e.g., Available, Limited).​
- `modifiers`: List of modifiers provided by the equipment (e.g., +1 to WS, -1 to BS).​
- `special_rules`: List of special rules associated with the equipment (e.g., Rapid Fire, Blast).​

### Cypher Query to Create an Equipment Node:
```cypher
MERGE (e:Equipment {name: 'Boltgun', type: 'Weapon', description: 'A powerful ranged weapon.', requirements: ['Strength 3'], cost: 50, effects: ['+1 to damage'], range: '24"', damage: '1d6', armor_save: 'N/A', weight: 'Heavy', limitations: ['Can only be used by Gangers'], fluff: 'A reliable and deadly weapon.', source: 'Rulebook', gangs: ['House Goliath'], rarity: 'Common', availability: 'Available', modifiers: ['+1 to BS'], special_rules: ['Rapid Fire']})
RETURN e
```

## Gangs Nodes
Gangs can be represented with the label :Gang. Each gang should have properties that define its attributes and characteristics. Essential properties include:​
- `name`: The name of the gang (e.g., House Goliath, House Escher).​
- `faction`: The faction of the gang (e.g., House Goliath, House Escher).​
- `territories`: List of territories controlled by the gang. 
- `members`: List of fighters that belong to the gang. ​
- `status`: Current status of the gang (e.g., Active, Inactive).​
- `allies`: List of allied gangs. ​
- `rivals`: List of rival gangs. ​
- `leader`: The leader of the gang (a reference to a Fighter node).​
- `champions`: List of champions in the gang (references to Fighter nodes).​
- `credits`: The amount of credits the gang possesses. ​​
- `reputation`: The gang's reputation in the underhive. ​
- `campaign` : The campaign the gang is currently involved in. ​
- `history`: List of significant events in the gang's history. ​
- `fluff`: Flavor text or background information about the gang (optional).​
- `source`: Source of the gang (e.g., rulebook, expansion).​

### Cypher Query to Create a Gang Node:
```cypher
MERGE (g:Gang {name: 'House Goliath', faction: 'House Goliath', territories: [], members: [], status: 'Active', allies: [], rivals: [], leader: 'Goliath Brute', champions: [], credits: 1000, reputation: 5, campaign: 'Underhive Wars', history: [], fluff: 'A gang of brutal fighters.', source: 'Rulebook'})
RETURN g
```

## Territories Nodes
Territories can be represented with the label :Territory. Each territory should have properties that define its attributes and characteristics. Essential properties include:​
- `name`: The name of the territory (e.g., Spire, Sewers).​
- `type`: Type of territory (e.g., Industrial, Residential).​
- `description`: Description of the territory's effects and benefits.​
- `controlled_by`: The gang that controls the territory (a reference to a Gang node).​
- `benefits`: List of benefits provided by the territory (e.g., +1 credit per turn, access to resources).​
- `drawbacks`: List of drawbacks associated with the territory (e.g., +1 to enemy attacks, limited resources).​
- `location`: Location of the territory (e.g., Sector 1, Zone 3).​
- `status`: Current status of the territory (e.g., Controlled, Contested).​
- `fluff`: Flavor text or background information about the territory (optional).​
- `source`: Source of the territory (e.g., rulebook, expansion).​

### Cypher Query to Create a Territory Node:
```cypher
MERGE (t:Territory {name: 'Sewers', type: 'Industrial', description: 'A network of underground tunnels.', controlled_by: 'House Goliath', benefits: ['+1 credit per turn'], drawbacks: ['+1 to enemy attacks'], location: 'Sector 2', status: 'Controlled', fluff: 'A dark and dangerous place.', source: 'Rulebook'})
RETURN t
```

## Armor Nodes
Armor can be represented with the label :Armor. Each armor piece should have properties that define its attributes and effects. Essential properties include:​
- `name`: The name of the armor (e.g., Flak Armor, Carapace Armor).​
- `type`: Type of armor (e.g., Light, Heavy).​
- `description`: Description of the armor's effects and benefits.​
- `requirements`: List of requirements to use the armor (e.g., minimum strength, specific fighter type).​
- `cost`: Cost in credits to acquire the armor.​
- `effects`: List of effects the armor provides (e.g., +1 to armor save, -1 to movement).​
- `armor_save`: Armor save provided by the armor (e.g., 4+, 5+).​

### Cypher Query to Create an Armor Node:
```cypher
MERGE (a:Armor {name: 'Flak Armor', type: 'Light', description: 'A basic protective suit.', requirements: ['Strength 3'], cost: 30, effects: ['+1 to armor save'], armor_save: '5+'})
RETURN a
```

# Querying the Graph
Once your data is structured, you can perform various queries to extract meaningful insights.​

Retrieve All Fighters and Their Skills:
```cypher
MATCH (f:Fighter)-[:HAS_SKILL]->(s:Skill)
RETURN f.name AS Fighter, COLLECT(s.name) AS Skills
```
Retrieve All Gangs and Their Territories:
```cypher
MATCH (g:Gang)-[:CONTROLS]->(t:Territory)
RETURN g.name AS Gang, COLLECT(t.name) AS Territories
```

Retrieve All Equipment Used by a Specific Gang:
```cypher
MATCH (g:Gang)-[:USES]->(e:Equipment)
WHERE g.name = 'House Goliath'
RETURN g.name AS Gang, COLLECT(e.name) AS Equipment
```

Retrieve All Fighters in a Specific Gang:
```cypher
MATCH (g:Gang)-[:HAS_MEMBER]->(f:Fighter)
WHERE g.name = 'House Goliath'
RETURN g.name AS Gang, COLLECT(f.name) AS Fighters
```

Find Fighters with Specific Skills:
```cypher
MATCH (f:Fighter)-[:HAS_SKILL]->(s:Skill)
WHERE s.name = 'Quick Shot'
RETURN f.name AS Fighter, s.name AS Skill
```

# Best Practices
- Use meaningful labels and properties to ensure clarity and ease of understanding.
- Utilize relationships to represent interactions and associations between nodes.
- Keep your graph structure flexible to accommodate future expansions or changes in the game.
- Document your graph structure and queries for future reference and collaboration.
- Consider using indexes on frequently queried properties to improve performance.
- Regularly back up your graph data to prevent loss of information.
- Test your queries with sample data to ensure accuracy and efficiency.
- Use descriptive names for your nodes and relationships to enhance readability.
- Consider using tags or categories to group similar nodes for easier querying.
- Utilize Memgraph's visualization tools to gain insights into your graph structure and relationships.
- Monitor performance and optimize queries as your graph grows in size and complexity.

## Indexing

```cypher
CREATE INDEX ON :Fighter(name)
CREATE INDEX ON :Skill(name)
CREATE INDEX ON :Equipment(name)
CREATE INDEX ON :Gang(name)
```

## Constraints
    
```cypher
CREATE CONSTRAINT ON (f:Fighter) ASSERT f.name IS UNIQUE
CREATE CONSTRAINT ON (s:Skill) ASSERT s.name IS UNIQUE
```

## Data Import

### CSV Import
```cypher
LOAD CSV WITH HEADERS FROM 'file:///fighters.csv' AS row
CREATE (:Fighter {name: row.name, type: row.type, gang: row.gang, faction: row.faction, experience: toInteger(row.experience), skills: split(row.skills, ','), equipment: split(row.equipment, ','), weapons: split(row.weapons, ','), armor: split(row.armor, ','), wounds: toInteger(row.wounds), movement: toInteger(row.movement), ws: toInteger(row.ws), bs: toInteger(row.bs), s: toInteger(row.s), t: toInteger(row.t), w: toInteger(row.w), i: toInteger(row.i), a: toInteger(row.a), ld: toInteger(row.ld), cl: toInteger(row.cl), wil: toInteger(row.wil), int: toInteger(row.int), status: row.status, injuries: split(row.injuries, ',')})
```
```cypher
LOAD CSV WITH HEADERS FROM 'file:///skills.csv' AS row
CREATE (:Skill {name: row.name, type: row.type, description: row.description, requirements: split(row.requirements, ','), cost: toInteger(row.cost), effects: split(row.effects, ','), duration: row.duration, target: row.target, activation: row.activation, limitations: split(row.limitations, ','), fluff: row.fluff, source: row.source, gangs: split(row.gangs, ',')})
```
```cypher
LOAD CSV WITH HEADERS FROM 'file:///equipment.csv' AS row
CREATE (:Equipment {name: row.name, type: row.type, description: row.description, requirements: split(row.requirements, ','), cost: toInteger(row.cost), effects: split(row.effects, ','), range: row.range, damage: row.damage, armor_save: row.armor_save, weight: row.weight, limitations: split(row.limitations, ','), fluff: row.fluff, source: row.source, gangs: split(row.gangs, ','), rarity: row.rarity, availability: row.availability, modifiers: split(row.modifiers, ','), special_rules: split(row.special_rules, ',')})
```
```cypher
LOAD CSV WITH HEADERS FROM 'file:///gangs.csv' AS row
CREATE (:Gang {name: row.name, faction: row.faction, territories: split(row.territories, ','), members: split(row.members, ','), status: row.status, allies: split(row.allies, ','), rivals: split(row.rivals, ','), leader: row.leader, champions: split(row.champions, ','), credits: toInteger(row.credits), reputation: toInteger(row.reputation), campaign: row.campaign, history: split(row.history, ','), fluff: row.fluff, source: row.source})
```
```cypher
LOAD CSV WITH HEADERS FROM 'file:///territories.csv' AS row
CREATE (:Territory {name: row.name, type: row.type, description: row.description, controlled_by: row.controlled_by, benefits: split(row.benefits, ','), drawbacks: split(row.drawbacks, ','), location: row.location, status: row.status, fluff: row.fluff, source: row.source})
```
```cypher
LOAD CSV WITH HEADERS FROM 'file:///armor.csv' AS row
CREATE (:Armor {name: row.name, type: row.type, description: row.description, requirements: split(row.requirements, ','), cost: toInteger(row.cost), effects: split(row.effects, ','), armor_save: row.armor_save})
```
```cypher
LOAD CSV WITH HEADERS FROM 'file:///relationships.csv' AS row
MATCH (f:Fighter {name: row.fighter_name}), (g:Gang {name: row.gang_name})
CREATE (f)-[:BELONGS_TO]->(g)
MATCH (f:Fighter {name: row.fighter_name}), (s:Skill {name: row.skill_name})
CREATE (f)-[:HAS_SKILL]->(s)
MATCH (f:Fighter {name: row.fighter_name}), (e:Equipment {name: row.equipment_name})
CREATE (f)-[:HAS_EQUIPMENT]->(e)
MATCH (g:Gang {name: row.gang_name}), (t:Territory {name: row.territory_name})
CREATE (g)-[:CONTROLS]->(t)
MATCH (g:Gang {name: row.gang_name}), (a:Armor {name: row.armor_name})
CREATE (g)-[:HAS_ARMOR]->(a)
MATCH (g:Gang {name: row.gang_name}), (s:Skill {name: row.skill_name})
CREATE (g)-[:HAS_SKILL]->(s)
MATCH (g:Gang {name: row.gang_name}), (e:Equipment {name: row.equipment_name})
```


> ​Creating a graph database schema in Memgraph to model Necromunda's complex gameplay involves defining nodes and relationships that represent entities such as Fighters, Gangs, Equipment, Skills, and Territories. This guide provides Cypher queries to set up this schema, ensuring a robust foundation for analyzing and managing Necromunda gameplay data.

