# Machine Learning Pipeline for Necromunda Data from Memgraph

## Objective:
Use graph features and embeddings from Memgraph to train predictive ML models for tasks like:

- Match outcome prediction

- Gang composition optimization

- Skill synergy analysis

## Step 1: Data Preparation

### Install Dependencies
```bash
pip install pandas numpy scikit-learn neo4j
```
> I added the `neo4j` package to the dependencies list because `gqlalchemy` was giving me trouble because of `mgclient`. This is necessary for connecting to the database and querying data.

### Example Query for the Data
```cypher
    MATCH (f:Fighter)-[:BELONGS_TO]->(g:Gang)
    OPTIONAL MATCH (f)-[:HAS_SKILL]->(s:Skill)
    OPTIONAL MATCH (f)-[:USES]->(e:Equipment)
    RETURN f.name AS name, f.ws AS ws, f.bs AS bs, f.move AS move,
           g.name AS gang, COLLECT(DISTINCT s.name) AS skills,
           COLLECT(DISTINCT e.name) AS equipment
```
> This query retrieves the fighter's name, weapon skill (ws), ballistic skill (bs), movement (move), gang affiliation, and lists of skills and equipment. You can modify this query to include other relevant features for your analysis.
> The `COLLECT` function is used to aggregate multiple values into a list, which is useful for skills and equipment since a fighter can have multiple of each.
> You can also add other features like injuries, conditions, and actions by modifying the query accordingly.
> Make sure to adjust the query based on your specific data model and requirements.
> The query assumes that the `Fighter`, `Gang`, `Skill`, and `Equipment` nodes are connected through the specified relationships.

You can execute this query in the Memgraph Lab or through a Python script using the `neo4j` package.  This next step assumes that you have the data exported to a CSV file called `fighters.csv`.

```python
import pandas as pd
df = pd.read_csv('fighters.csv')
print(df.head())
```
> This will print the first few rows of the DataFrame, allowing you to verify that the data has been loaded correctly.
> You can also use the `df.info()` method to get a summary of the DataFrame, including the number of rows and columns, data types, and memory usage.
> This can help you identify any potential issues with the data, such as missing values or incorrect data types.

> You can also use the `df.describe()` method to get a statistical summary of the numerical columns in the DataFrame, which can help you understand the distribution of the data and identify any potential outliers.

## Step 2: Feature Engineering
Transform skills and equipment to multi-hot vectors
```python
from sklearn.preprocessing import MultiLabelBinarizer

mlb = MultiLabelBinarizer()
skill_features = mlb.fit_transform(df['skills'])
equipment_features = mlb.fit_transform(df['equipment'])

X_base = df[['ws', 'bs', 'move']].fillna(0)
X = pd.concat([X_base, pd.DataFrame(skill_features), pd.DataFrame(equipment_features)], axis=1)

# Fake target variable: did the gang win?
df['won'] = (df['gang'] == 'Iron Fists')  # example only
y = df['won'].astype(int)

```
> This code uses the `MultiLabelBinarizer` from `sklearn` to transform the skills and equipment columns into multi-hot vectors. The `fit_transform` method is used to fit the binarizer to the data and transform it into a binary matrix.

## Step 3: Train a Classifier
```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

clf = RandomForestClassifier()
clf.fit(X_train, y_train)

print(classification_report(y_test, clf.predict(X_test)))

```

> This code uses a `RandomForestClassifier` from `sklearn` to train a model on the features and target variable. The `train_test_split` function is used to split the data into training and testing sets, and the `classification_report` function is used to print the classification report for the model's performance on the test set.
> You can also use other classifiers from `sklearn` or even deep learning models from libraries like `TensorFlow` or `PyTorch` depending on your requirements and the complexity of the data.
> You can also use cross-validation to evaluate the model's performance more robustly. The `cross_val_score` function from `sklearn` can be used for this purpose.


## Step 4: Store Predictions Back into Memgraph
```python
from neo4j import GraphDatabase
predictions = clf.predict(X)
df['predicted_won'] = predictions

for _, row in df.iterrows():
    query = """
    MATCH (f:Fighter {name: $name})
    SET f.predicted_won = $predicted_won
    """
    with GraphDatabase.driver("bolt://localhost:7687", auth=(f"{memgraph_user}", f"{memgraph_password}")) as driver:
        with driver.session() as session:
            session.run(query, name=row['name'], predicted_won=row['predicted_won'])
```
> This code connects to the Memgraph database and updates the `predicted_won` property for each fighter based on the predictions made by the classifier. Make sure to replace `memgraph_user` and `memgraph_password` with your actual Memgraph credentials (I generally load this in a `.env` file).
> You can also use the `MERGE` statement instead of `MATCH` if you want to create the property if it doesn't exist. This can be useful if you're running this code multiple times and want to ensure that the property is always updated.
> You can also use the `UNWIND` statement to update multiple nodes in a single query, which can be more efficient than running a separate query for each node. Here's an example of how to do this:

```cypher
UNWIND $fighters AS fighter
MATCH (f:Fighter {name: fighter.name})
SET f.predicted_won = fighter.predicted_won
```
> This will update all the fighters in a single query, which can be more efficient than running a separate query for each fighter. You can pass the list of fighters as a parameter to the query.