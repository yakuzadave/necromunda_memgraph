# AI Opponent Simulation with Reinforcement Learning

## Objective:
Train a reinforcement learning agent to simulate tactical decision-making in Necromunda.

## Step 1: Represent Game State as Environment
Wrap GameState + Fighter status into an RL-compatible `env`.

```python
class NecroEnv(gym.Env):
    def __init__(self, neo4j_driver, fighter_id):
        self.driver = neo4j_driver
        self.fighter_id = fighter_id
        self.action_space = spaces.Discrete(5)  # Move, Shoot, Charge, Aim, Pass
        self.observation_space = spaces.Box(low=0, high=1, shape=(NUM_FEATURES,))
    
    def reset(self):
        # Reset Neo4j state
        with self.driver.session() as session:
            session.run("MATCH (a:Action {fighter_id: $id}) DETACH DELETE a", 
                       {"id": self.fighter_id})
        return self._get_observation()
    
    def step(self, action):
        # Write Action node to Neo4j and update fighter status
        with self.driver.session() as session:
            result = session.write_transaction(self._execute_action, action)
            obs = self._get_observation()
            reward = result.get("reward", 0)
            done = result.get("done", False)
        return obs, reward, done, {}
        
    def _get_observation(self):
        with self.driver.session() as session:
            return session.read_transaction(self._fetch_observation)
```

## Step 2: Encode Observations

Use:

- Nearby enemies

- Cover availability

- Weapon range

- Remaining wounds

- Action history

```python
def _fetch_observation(self, tx):
    result = tx.run("""
        MATCH (f:Fighter {name: $fighter})
        OPTIONAL MATCH (f)-[:USES]->(e:Equipment)
        OPTIONAL MATCH (f)-[:HAS_CONDITION]->(c:Condition)
        RETURN f.ws, f.bs, f.move, SIZE((f)-[:USES]->()) AS equipment_count,
               COUNT(DISTINCT c) AS condition_count
    """, fighter=self.fighter_id).single()

    return np.array([result["f.ws"], result["f.bs"], result["f.move"],
                    result["equipment_count"], result["condition_count"]])
```

## Step 3: Train an RL Agent
Use libraries like Stable Baselines3 or RLlib to train the agent.

```shell
pip install stable-baselines3
```


```python
from stable_baselines3 import PPO

env = NecroEnv(memgraph, fighter_id="John Doe")
model = PPO("MlpPolicy", env, verbose=1)
model.learn(total_timesteps=10000)
```

## Step 4: Deploy AI as Simulated Player
Use the trained model to simulate an opponent in a game.

```python
obs = env.reset()
done = False

while not done:
    action, _ = model.predict(obs)
    obs, reward, done, info = env.step(action)
    # Update game state in Memgraph

```

## Optional Enhancements

- Add graph-based reward shaping:

- e.g., reward fighter proximity to objectives or retreating when injured

- Use embeddings instead of handcrafted observations

- Include policy graphs to visualize the agent’s strategy over time