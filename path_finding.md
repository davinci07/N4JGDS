# Path Finding in Neo4J

## Shortest Paths with Cypher

### Unweighted Shortest Path
In an _unweighted path_ the traversal of each relationship has an identical cost. The _shortestPath()_ function expects a definition of a Cypher path between an origin and destination node. The traversal is breadth first, meaning that all relationships from a node will be followed before traversing further through the graph. 

Example:

Single-Relationship Type
```
MATCH p = shortestPath({source}-[:Relationship*{1...n}]=>{target})

RETURN p
```

Multiple Relationship Types
```
MATCH p = shortestPath({source}-[:Relationship1|Relationshipt2|RelationshipN*{1...n}]=>{target})

RETURN p
```
---

### Weighted Shortest Paths

Weighted Shortest paths not only find the shortest paths but also find the most effective paths based on edge properties.

**Dijkstra Source-Target Shortest Path**

Dijkstra’s algorithm supports weighted graphs with positive relationship weights only. If you have any negative weights, the algorithm will not work correctly.

Available by `gds.shortestPath.dijkstra.stream()`

**Yen's k-Shortest Path Algorithm**

Similar to Dijkstra but with the exception of a configurable `k` where `k` is the number of shortest paths to return.

Available by `gds.shortestPath.yens.stream()`