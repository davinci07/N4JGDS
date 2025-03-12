# Neo4J Graph Data Science Overiview

## Installation

**How to Install Graph Data Science Library**

1. Install Neo4J Desktop
2. On a given database, install the Graph Data Science Library as a plugin of the given database

- Community and Enterprise both have access to all GDS algorithims and machine learning models

- Enteprise-only features for increased perfomance include unlimited concurrency and low-memory analytics graph format

- Enterprise features for security and workflow in production for fine-grained security

- If the procedure allowlist is configured, make sure to also include procedures from the GDS library

## Basis (How GDS Works)

- GDS works by transforming and loading data into an in-memory format. 

### Workflow
- Neo4J Database is loaded and transformed into an in-memory graph (**Graph Projection**) via the Graph Loader (Projecting the Graph. It can hold multiple graph projections at once and managed by the **Graph Catalog**

- Algorithms such as Centrality, community detection, path finding, embeddings, etc include ML pipelines

- GDS configs run greedily (Max CPU/GPU + memory resources as needed until configured limits)

- GDS uses parallelization for graph projections, algos, and generating resources (Community is up to level 4 parallelization which means each task is spread out to each core)

### Memory 

- GDS runs on the JVM (Java Virtual Machine) memory management which is in memory management (Heap and Off-Heap)
    - **Off-Heap**: Page-Cache, Direct Memory, JVM Internals
    - **Heap**: Maximum Heap, Initial Heap

- Two main types can be allocated in configuration:
    - Page Cache: used for indexes, caching Neo4J data stored on disk. Don't use this one as often since this is just the information and not the actual computation. Minimum is `8KB * 100 * readConcurrency`
    `dbms.memory.pagecache.size`

    - Heap Space: in-memory graphs, executing GDS algos, query execution, transaction state. Best to be generous on this one. `dbms.memory.heap.initial_size and dbms.memory.heap.max_size`

- Recommended to use **Memory Estimation** to find out the heap size. 

---
## Graph Management

### Graph Catalog

**Graph Catalog** lets you interact with Neo4J in a similar vain as using the standard Neo4J database

### Streaming and Writing Node Properties
The graph catalog has methods to stream and write both node properties and relationships. Using the `nodeProperty.stream` graph catalog operation. We can also write the operation (in this case, the degree centrality) as a property to the graph by `nodeProperties.write`. nodeProperties.write
 
### Exporting Graphs 
After doing graph projections, one may want to do bulk data export. The graph catalog can export into into a Neo4J database by `gds.graph.export` or to a CSV by `gds.beta.graph.export.csv`

### Native Projections
In Neo4J there is **native projections** and Cypher projections. These are projections that read directly from the Neo4j store files. 

Here is the basic syntax of the projection procedure:

```
CALL gds.graph.project(
  graphName: String,
  nodeProjection: String or List or Map,
  relationshipProjection: String or List or Map,
  configuration: Map
)
```
 ### Changing Relationship Orientation
To accommodate this there are three orientation options we can apply to relationship types in the relationshipProjection:

NATURAL: same direction as in the database (default)

REVERSE: opposite direction as in the database

UNDIRECTED: undirected

