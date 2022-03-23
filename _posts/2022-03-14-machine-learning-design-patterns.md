---
layout: post
title:  "Book Notes: Machine Learning Design Patterns"
category: book
---

![Book cover](/assets/machine-learning-design-patterns.jpg)

By Valliappa Lakshmanan, Sara Robinson, Michael Munn.

Book club book with my sister.

Chapter 1, The Need for Machine Learning Design Patterns. Machine learning has it's own jargon. There are dozens of italicized words to learn.

### 1: Hashed Feature
Reduce the cardinality of your input data by hashing them into bins. Theoretically useful if you have incomplete vocabulary, high cardinality, or cold start issues. The example given was measuring flight delays by airport codes. There are a lot of airports (high cardinality), some of which might not be in your training data (incomplete vocabulary), and new airports may come online (cold start). However, you may randomly lump high-use airports with low-use one, making your model less interesting. We struggled to think of a use case where you would want random bins. Therefore we've renamed this pattern to "Dumb Embeddings".

Note on hashed_feature.ipynb... It doesn't run out of the box. I had to add lines to authenticate_user, create a BigQuery project in google console, and add that project name to the bigquery call with --project.

### 2: Embeddings
Map high-cardinality input data into a lower-dimensional representation. Good rules of thumb for reduction are the 4th root(N) through 1.6 * sqrt(N). Example uses TensorFlow.keras.layers.Embedding(output_dim = 2) to reduce 6x6 natality dataset to 6x2 dense vector. The secret is that the 6x6 matrix variables aren't fully independent.

A text embedding example using nytimes headlines reduced a 96k x 27 input matrix to 96k x 10. Also interesting is it used a reduce_mean lambda layer as well as a 

Note on embeddings.ipynb... had to put the "data" folder on my google drive, then mount it, and update paths to "./drive/MyDrive/data/..".

This design pattern looks like something you have to do all the time. Very useful.

### 3: Feature Cross
Feature engineering by making combinations of input values separate features. This allows you to use a simpler (i.e. Linear) model.

Simple contrived example is binary classifier with data that is in opposite quadrants of 2-dimensional graph. More complex example is New York taxi ride data, that turns pickup timestamps into hour intervals for hour/day (7*24 = 168 dimension 1 hot encoded vector). It's recommended to pass "bucketized" feature crosses like this through an Embedding to reduce the dimensionality.

It is recommended to pass a feature cross through an L1 or L2 regularization.

