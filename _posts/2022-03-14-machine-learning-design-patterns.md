---
layout: post
title:  "Book Notes: Machine Learning Design Patterns"
category: book
---

![Book cover](/assets/machine-learning-design-patterns.jpg)

By Valliappa Lakshmanan, Sara Robinson, Michael Munn.

Book club book with my sister.

Chapter 1, The Need for Machine Learning Design Patterns. Machine learning has it's own jargon. There are dozens of italicized words to learn.

## 1: Hashed Feature

Reduce the cardinality of your input data by hashing them into bins. Theoretically useful if you have incomplete vocabulary, high cardinality, or cold start issues. The example given was measuring flight delays by airport codes. There are a lot of airports (high cardinality), some of which might not be in your training data (incomplete vocabulary), and new airports may come online (cold start). However, you may randomly lump high-use airports with low-use one, making your model less interesting. We struggled to think of a use case where you would want random bins. Therefore we've renamed this pattern to "Dumb Embeddings".

Note on hashed_feature.ipynb... It doesn't run out of the box. I had to add lines to authenticate_user, create a BigQuery project in google console, and add that project name to the bigquery call with --project.

## 2: Embeddings

Map high-cardinality input data into a lower-dimensional representation. Good rules of thumb for reduction are the 4th root(N) through 1.6 * sqrt(N). Example uses TensorFlow.keras.layers. Embedding(output_dim = 2) to reduce 6x6 natality dataset to 6x2 dense vector. The secret is that the 6x6 matrix variables aren't fully independent. The lower-dimensional representation is interesting because it shows relationships.

A text embedding example using nytimes, github, and techcrunch headlines reduced a 47k x 27 input matrix (number of unique words x max headline word length) to 47k x 10 dense(r) matrix. Interestingly, it did some further processing with a reduce_mean lambda layer and represented in a dense softmax layer.

This design pattern looks like something you have to do all the time. Very useful.

## 3: Feature Cross

Feature engineering by making combinations of input values separate features. This allows you to use a simpler (i.e. Linear) model.

Simple contrived example is binary classifier with data that is in opposite quadrants of 2-dimensional graph. More complex example is New York taxi ride data, that turns pickup timestamps into hour intervals for hour/day (7*24 = 168 dimension 1 hot encoded vector). It's recommended to pass "bucketized" feature crosses like this through an Embedding to reduce the dimensionality.

It is recommended to pass a feature cross through an L1 or L2 regularization. Don't cross highly-correlated features.

## 4: Multimodal Input

Represent different types of data by concatenating all available data representations. Bag of Words (BOW) vs embeddings for text representation. Images can be individual pixels or a tiled structure like convolutional neural networks.

## 5: Reframing

Change the representation of the output of a machine learning problem, such as regression to classification.

The example given was "male babies born to 25 year old mothers at 38 weeks" from the natality dataset returned a normal distribution around 7.5 pounds. As a regression problem the error is a standard deviation. If we reframe this as a classification model with weight ranges we get something more like a probability density function (PDF).

They gave an example that works in the opposite way, taking a classification model for movie recommendations and turning it into a regression model, but I couldn't make sense of it. Another possibility would be to use latitude/longitude instead of cities. It can also be useful to use the regression model as an input to a multitask learning model. My takeaway here is usually you will reframe regression models into classification models.

If your output distribution is "sharp", you should stick with a regression.

If you want less error in your classification, you need more buckets.

Instead of changing between regression and classification, do both and use a multitask learning model.

Interesting things learned: Tweedie distribution and Sigmoid function.

## 6: Multilabel

If your data can have more than 1 label (e.g. an image can have a cat and a dog), use a sigmoid function instead of softmax.

For a binary classifier, use sigmoid with size 1 instead of softmax with size 2 as an optimization.

For sigmoid functions use a binary crossentropy loss function.

Need a threshold for processing sigmoid results. Rule of thumb is number of specific tags / number of total examples. Should also explore S-cut or F-measure.

Try to have training data with an equal number of labels and combinations of labels.

Optionally could use several individual binary classifier models rather than one multilabel model.

Something else that occurred to me... "class" and "label" mean nearly the same thing, but are used in different contexts.

## 7: Ensembles

Money quote: "No machine learning model is perfect" (p100)

The main ML model error parts:
1. Irreducible error: noise in the dataset. (e.g. measurement errors or bad training data)
2. Bias: inability to figure out relationship between features and labels. High bias -> *underfit*.
3. Variance: can't handle new, unseen data. High variance -> *overfit*.

Per our book club discussion: It seems like *underfit* vs *overfit* could be a single value instead of bias and variance, especially since these words are used in many other contexts anyways.

