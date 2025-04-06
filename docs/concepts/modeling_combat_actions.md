# Modeling Combat Actions

In Necromunda, combat actions such as shooting or charging can be tracked explicitly as `Action` nodes. This allows for more granular event analysis and future ML training.

```cypher
MERGE (:Action {
  type: 'Shoot',
  weapon_used: 'Lasgun',
  result: 'Hit',
  damage_dealt: 1,
  turn: 2
});
```

```cypher
MERGE (:Action {
  type: 'Charge',
  weapon_used: 'Combat Knife',
  result: 'Miss',
  damage_dealt: 0,
  turn: 3
});
```

> Link the Action to the acting and affected Fighter:

```cypher
MATCH (attacker:Fighter {name: 'John Doe'}),
      (target:Fighter {name: 'Enemy A'}),
      (a:Action {type: 'Shoot', turn: 2})
MERGE (attacker)-[:PERFORMED]->(a)
MERGE (a)-[:TARGETED]->(target);
```


