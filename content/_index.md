---
title: "Necromunda Memgraph Documentation"
---

# Necromunda Graph Database & ML Project

Welcome to the documentation for the Necromunda Graph Database and Machine Learning project.

## What is this project?

This project combines graph database technology with machine learning to:
- Model Necromunda game entities and relationships
- Track game states, turns, and combat actions
- Apply machine learning for predictive analytics
- Simulate AI opponents using reinforcement learning

## Getting Started

Choose a section from the menu to explore:

- **Concepts**: Core ideas behind modeling Necromunda in a graph database
- **Advanced Options**: Advanced techniques for game state modeling and embeddings
- **Machine Learning**: ML pipelines and AI opponent simulation

## Database Schema Structure

The database contains structured information about:

- Fighters, skills, and equipment
- Gangs and territories
- Campaigns and scenarios
- Game mechanics and actions

See [Optimizing Memgraph Schema for Necromunda Data](/docs/machine_learning_with_necromunda/optimizing-memgraph-schema-necromunda-data/) for detailed schema information.

## Key Features

- **Graph Database Schema**: Comprehensive modeling of Necromunda game elements
- **Machine Learning Integration**: Predictive analytics and strategy optimization
- **Game State Tracking**: Both snapshot and dynamic approaches
- **Combat Simulation**: Detailed action and injury modeling

## Example Cypher Query

```cypher
MATCH (f:Fighter)-[:BELONGS_TO]->(g:Gang)
OPTIONAL MATCH (f)-[:HAS_SKILL]->(s:Skill)
OPTIONAL MATCH (f)-[:USES]->(e:Equipment)
RETURN f.name AS name, g.name AS gang, 
       COLLECT(DISTINCT s.name) AS skills,
       COLLECT(DISTINCT e.name) AS equipment
```
