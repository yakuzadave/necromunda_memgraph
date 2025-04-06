# Tracking Conditions
You can model game-affecting states (e.g., Pinned, Seriously Injured) as either properties or separate Condition nodes.

Option A: Simple Boolean Properties

```cypher
SET f.is_pinned = true
SET f.is_seriously_injured = true
```

Option B: Linked `Condition` Nodes

```cypher
MERGE (:Condition {
  name: 'Pinned',
  effect: 'Fighter cannot take actions',
  duration: '1 turn'
});

MATCH (f:Fighter {name: 'John Doe'}), (c:Condition {name: 'Pinned'})
MERGE (f)-[:HAS_CONDITION]->(c);
```

