# Necromunda Graph Database & ML Project

A comprehensive project for modeling the tabletop game Necromunda using Memgraph (a graph database) and applying machine learning techniques for game analysis and opponent simulation.

## Overview

This project combines graph database technology with machine learning to:
- Model Necromunda game entities and relationships
- Track game states, turns, and combat actions
- Apply machine learning for predictive analytics
- Simulate AI opponents using reinforcement learning

## Repository Structure

```
docs/
├── advanced_options/
│   ├── embeddings_and_hooks.md
│   └── modeling_game_states.md
├── concepts/
│   ├── modeling_combat_actions.md
│   ├── structure_nodes_and_relationships.md
│   ├── tracking_game_state.md
│   └── tracking_turns.md
└── machine_learning_with_necromunda/
    ├── machine_learning_pipeline_necromunda.md
    └── opponent_simulation.md
queries/
```

## Key Components

### Graph Database Schema

The project uses Memgraph to create a rich graph representation of Necromunda gameplay:

- **Entities as Nodes:**
  - Fighters with stats, skills, and equipment
  - Gangs and their composition
  - Equipment, skills, territories, and armor
  - Game states, turns, and conditions

- **Game Dynamics:**
  - Tracking turns with ordered relationships
  - Modeling combat actions as explicit nodes
  - Tracking game states with conditions
  - Modeling complex game states (snapshot vs. dynamic approaches)

- **Schema Details:** See `structure_nodes_and_relationships.md` for comprehensive details on node properties and relationships

### Machine Learning Integration

- **ML Pipeline:** `machine_learning_pipeline_necromunda.md`
  - Data preparation from Memgraph
  - Feature engineering for tabletop game data
  - Training classifiers for match outcome prediction
  - Gang composition optimization

- **Opponent Simulation:** `opponent_simulation.md`
  - Reinforcement learning environment for Necromunda
  - Encoding game state as observations
  - Training models with Stable Baselines3
  - Deploying AI as simulated opponents

- **Advanced Analysis:** `embeddings_and_hooks.md`
  - Graph embeddings for entity representation
  - Integration with external ML tools

## Plan for Leveraging Memgraph with Machine Learning

### 1. Data Modeling and Preparation
- Use Memgraph to model Necromunda entities (fighters, gangs, equipment, etc.) and their relationships.
- Track game states dynamically or via snapshots for historical analysis.
- Use Cypher queries to extract relevant data for machine learning tasks.

### 2. Feature Engineering
- Transform graph data into ML-compatible formats using techniques like multi-hot encoding for skills and equipment.
- Generate graph embeddings using Memgraph's MAGE module (e.g., `node2vec`).
- Encode game states as RL-compatible environments for reinforcement learning.

### 3. Machine Learning Pipeline
- Train predictive models for tasks like match outcome prediction and gang optimization using scikit-learn or TensorFlow.
- Use reinforcement learning libraries (e.g., Stable Baselines3) to train AI opponents.
- Store predictions and learned policies back into Memgraph for visualization and further analysis.

### 4. Simulation and Analysis
- Simulate AI opponents in Necromunda matches using trained RL models.
- Analyze game dynamics and strategies using graph queries and embeddings.
- Visualize game states, actions, and outcomes using Memgraph Lab.

### 5. Deployment and Integration
- Deploy trained models as part of a game simulation framework.
- Use Memgraph's real-time capabilities to update and query game states during simulations.
- Integrate with external tools for advanced analytics and reporting.

## Detailed Implementation Roadmap

### Phase 1: Core Data Modeling and Game Mechanics
1. **Database Schema Development**
   - Define node types (Fighter, Gang, Equipment, etc.)
   - Define relationship types (BELONGS_TO, EQUIPPED_WITH, etc.)
   - Create Cypher queries for schema creation

2. **Game Rules Implementation**
   - Develop dice rolling mechanics (`necromunda_combat_demo.py`)
   - Implement combat resolution algorithms
   - Model skill and equipment effects

