# Graph Embedding and Analysis Hooks
If using Memgraph’s MAGE module:

```cypher
CALL node2vec.get() YIELD node, embedding
RETURN node, embedding
```

Store the result back to the node:
    
```cypher
SET node.embedding = embedding

```

> These embeddings can be exported to Python for further analysis or machine learning tasks. For example, you can use libraries like TensorFlow or PyTorch to train models on these embeddings.

