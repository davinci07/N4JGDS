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


### Configure the Pipeline
The configuration steps are as follows. Technically they need not be configured in order, though it helps to do so to make things easy to follow.

1. Create the Pipeline
2. Add Node Properties
3. Select Node Properties as Features
4. Configure Node Splits
5. Add Model Candidates

### Train the Pipeline

The following command will train the pipeline. This process will:

1. Apply node and relationship filters
2. Execute the above pipeline configuration steps
3. Train with cross-validation for all the candidate models
4. Select the best candidate according to the average precision-recall, a.k.a. AUCPR.
5. Retrain the winning model on the entire training set and do a final evaluation on the test with AUCPR
6. Register the winning model in the model catalog

```
CALL gds.beta.pipeline.linkPrediction.train('proj', {
    pipeline: 'pipe',
    modelName: 'lp-pipeline-model',
    targetRelationshipType: 'ACTED_WITH',
    randomSeed: 7474 //usually a good idea to set a random seed for reproducibility.
}) YIELD modelInfo
RETURN
modelInfo.bestParameters AS winningModel,
modelInfo.metrics.AUCPR.train.avg AS avgTrainScore,
modelInfo.metrics.AUCPR.outerTrain AS outerTrainScore,
modelInfo.metrics.AUCPR.test AS testScore
```

### Predict the Model
Once the pipeline is trained we can use it to predict new links in the graph. The pipeline can be re-applied to data with the same schema. 

```
CALL gds.beta.pipeline.linkPrediction.predict.stream('proj', {
  modelName: 'lp-pipeline-model',
  sampleRate:0.1,
  topK:1,
  randomSeed: 7474,
  concurrency: 1
})
 YIELD node1, node2, probability
 RETURN gds.util.asNode(node1).name AS actor1, gds.util.asNode(node2).name AS actor2, probability
 ORDER BY probability DESC, actor1
```

This operation supports a mutate execution mode to save the predicted links in the graph projection. If you want to write back to the database you can use the mutate mode followed by the gds.graph.writeRelationship command





