Solutions:
1. Bagging: (bootstrap aggregating). Train several models of the same type in parallel, and aggregate their outputs. Useful for reducing variance (overfitting). Good example is random forrest.
2. Boosting: Use iterative models in series that have more "capacity" than the individual models. Reduces bias (underfitting).
3. Stacking: Feed the output of a collection of models into another model. Think of this as fancier version of model averaging. Netflix Prize winner did this.

Downsides: Increased complexity & design time. Harder to explain how the whole thing works.

## 8: Cascade

Cascade is a specific case of the Ensemble pattern where you have a variable that has a normal case and a rare but much different case.

Downside in addition to the Ensemble downsides is that your initial classifier won't be perfect.

Much of this design pattern is spent warning you against using it.

## 9: Neutral Class

Short and sweet! In a classifier have a distinct "maybe" class. Useful for cases where experts disagree (example given is doctors prescribing ibuprofen vs acetaminophen) or gray areas like 5-7 range on customer satisfaction scores.

Also learned about "labelling services".

## 10: Rebalancing

This was a big one, though I'm not sure how you would break it up into separate design patterns. What to do if you have an labelling imbalance in your data? This assumes you have enough data in the first place.

Choosing an evaluation metric:
* Accuracy might not be acceptable. E.g. assuming 99% of transactions aren't fraud, a model that doesn't detect any fraud is 99% accurate.
* Precision: correct positive classifications / all positive classifications.
* Recall: correct positive classifications / actual positive classifications.
* F-measure: 0-1 description of accuracy combining precision and recall.

Introduces the Confusion Matrix.

Downsampling removes abundantly labelled data. Upsampling creates new data from existing data. SMOTE combines both. Weighted classes uses weights to account for different ratios of labelled data.

For regression models you can use "errors as a signal" or clustering data for anomaly detection. You can use clustering for unsupervised learning to create labelled data for input to another model.

There are some great tips in the "combining different techniques" and "choosing a model" sections.

Importance of explainability: SHAP, "What-if Tool", etc, explains the impacts of various inputs on the output.

## 11: Useful Overfitting

I really liked this one. Usually you want to avoid overfitting, but sometimes not! In cases like a mathematical equations (they used partial differential equation solution as an example) it may be more efficient to train a ML model than use numerical methods like Runge-Kutta. Think of it like a lookup table with unwieldy amount of input data, the ML model can interpolate and run faster. This only works if you have no unseen data.

Monte Carlo methods. The danger of overfitting in this case comes from "model complexity", not noise. Use a smaller model.

Distilling knowledge of neural networks. A larger model can be easier to train, even though a smaller model is capable. Train a larger model first, and then transfer that knowledge by overfitting to a smaller model.

Overfitting a batch. A model should be capable of overfitting. Try to overfit on purpose as a sanity check that the model can work.

Other stuff learned:
* Universal Approximation Theorem. A ML model is capable of representing a "wide variety of interesting functions when given appropriate weights".
* Keras training loop: compile, fit, evaluate.
* Stochastic gradient descent (SGD).
* Deep Galerkin Method: ML for solving partial differential equations.

Money Quote: "In practice, training a Neural Network requires a lot of experimentation." (p148). Lol, no kidding.

## 12: Checkpoints

It can take hours or days to train a model. Save off instances as you go. Typically on an epoch, but possibly in smaller epoch chunks called steps.

Early stopping and checkpoint selection. You typically stop training when your loss function reaches a certain point. Alternatively you could continue training but save off checkpoints, inspect the loss function of each, and choose the optimal one. Often a model can settle in on a low loss for common cases, then go up and down again as it tunes for rare cases. Early stopping can be cheaper and checkpoint selection can get higher accuracy.

Offers L2 regression as a way to go instead of checkpoints, ideally in cases of a "well behaved" training loop where the loss plateaus.

Fine tuning. On new data can get a "warm start" working from a checkpoint. You might want to use an earlier checkpoint that didn't fine tune on previous rare cases.

Many tricks for large amounts of data that don't fit in memory.

## 13: Transfer Learning

Take a previously-trained model and solve a similar problem with a smaller dataset.

