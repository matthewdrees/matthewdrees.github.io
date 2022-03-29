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

This design pattern looks like something you have to do all the time. Very useful.

### 3: Feature Cross
Feature engineering by making combinations of input values separate features. This allows you to use a simpler (i.e. Linear) model.

Simple contrived example is binary classifier with data that is in opposite quadrants of 2-dimensional graph. More complex example is New York taxi ride data, that turns pickup timestamps into hour intervals for hour/day (7*24 = 168 dimension 1 hot encoded vector). It's recommended to pass "bucketized" feature crosses like this through an Embedding to reduce the dimensionality.

It is recommended to pass a feature cross through an L1 or L2 regularization. Don't cross highly-correlated features.

### 4: Multimodal Input
Represent different types of data by concatenating all available data representations. Bag of Words (BOW) vs embeddings for text representation. Images can be individual pixels or a tiled structure like convolutional neural networks.

### 5: Reframing
Change the representation of the output of a machine learning problem, such as regression to classification.

The example given was "male babies born to 25 year old mothers at 38 weeks" from the natality dataset returned a normal distribution around 7.5 pounds. As a regression problem the error is a standard deviation. If we reframe this as a classification model with weight ranges we get something more like a probability density function (PDF).

They gave an example that works in the opposite way, taking a classification model for movie recommendations and turning it into a regression model, but I couldn't make sense of it. Another possibility would be to use latitude/longitude instead of cities. It can also be useful to use the regression model as an input to a multitask learning model. My takeaway here is usually you will reframe regression models into classification models.

If your output distribution is "sharp", you should stick with a regression.

If you want less error in your classification, you need more buckets.

Instead of changing between regression and classification, do both and use a multitask learning model.

Interesting things learned: Tweedie distribution and Sigmoid function.

### Notes on running *.ipynb files on google colab
None of the design pattern *.pynb files ran "out of the box" for me.

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

Mounting my google drive also solved authentication problems when running bq.query() calls.

BigQuery Client calls required adding the project name:

    bq = bigquery.Client(project='project-name-number')
    %%bigquery --project project-name-number

By default the notebooks run without a GPU. In hindsight I didn't need it though. Most calls run in < 10 seconds. FWIW: Edit -> Notebook settings -> Hardware Accelerator to enable GPU. 

#### DP3, feature_cross.ipynb notes
This line ran for 29 minutes before I stopped it:

    CREATE OR REPLACE MODEL babyweight.natality_model ...

There isn't a progress bar or logging that I'm aware of in the notebook or on google console. Here's what I did:
* Moved the query from jupyter notebook over to a google console BigQuery query. It said there was a syntax issue in the query. When I removed the backtick quotes from 'babyweight.natality_model' and ran again it went away. This didn't turn out to be a problem on colab though.
* I don't know if this ultimately made a difference in execution time but I moved the cutoff year from 2000 to 2006, which reduced the database from 33 billion rows to 8 million.
* After that the query took 1 hour, 52 minutes! But it seemed to work. The df.head() call in the next code block gave reasonable rmse values.
* Shortly after this I hit a 403 error data usage quota. I went ahead and signed up for billing and $300 credit from google console and I was able to keep going. (They got me!) Note that enabling GPU didn't appear to help speed up this call, it warned that the GPU wasn't being used so I killed it after a couple of minutes. I didn't let it run so don't know for sure if it eventually helped.
* Running the Ny taxi data with an L2 regression the RMSE got slightly worse than without!

#### DP4, mixed_representation.ipynb notes

Bug on this line. "tiled_input" should be "tiled_layer"?

    mixed_image_tabular_model = Model(inputs=[image_tabular_input, tiled_input], outputs=merged_image_output)
