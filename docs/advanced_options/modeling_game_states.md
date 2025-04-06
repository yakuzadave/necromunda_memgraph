# Modeling Game States (Snapshot vs Dynamic)
A. Snapshot GameState Modeling
This strategy creates a new subgraph each turn:
```cypher
MERGE (:GameState {
  id: 'match001_turn01',
  turn: 1,
  phase: 'Movement',
  timestamp: datetime()
});
```

Link Fighters to a specific GameState snapshot:
```cypher
MATCH (f:Fighter {name: 'John Doe'}),
      (gs:GameState {id: 'match001_turn01'})
MERGE (f)-[:INVOLVED_IN]->(gs);
```
> This model works best for discrete turn comparisons and after-action analysis.
>
> It allows for easy querying of game states at any point in time, making it ideal for historical analysis and reporting.
> However, it can lead to data redundancy and increased storage requirements, especially in long campaigns with many turns.
> Consider using this model if you need to frequently analyze game states at specific points in time or if you want to maintain a complete history of the game.

# Dynamic GameState Modeling
Instead of duplicating, modify properties over time and use relationships like NEXT and HAS_CONDITION to track changes.

Create a timeline of turns:
```cypher
MERGE (:Turn {number: 1})-[:NEXT]->(:Turn {number: 2});
MERGE (:Turn {number: 2})-[:NEXT]->(:Turn {number: 3});
MERGE (:Turn {number: 3})-[:NEXT]->(:Turn {number: 4});
```

Update fighter status each turn:
```cypher
MATCH (f:Fighter {name: 'John Doe'}), (t:Turn {number: 2})
MERGE (f)-[:STATUS_IN {status: 'Pinned'}]->(t);
```

Or use temporal relationships to avoid overwriting:
```cypher
MATCH (f:Fighter {name: 'John Doe'}), (t:Turn {number: 2})
MERGE (f)-[:STATUS_IN {status: 'Pinned'}]->(t);
```


# Modeling Actions as Nodes
To capture events and causal relationships, create explicit :Action nodes:
```cypher
MERGE (:Action {
  type: 'Move',
  timestamp: datetime(),
  outcome: 'Advanced 5”'
});
```

Link action to actor and target:
```cypher
MATCH (f:Fighter {name: 'John Doe'}),
      (t:Turn {number: 2}),
      (a:Action {type: 'Move', outcome: 'Advanced 5”'})
MERGE (f)-[:PERFORMED]->(a),
       (a)-[:OCCURRED_IN]->(t);
```

More complex actions like shooting
can also be modeled:
```cypher
CREATE (:Action {
  type: 'Shoot',
  weapon: 'Plasma Gun',
  result: 'Injury',
  damage: 2
});
```

# Representing Conditions
Model game-affecting states (e.g., Pinned, Seriously Injured) as either properties or separate Condition nodes.
```cypher
MERGE (:Condition {
  name: 'Blinded',
  duration: '1 turn',
  effect: 'Fighter cannot make shooting attacks'
});
    
```

```cypher
MATCH (gs:GameState {id: 'match001_turn01'})
MERGE (c)-[:CONDITION_IN]->(gs);
```

# Adding Terrain and Spatial Awareness
Terrain can be modeled as nodes with properties like type, height, and cover:
```cypher
MERGE (:Terrain {
  name: 'Barricade',
  type: 'Cover',
  cover_bonus: 1
});
```

## Link a Fighter to their current position:
```cypher
MATCH (f:Fighter {name: 'John Doe'}),
      (t:Terrain {name: 'Barricade'})
MERGE (f)-[:OCCUPIES]->(t);
```

> This allows for spatial queries, such as finding all fighters within a certain distance of a terrain feature or determining line of sight between fighters.
> This model is useful for tactical analysis and can help in understanding the impact of terrain on gameplay.

> Optional: Add (Position) node if you want an X/Y/Z abstraction:
```cypher
MERGE (:Position {
  x: 10,
  y: 20,
  z: 0
});
```

### Complex Queries and Path Traversals
> With this model, you can perform complex queries to analyze game states, actions, and conditions over time. For example, you can find all fighters who were pinned during a specific turn or analyze the sequence of actions taken by a player in a match.

Fighters within range of a terrain element:
```cypher
MATCH (f:Fighter)-[:OCCUPIES]->(pos1:Position),
      (e:Equipment)-[:USED_IN]->(:GameState),
      (t:Terrain)-[:HAS_POSITION]->(pos2:Position)
WHERE distance(pos1, pos2) <= 6
RETURN f.name, t.name;

```


Sequence of actions for a fighter across turns:
```cypher

