# Machine Learning with GDS

## ML Pipelines and Models
Currently, there are two ML pipelines:
- Node Classification Pipelines: Supervised binary and multi-class classification for nodes

- Link Prediction Pipelines: Supervised prediction for whether a relationship or "link" should exist between pairs of nodes

These pipelines have a `train` procedure which makes a trained model object which then have a `predict` procedure.

## Node Classification Pattern in GDS

Generally for Node Classification, this follows the standard ML pattern with a few changes:

1. Generate a Graph Projection with Node Properties
2. Select features
3. Split the nodes into a training set and a sparse test set
4. Train and Evaluate Model Candidates with K-Fold CV
5. Select the best performing model
6. Retrain on whole training set, evaluate on test set 
7. Register in Model Catalog and apply model to classify new nodes.

The configuration steps are as follows. Technically they need not be configured in order, though it helps to do so to make things easy to follow.

1. Create the Pipeline
2. Add Node Properties
3. Select Node Properties as Features
4. Configure Node Splits
5. Add Model Candidates

---
## Link Prediction Pattern

GDS currently offers a binary classifier for links which works well for undirected graphs.

Here is the standard procedure for Link Prediction in GDS:

1. Split Relationships with negative examples in Training and Test sets but not in Feature-Input Sets
2. Add Node Properties
3. Engineer Link Features for Training Set
4. Train and Evaluate Model Candidates using K-Fold Cross-Validation
5. Select best performing model
6. Engineer link features for test set
7. Retrain on Whole Training set, evaluate on test and register in catalog.

There is an additional `feature-input` set in the relationship splits which now comes before node property and feature generation steps. In short, this is to handle data leakage issues, whereby model features are calculated using the relationships you are trying to predict







