3. **Data Import Framework**
   - Create templates for fighter, gang, and equipment data
   - Develop import scripts for populating the database
   - Set up validation rules for data integrity

### Phase 2: Game State Tracking
1. **Turn Management System**
   - Implement turn tracking relationships
   - Develop activation sequence logic
   - Model action points and constraints

2. **Combat Action Modeling**
   - Create combat action nodes with parameters
   - Model action outcomes and dice rolls
   - Link actions to game state changes

3. **Game State Representation**
   - Implement both snapshot and dynamic state tracking
   - Develop queries for state transitions
   - Create visualization tools for game state

### Phase 3: Machine Learning Foundation 
1. **Data Pipeline Creation**
   - Develop extraction queries for ML-ready datasets
   - Create preprocessing functions for tabletop game data
   - Set up training/validation/test splits

2. **Feature Engineering Framework**
   - Implement encoding techniques for categorical game attributes
   - Develop graph embedding generation for Necromunda entities
   - Create feature importance analysis tools

3. **Basic Model Training**
   - Develop predictive models for combat outcomes
   - Train classifiers for tactical decision support
   - Implement model performance evaluation metrics

### Phase 4: Reinforcement Learning Environment
1. **Custom Gym Environment**
   - Create a Necromunda game environment compatible with OpenAI Gym
   - Implement state encoding and action space definition
   - Develop reward functions aligned with game objectives

2. **Agent Development**
   - Implement baseline agents (random, rule-based)
   - Train RL agents using Stable Baselines3 (PPO, A2C, SAC)
   - Develop agent evaluation metrics

3. **Simulation Framework**
   - Create a simulator for agent vs. agent matches
   - Implement game scenario generation
   - Develop performance tracking and analysis tools

### Phase 5: Integration and Application
1. **Applications Development**
   - Create a web interface for simulation results
   - Build tools for gang optimization
   - Develop campaign tracking applications

2. **Performance Optimization**
   - Optimize database queries for real-time use
   - Improve ML model efficiency for faster inference
   - Enhance simulation speed for bulk analysis

3. **Documentation and Deployment**
   - Create comprehensive API documentation
   - Develop deployment guides for local and cloud setups
   - Create user tutorials for common workflows

## Technical Requirements

### Software Dependencies
- **Memgraph DB** (v2.4+)
- **Python** (v3.9+)
- **Libraries:**
  ```
  ne04j==4.4.0
  pandas==1.5.3
  numpy==1.24.2
  scikit-learn==1.2.2
  tensorflow==2.11.0
  stable-baselines3==1.7.0
  gymnasium==0.27.1
  matplotlib==3.7.1
  networkx==3.0
  ```

### Hardware Recommendations
- **Development:** 16GB RAM, 4+ CPU cores
- **Training:** 32GB RAM, 8+ CPU cores, NVIDIA GPU (8GB+ VRAM)
- **Production:** Dedicated server with 64GB RAM, 16+ CPU cores

## Demo Scenarios

This project includes several demonstration scenarios for testing core mechanics:

- **Dice Rolling Demo:** Test the randomization mechanics
- **Ranged Combat Demo:** Simulate shooting actions between fighters
- **All Scenarios:** Run all available demos



## Getting Started

1. Install Memgraph DB (see [Memgraph documentation](https://memgraph.com/docs/))
2. Install Python dependencies:
   ```bash
   pip install pandas numpy scikit-learn neo4j stable-baselines3 gymnasium
   ```
3. Create graph schema using the Cypher queries in `structure_nodes_and_relationships.md`
4. Populate your database with fighter, equipment, and gang data
5. Explore the ML examples in the machine learning docs

## Use Cases

- Campaign tracking and analysis
- Historical battle reporting
- Gang optimization and strategy development
- AI opponent training and simulation
- Skill and equipment synergy analysis

## Contributing

Contributions, bug reports, and feature requests are welcome! See the issues section to contribute.

## License

MIT License