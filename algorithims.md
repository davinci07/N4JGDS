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

Louvain maximizes a modularity score for each community by evaluating how much more densely connected the nodes within a community are compared to how connected they would be in a random network. Louvain optimizes the modularity with a hierarchial clustering approach. TEST
