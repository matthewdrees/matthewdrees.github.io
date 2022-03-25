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
Map high-cardinality input data into a lower-dimensional representation. Good rules of thumb for reduction are the 4th root(N) through 1.6 * sqrt(N). Example uses TensorFlow.keras.layers.Embedding(output_dim = 2) to reduce 6x6 natality dataset to 6x2 dense vector. The secret is that the 6x6 matrix variables aren't fully independent. The lower-dimensional representation is interesting because it shows relationships.

A text embedding example using nytimes, github, and techcrunch headlines reduced a 47k x 27 input matrix (number of unique words x max headline word length) to 47k x 10 dense(r) matrix. Interestingly, it did some further processing with a reduce_mean lambda layer and represented in a dense softmax layer.


Note on embeddings.ipynb... had to put the "data" folder on my google drive, then mount it, and update paths to "./drive/MyDrive/data/..".

This design pattern looks like something you have to do all the time. Very useful.

### 3: Feature Cross
Feature engineering by making combinations of input values separate features. This allows you to use a simpler (i.e. Linear) model.

Simple contrived example is binary classifier with data that is in opposite quadrants of 2-dimensional graph. More complex example is New York taxi ride data, that turns pickup timestamps into hour intervals for hour/day (7*24 = 168 dimension 1 hot encoded vector). It's recommended to pass "bucketized" feature crosses like this through an Embedding to reduce the dimensionality.

It is recommended to pass a feature cross through an L1 or L2 regularization. Don't cross highly-correlated features.

### 4: Multimodal Input
Represent different types of data by concatenating all available data representations. Bag of Words (BOW) vs embeddings for text representation. Images can be individual pixels or a tiled structure like convolutional neural networks.


### Running *.ipynb files
None of the *.pynb files ran "out of the box" for me.

Always had to add this:

    from google.colab import auth
    auth.authenticate_user()

If using local data I copied it to my google drive and mounted it...

    from google.colab import drive
    drive.mount('/content/drive')

... and tested the path...

    ls drive/MyDrive/data
    babyweight_eval.csv    babyweight_train.csv babyweight_sample.csv  titles_full.csv

... and added "drive/MyDrive/" to paths for opening the files.

BigQuery required creating a BigQuery project on Google Console and adding the project name to bigquery calls:

    bq = bigquery.Client(project='project-name-344319')

    %%bigquery --project project-name-344319

By default the notebooks run without a GPU. :-(.  Edit -> Notebook settings -> Hardware Accelerator to enable GPU.

#### DP3, feature_cross.ipynb
This line ran for 29 minutes before I stopped it:

    CREATE OR REPLACE MODEL babyweight.natality_model ...

There isn't a progress bar or logging that I'm aware of in the notebook or on google console. Here's what I did:
* Moved the query from jupyter notebook over to a google console BigQuery query. It said there was a syntax issue in the query. When I removed the backtick quotes from 'babyweight.natality_model' and ran again it went away.
* I don't know if this ultimately made a difference in execution time but I moved the cutoff year from 2000 to 2006, which reduced the database from 33 billion rows to 8 million.
* After that the query took 1 hour, 52 minutes! But it seemed to work. The df.head() call in the next code block gave reasonable rmse values.
* Shortly after this I hit a 403 error data usage quota. I went ahead and signed up for billing and $300 credit from google console and I was able to keep going. (They got me!)
* Running the Ny taxi data with an L2 regression the RMSE got slightly worse than without!

#### DP4, mixed_representation.ipynb

Bug on this line. "tiled_input" should be "tiled_layer"?
    mixed_image_tabular_model = Model(inputs=[image_tabular_input, tiled_input], outputs=merged_image_output)
