# Graph Algorithms

## Algorithm Tiers and Execution Tiers

### Tiers of Algorithms
**Alpha**: Experimental and might be changed or removed at any time. 
**Beta**: Candidate for production quality tier
**Production**: Tested and Stable algorithms

### Execution Modes

`stream`: Returns the result of the algorithm as a stream of records

`stats`: Returns a single record of summary stats but does not write or modify any data

`mutate`: Writes the results to the in-memory graph projection and a single record of summary stats

`write`: Write the results of the algorithm back to the Neo4J database 

Use the `estimate` command to estimate how much memory will be needed to execute the algorithms.

Usually the syntax for any algorithm will be:

```
CALL gds[.<tier>].<algorithm>.<execution-mode>[.<estimate>](
	graphName: STRING,
	configuration: MAP
)
```

---
## Centrality and Importance
Centrality is usually used to find the importance of various nodes in regards to other nodes in the overall graph. Some examples include:

    - Degree Centrality: # of relationships a node has

    - PageRank Centrality: measures the influence of nodes in a directed graph (the general idea is that more important nodes are more likely to have proportionally more incoming relationships).

    - Betweenness Centrality: Measures the extent to which a node stands between the other nodes in a graph

    - Eigenvector Centrality: Similiar to PageRank but only on the largest eigenvector and tends to strongly favor high degree nodes.

    - Article Rank: A variant of PageRank which assumes that relationships originating from low-degree nodes have a higher influence than relationships from high-degree nodes.

Degree Centrality Stream:

```
CALL gds.degree.stream( {projections} )
YIELD nodeId, score
RETURN
  gds.util.asNode(nodeId).{nodeProp} AS {nodePropAlias},
  score AS {scoreProp}
ORDER BY {scoreProp} DESC
```


---

## Path Finding

### Dijkstra Source-Target Shortest Path

This is the most common path finding algorithm in computing. Dijkstra supports weighted relationships to account for distance or another cost property when comparing paths. 

```
CALL gds.shortestPath.dijkstra.stream(
    'proj',
    {
        sourceNode:{sourceNode},
        TargetNode:{targetNode}
    }
)
```

### Other Path Finding Algorithms

Shortest path between two nodes:

    - A* Shortest Path: An extension of Dijkstra that uses a heuristic function to speed up computation.

    - Yen’s Algorithm Shortest Path: An extension of Dijkstra that allows you to find multiple, the top k, shortest paths.

Shortest path between a source node and multiple other target nodes:

    - Dijkstra Single-Source Shortest Path: Dijkstra implementation for shortest path between one source and multiple targets.

    - Delta-Stepping Single-Source Shortest Path: Parallelized shortest path computation. Computes faster than Dijkstra single-source shortest Path but uses more memory.

General path search between a source node and multiple other target nodes:

    - Breadth First Search: Searches paths in order of increasing distance from the source node on each iteration.

    - Depth First Search: Searches as far as possible along a single multi-hop path on each iteration.



## Community Detection

Community detection algorithms are used to evaluate how groups of nodes may be clustered or partitioned in the graph. 

### Louvain Community Detection

Louvain maximizes a modularity score for each community by evaluating how much more densely connected the nodes within a community are compared to how connected they would be in a random network. Louvain optimizes the modularity with a hierarchial clustering approach.

Louvain is also a stochastic algorithm, meaning that it can have very random outcomes without a defined `seedProperty` or defined structure. Additionally, the Weakly Connected Components (WCC), take a more deterministic partitioning approach to contain better consistency.


**Other Community Detection Algorithms**
- Label Propagation: Similar intent as Louvain. Fast algorithm that parallelizes well. Great for large graphs.

- Weakly Connected Components (WCC): Partitions the graph into sets of connected nodes such that
    - Every node is reachable from any other node in the same set
    - No path exists between nodes from different sets

- Triangle Count: Counts the number of triangles for each node. Can be used to detect the cohesiveness of communities and stability of the graph.

- Local Clustering Coefficient: Computes the local clustering coefficient for each node in the graph which is an indicator for how the node clusters with its neighbors.

---

## Node Embeddings

Similar to finding similarity between node properties of nodes, node embeddings compute low-dimensional vector representations of nodes such that similarity between vectors.

Think of node embeddings as taking a graph database and mapping them to a lower dimensional space so then they can be represented as vectors. Once they are vectors, they can be used for cluster analysis. They are not meant to create insight themselves but used for enable or scaling other analytics. 
Some possibilities are:
- Exploratory Data Analysis
- Similiarity Measurements
- Machine Learning Training

### FastRP

**FastRP** stands for Fast Random Projection which levarages probablistic samplistic techniques to generate sparse representations which allow for faster calculation of embedding vectors in similiar quality as traditional random walks and NN techniques liek **Node2Vec** and **GraphSage**. 

There are two that are worth noting consideration:
- `embeddingDimension`: Controls the length of embedding vectors which is a tradeoff between dimensionality reduction and accuracy. Bigger dimensions = more accuracy, but takes longer to generate and produce embedding vectors.
Generally its best between 2^7 and 2^10 (e.g. 2^8 for 100K nodes). 
- `IterationWeights`: Controls the number of iterations for intermediate embeddings and their relative impact on the final node embedding. The parameter is a list of numbers, indicating one iteration per number where the number is the weight applied to that iteration. the default is [0.0, 1.0, 1.0].

---
## Similarity