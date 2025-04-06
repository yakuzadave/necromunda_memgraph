# Tracking Turns with Ordered Relationships

```cypher
MERGE (:Turn {number: 1})-[:NEXT]->(:Turn {number: 2});
MERGE (:Turn {number: 2})-[:NEXT]->(:Turn {number: 3});
MERGE (:Turn {number: 3})-[:NEXT]->(:Turn {number: 4});
```
```cypher
MATCH (f:Fighter {name: 'John Doe'}), (t:Turn {number: 2})
MERGE (f)-[:ACTIVATED_IN]->(t);
```
We can also track the order of actions within a turn using a similar approach. This allows us to analyze the sequence of events and their impact on the game state.

```cypher
MATCH (f:Fighter {name: 'John Doe'}), (a:Action {type: 'Shoot', turn: 2})
MERGE (f)-[:PERFORMED]->(a);
```
```cypher
MATCH (a:Action {type: 'Shoot', turn: 2}), (t:Turn {number: 2})
MERGE (a)-[:OCCURRED_IN]->(t);
```
```cypher
MATCH (a:Action {type: 'Shoot', turn: 2}), (f:Fighter {name: 'Enemy A'})
MERGE (a)-[:TARGETED]->(f);
```
```cypher
MATCH (a:Action {type: 'Shoot', turn: 2}), (f:Fighter {name: 'John Doe'})
MERGE (f)-[:ACTED]->(a);
```

