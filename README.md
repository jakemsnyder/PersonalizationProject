# Last.fm Recommender System

We chose to investigate the Last.fm data using a temporal collaborative filtering model; specifically, we want to investigate the effect time of day and day of the week has on users' listening habits, with our objective to reduce the number of times a user will skip the song. Because we are using skips as a proxy for a user's enjoyment of the song, we do not need to rely on explicit feedback data, such as a user's song rating. In addition, we do not need to be wary of how long it has been since a user has rated a song, since consumer preferences can change over time. Because skips are all in the present, we can ignore this recency factor.

## The Data

Last.fm data set contains:
* Listening habits for roughly 992 users
* 173,921 artists with timestamped entries
* Metafile containing user profiles (e.g., gender, age, country and signup date).

The 1K data set can be found through this [link](http://www.dtic.upf.edu/~ocelma/MusicRecommendationDataset/lastfm-1K.html). The data was collected by Òscar Celma.
 
The data set records users’ listening history without recording explicit feedback on artist and track pairs.  As there is no input from a user on whether they liked or disliked a track, we initially treat the data as an indication of positive preference. This will be expanded to include skips for the full recommender system.

A preview of the data is shown here, with the leftmost column as the Pandas index:
![Data Preview](data/DataPreview.png)

The following is the play counts for the entire data set, grouped by hour of the day (left) and day of the week (right):
![Play Counts by hour of day and day of week](data/PlayCounts.png)

## Part I Objective
We are interested in learning how neighborhood - and model-based collaborative filters (CF) would perform on aggregated data. These CF approaches will help us understand how an improved recommendation engine can drive increased user engagement with the music platform.  We intend to ustilize timestamps fot the final project along with other metadata to build better personalization systems.

### Neighborhood-Based Collaborative Filter Analysis
We implemented an user-user neighborhood based collaorative filtering technique. We tried to predict the interest that a user may have in a particular artist in terms of the number of times they would listen to an artist, based on their average artist plays and that of their peers.

#### Data preprocessing
We grouped our data into artists and users for the first part of the project, with the values as number of plays. Hence the recommendations are at an artist level and not individual songs. There is no additional pre-processing.

#### Similarity Metric
We used Pearson correlation, primarily because of the nature of the dataset and how missing values are interpreted. We found the find similarity between users on their common tastes, irrespective of missing values.

*Network of highly correlated users*
![Visualization of correlation between users](data/highly-correlated-users.png)


#### Training and Testing data
We split the dataset into 80-20 train-test split. This split was not completely random. For each user, a random 20% of the artists they listen to are put into the test data. This ensures that every user is represented in the test data. There is no explicit validation (tuning) dataset. This choice was made because of the nature of the model which relies on the similarities between users, has only one hyperparameter K and has no scope for overfitting the test data. Having an additional tuning dataset would result in loss of data for training purposes. 

#### Model evaluation
Since the prediction value is the number of times a user is expected to listen to an artist, which is a continuous variable, we chose to use RMSE and MAE, which are standard metrics for evaluating continuous predctors.

#### Additonal Design Considerations
We considered having an item-item based collaborative filtering model but decided to stick with user-user based. This choice was made because we have 992 users but nearly 174,000 unique artists. The similarity matrix for such a large number of items would be huge as well as extremely sparse. Most artists feature only a few times in the dataset and hence would not have any similar neighbors for a meaningful analysis. 

We also decided to work with aggregated artists for the first part of the project. This helps reduce the size and complexity of the data for our first exploration of the data. For our final project, we will include content based models with time as an important variable.

#### Model performance with hyperparameter tuning
The model has only one hyperparameter - the neighborhood size K. Increasing K improves both RMSE and MAE. 

![Neighbors RMSE vs K](data/neighbors_mse_vs_k.png)

![Neighbors MAE vs K](data/neighbors_mae_vs_k.png)

#### Model performance with data size
We varied the data size sytematically from 100 users to 1000, keeping a constant K value. The results are follows:

![Neighbors RMSE vs Sample Size](data/neighbors_mse_vs_sample_size.png)

![Neighbors MAE vs Sample Size](data/neighbors_mae_vs_sample_size.png)

#### Scaling of running time with data size
Finding user-user similarity matrix is an O(n^2) operation as each pair of users need to be assessed. Making predictions is an O(K n) operation, as for each user, we need to look at all their K peers and predict accordingly. The total running time is hence asymptotically dominated by the similarity matrix step which scales as O(n^2).


### Model-Based Collaborative Filter Analysis
We are using the SVD algorithm as a benchmark for a model-based CF.  We use the Surprise package, which can be installed using the following command: `pip install scikit-surprise`.  Read more about Surprise [here](http://surpriselib.com/).
* How was the data preprocessed?
* How are the training, tuning and test data sets defined?
* How is the model evaluated?


### Conclusion


## Part II Objective
We will incorporate timestamps and signup dates to further clarify users’ preferences for artists and tracks.  Based on these data points, we can derive a skips parameter.  This parameter indicates whether the user skipped to the next track in the playlist and will help us answer questions such as:
* Can skips be used to improve music recommendation?
* Can we use timestamps and skips to identify the user’s mood (i.e., genre preference given an unspecified activity)?
* After how many times of listening to a track is a user more likely to start skipping the track?
 
 
## Sources/Relevant Literature
 
Hu, Y., Koren, Y., & Volinsky, C. Collaborative Filtering for Implicit Feedback Data Sets. Retrieved from http://yifanhu.net/PUB/cf.pdf
