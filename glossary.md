# Glossary

**Graph Projection** -  In-memory representation of a graph from a graph database

`gds.graph`: Enables graph projection work
    - `project`: project the graph in the Graph Catalog
    - `drop`: drops the graph projection

`gds.{centrality algo}`: Creates a centrality ranking across the various nodes depending on the type of algo
    - `degree`: Degree Centrality
    - `pageRank`: PageRank Centrality
    - `closeness`: Closeness Centrality
    - `articleRank`: ArticleRank Centrality