Learned about [ImageNet](https://www.image-net.org/index.php). 14 million labelled images.

The shortest path to glory is to freeze the weights up to the "bottleneck" (i.e. penultimate) layer and train only the output layer with new data. This is called "feature extraction". Optionally you could let the weights up to any of the layers to retrain, which is called "fine tuning". It's not advisable to let the lowest layers retrain, e.g. image classification because they correspond to the physical world (edges, shapes). You can do a binary search for the proper layer to freeze with "progressive fine tuning".

Regarding tabular data, this approach works better at the sentence level than the word level because you have context. See TabNet, Google's Universal Sentence Encoder, and BERT.

## 14: Distribution Strategy

Distribute the model training loop over multiple workers.

It can take days for some models to train. Offload this onto multiple servers. Can do synchronously or asynchronously.

Use specialized hardware for faster performance at reduced costs: GPUs, ASICs, FPGAs, TPUs (Tensor Processing Unit, an ASIC developed by Google for AI).

Interesting note about batch sizes... Larger batch sizes are more efficient for reduced I/O and taking advantage of hardware, however larger batch sizes can lead to reduced SGD convergence and accuracy. LAMB is a method for finding the sweet spot.

## 15: Hyperparameter Tuning

How to pick model architecture parameters (e.g. Number of layers, neurons/layer, decision tree depth) or model training parameters (e.g. number of epochs, learning rate, batch size)?

You could manually try different hyperparameter combinations (grid search), but takes a lot of time and can have combinatorial explosion of parameters.

Bayesian optimization: Try hyperparameter combinations with a faster "surrogate function" rather than model training. Usually use a Guassian process (Kriging?) or tree-structure Parzen estimator to take hyperparameters as input and a metric as output (accuracy, error).

Genetic algorithms: Instead of using a "surrogate function", you randomly take some combinations of hyperparameters, train the model a bit, and see what you have. Somehow the stronger combinations survive based on survival of the fittest. This isn't as well supported as Bayesian optimization.

Note the keras BayesianOptimization in hyperparameter_tuning.ipynb took a long time for me... 30 mins. I was using GPU hardware acceleration. The takeaway for me is even if you're using tricks the hyperparameter tuning can take a long time.

Like this DP! I bet one is often left wondering if a model would be better with different hyperparamters but didn't have the time to try.

## 16: Stateless Serving Function

Only deploy what you need for inference. Can deploy as a REST API with outputs that only depend on inputs (i.e. no state).

Good pragmatic tips in this one, though with the examples given I struggle to see how you would deploy with state, so the general advice here seems like common sense.

logit: function that takes -inf to +inf and returns 0 to 1.

## 17: Batch Serving

If you have to process many inferences, it's cheaper and more efficient to batch them. The example uses BigQuery which seems to batch automatically if necessary (I didn't see any special syntax like "batch").

Suggests using Apache Beam for serving if need pre/post processing (e.g. for images) or keep state (e.g. rolling average or counter).

Can cache results of a batch, e.g. run recommendations for all users at once (every so often) and store in a database.

Lambda Architecture: serving architecture that offers both single and batch processing. Google offers single processing through Cloud AI Platform Predictions and batch through BigQuery, can use same model in either. In some ways this book feels like and advertisement for google. Also, you know you've made it as a proper intellectual field once you've defined "lambda". Wikipedia at this time offers 16 different meanings for lambda. Sadly no mention yet for lambda architecture in machine learning.

IMHO the "positive" complaint example from the CFPB database should have been more amusing. (Though maybe I just got my hopes up.)

## 18: Continued Model Evaluation

"Deployment is not the end of a machine learning model's life cycle." (p220)

A deployed model might become stale over time due to concept drift or data drift.

When you deploy you save off some random inferences, compare them to ground truth, and decide to retrain based on a threshold or period of time. The decision on when/if to retrain is based heavily on domain knowledge, business case, and costs.

Ground truth can be done by using human labelling services (which sounds a bit inhuman to me) or tricking your users into giving you that information, e.g. having them provide a rating or being able to measure their decision (abandoning a shopping cart).

Google and TFX offer tools to help monitor deployed models. Facets provides data visualization.

## 19: Two-Phase Predictions

Continue to provide a model in the case of reduced server availability (server or network is down) by putting part or all of the model on the "edge" (user) device.

Examples:
* Google Translate. There is a great example of reduced quality, but available, translations from the edge device.
* Siri on iPhone. It would be expensive to constantly monitor audio over network listening for name.

Smaller models typically work better for edge devices, but with possibly reduced accuracy.

Quantization: convert a model to a smaller model.

Clever little edge devices for around $100: Coral Edge TPU and Jetson Nano.

## 20: Keyed Predictions

Have the user provide a key along with input features, and return the same key with the associated output features. The key can be a simple incrementing number, though take care not to have collisions if you have multiple clients.

## 21: Transform

Separate inputs, features, and transforms to make it easier to move a model to production.

Models take features as inputs, which aren't the same as the inputs given by clients. The example given is having the input value "3" for day of week, which could be a different day of the week depending on what day the week stars on. This is called "training-serving skew" and makes moving to production difficult. Make sure you serve on production with the same input->feature transforms you used to train with.

## 22: Repeatable Splitting

We like to use a deterministic way to separate out training, validation, and test datasets.

Use Farm Fingerprint hash on a column or columns, then mod 10 to put 80% training, 10% validation, 10% test. This is easier than trying to remember the seed for a random function.

Interestingly, choose a column or columns (or whole rows) to keep correlated rows in the same split. In the example they choose date for flight information, because flights on the same day are correlated. You can optionally group multiple columns, say date and airport destination. "In our experience, many problems with poor performance of ML can be addressed by designing the data split with potential correlations in mind." (p265)

Some data is correlated by time, examples given are weather patterns (stratify by month), or fraud detection (use the last x number of days because fraud patterns change over time).

## 23: Bridged Schema

How to update your model when you have a schema change in your new data, but you still need some of your old data for model accuracy. Use a statistical method in your one-hot encodings. p272 has a good rule of thumb list, e.g. Mean, median, mode, or frequency, depending on the situation.

"Union schema" is how *not* to do it... combining your old and new category values.

Great discussion on how much new data to set aside for validation, and how much old data to keep for training. In both cases you want as few as possible and you have to run trials to see where the prediction settles out.

## 24: Windowed Inference

Stateful stream processing. Use when inference requires a stream of instances. Example given is flight delays, which depends on time of day, they process on a rolling 2 hour window (closed every 10 mins).

Keep only the minimal amount of data in state. In the example, that was sum, sum^2, and count.

Time series models:
* ARIMA: Autoregressive integrated moving average.
* LSTM: Long short-term memory.

## 25: Workflow Pipeline

Rather than one monolithic process that does the entire data gathering, transforming, training, testing, production, etc, split these out into individual containerized steps. This enables collaboration, make the whole process more reliable, and enables some of the other design patterns.

## 26: Feature Store

Also known as feature engineering. Raw data can come from many different sources and processed by different people. Store and process the raw data in an organized way. Prevents training-serving skew.

For inference, store data with low latency, e.g. Cassandra or Redis. For training, need high throughput such as Hive or BigQuery.

Proposes open-source project Feast that does this.

## 27: Model Versioning

Provide newer models as a different version with a different URL. Typically only provide a new version if there is an API change, not for incremental improvements in inference performance.

## Notes on running *.ipynb files on google colab

Very few of the design pattern *.pynb files ran "out of the box" for me.

Sometimes had to authenticate:

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

By default the notebooks run without a GPU. The first notebook that needed it was DP6 multilabel.ipynb. Most calls run in < 10 seconds. FWIW: Edit -> Notebook settings -> Hardware Accelerator to enable GPU. 

### DP3, feature_cross.ipynb notes

This line ran for 29 minutes before I stopped it:

    CREATE OR REPLACE MODEL babyweight.natality_model ...

There isn't a progress bar or logging that I'm aware of in the notebook or on google console. Here's what I did:
* Moved the query from jupyter notebook over to a google console BigQuery query. It said there was a syntax issue in the query. When I removed the backtick quotes from 'babyweight.natality_model' and ran again it went away. This didn't turn out to be a problem on colab though.
* I don't know if this ultimately made a difference in execution time but I moved the cutoff year from 2000 to 2006, which reduced the database from 33 billion rows to 8 million.
* After that the query took 1 hour, 52 minutes! But it seemed to work. The df.head() call in the next code block gave reasonable rmse values.
* Shortly after this I hit a 403 error data usage quota. I went ahead and signed up for billing and $300 credit from google console and I was able to keep going. (They got me!) Note that enabling GPU didn't appear to help speed up this call, it warned that the GPU wasn't being used so I killed it after a couple of minutes. I didn't let it run so don't know for sure if it eventually helped.
* Running the Ny taxi data with an L2 regression the RMSE got slightly worse than without!

### DP4, mixed_representation.ipynb notes

Bug on this line. "tiled_input" should be "tiled_layer"?

    mixed_image_tabular_model = Model(inputs=[image_tabular_input, tiled_input], outputs=merged_image_output)

### DP6, multilable.ipynb notes

GPU acceleration helped speed this one up. It otherwise worked out of the box!

It had an anti-pattern example (do like), using softmax for a binary classifier instead of sigmoid. The optimization did run slightly faster (7 seconds instead of 8).

### DP7, ensemble_methods.ipynb notes

GPU helped again. Training the three models took 7.5 mins with the GPU. Fitting the ensemble model took 3.5 minutes.

The last step the initial Validation RMSE showed as 1.4, but when I ran it updated to 7.4. Hmmm.

### DP8, cascade.ipynb notes

Had to set up AI Platform Pipelines and had to add quite a few lines for authentication to get stuff to run.

Also had to install kfp and restart the runtime.

    !pip3 install kfp --upgrade --user

### DP10, rebalancing.ipynb notes

Python errors in the last few calls. "df" is not defined, and QueryJob isn't subscriptable from outlier_pred.

### DP11, overfitting.ipynb notes

Quick one! Be sure to use the chapter 4 data folder instead of the chapter 3 data folder. Different data even though files are same name and size.

### DP12, checkpoints.ipynb notes

Had to install tensorflow_io

    !pip3 install tensorflow_io
