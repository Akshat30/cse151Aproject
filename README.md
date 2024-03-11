# CSE 151A Project
Group Members: Christen Xie, Ojas Vashishtha, Liam Manatt, Akshat Jain

## Colab Notebook:
View only link [here](https://colab.research.google.com/drive/11aCk1dcJwYg5TeijLqb4zEgZH70U5kcP?usp=sharing).

Note: `cse151Aproject.ipynb` in repository is periodically updated as a copy of the notebook linked above. View link for most up to date version of notebooik.

## Introduction:
Dataset: [Spotify Songs Information](https://www.kaggle.com/datasets/lilycarew/spotify-songs-information/data)

### Abstract:

This project will aim to predict the popularity of new songs that are released on Spotify, , a popular music streaming platform. For context, Spotify’s API allows developers to access lots of information about songs through an endpoint. This information includes quantitative features such as danceability, tempo, instrumentalness, and more. The dataset linked contains the Spotify calculated features for 10130 songs, from different genres. We will be adding the feature of popularity to this dataset by fetching the popularity (scale of 1-100) from the Spotify API for each of the songs and storing it in a column. We will be using sci-kit learn to create a model that will be able to predict the popularity of a given song, based on its features. We will begin with a basic linear regression model as our baseline model, using results from the baseline model to move on to more advanced models such as decision tree and random forest regressors. We will also consider feature engineering, to see whether some features together may affect the popularity. Even though Spotify offers popularity as a metric for songs, this model will be useful to be able to predict the future popularity of songs, as the popularity value of songs changes over time. For example, for a new song that is released, we can input the metrics regarding danceability, instrumentalness, and other features (by fetching from Spotify API), and then receive a prediction on how popular the song will become from our model.


### Description:
This data set has over 10,000 songs with 21 nonindex features. The data is from Spotify, a popular music streaming platform. This dataset was found on kaggle, and is contained in a single 3.16 mb csv file. The features, as collected by Spotify and accessible through its API, are as follows:
- Danceability: Score 0-1
- Energy: Score 0-1
- Key: Integer describing the key 0-11
- Loudness: -45-0
- Mode: 0-1
- speechiness: 0-1 describes the ratio of vocals to sound
- Accousticness; 0-1
- Instrumentals: 0-1 describes the ratio of instrumentals to sound
- Valence: 0-1
- Tempo: 0-220 in BPM
- Type: 2 classes 
- Id: identifies the track on spotify
- Uri: Uniform Resource Identifier
- Track_href: We may use this to collect additional data
- Analysis_url: Link to analysis of the track
- Duration: Int in ms describing the length
- Time_signature: notes per bar
- Song_name: string of the name
- Artist: string with artist name

### Importing popularity data from the Spotify API
As our abstract states, our goal is to predict the popularity for the songs based on the other factors in the dataset. However, because the dataset does not contain the popularity for each song, we wrote a python script that fetched the popularities for each song and added it as a column `popularity` in our csv. The python script can be found in the Code, titled `fetch_popularity.py`.

We did run into an issue, which is that for a good amount of songs (even really popular ones), Spotify returns 0 as the popularity.

## Data Exploration:
From the Jypter Notebook linked above, we have these key insights:
- 10130 total entries
- 23 columns
- 14 of which are numerical (not counting entry as it just the row number)
- danceability, energy, key, loudness, mode, and speechiness are each missing 3 entries
- artist is missing 26 entries (that is OK as we will likely not be using it)

The information regarding columns can be found in the description section above.

**As mentioned above, the Spotify API was returning 0 for the popularity for a good amount of songs. After data exploration, this number was found to be 2479 out of 10130 songs. **

## Data Preprocessing PLAN:
One of our first plans is to research how to fix the issue of songs having a popularity of 0. After some research we found out that this may be happening because we did not specify the correct market for the song, which is why it was returning 0 for those songs. [Source](https://stackoverflow.com/questions/75572315/spotipy-popularity-value-is-different-than-the-value-retrieved-from-spotify-api).

As a result, we will be going through and refetching the popularity for songs with 0 in our data, this time specifying 'US' as the market.

In addition, after analyzing the heatmap and pairplots, it is evident that the other values are not well correlated with popularity (the highest column being instrumentallness with a value of -0.29). Thus we are aiming to improve this by:
- fetching more factors from the Spotify API and plotting those
- adding more entries such as liveness, start_of_fade_out, start_of_fade_in, time signature, and pitches (meaning we get data for more songs from the Spotify API)
- quantifying categorical values, such as genre, to help provide an additional factor for our model

# Milestone 3

## Preprocessing Data
As we planned, we set up one hot encoding for the categorical values. We one hot encoded key, genre, and time signature. We then added two new features to make use of the data in artist name and song name. We create the features song_name_length and artist_name_length, we are hoping to use the relation of name length to better predict popularity. Then we min-maxed the remaining numerical features because they fall between the bounded interval 0-1. 

We went through and refetched the popularity for songs that had a popularity of 0 originally. This time, we specified the market to be US, and had backup countries in case the popularity for the US was 0. After going through and updating these values, the number of songs with a popularity of 0 went from 2479 to 195. This gave us 2284 more datapoints to work with, allowing our training to be better.


## Model Version 1
With the min-maxed data and features created, we created a Linear Regression model to predict popularity. We set the training sample size to 80% and for any rows in the dataframe that had invalid data, we simply dropped them. Since we had over 10k rows of data, and we didn't have to drop many rows (around 10) we thought it would be fine to just drop them instead of replacing them with approximate data. 

In addition, we implemented another linaer regression model with cross-validation. We used 10 folds for cross validation. For both these models, we got MSE values of around 400, which was pretty poor. We are currently in the under-fitting side of the fitting graph as our model produces a line which doesn't fit the data well. This was just a baseline model to understand the complexity required so this is expected.


## Future steps
Given the relatively poor performance of our initial linear regression model, we would like to move on to more complex models in the future. One possible model we were thinking about was a DecisionTreeRegressor or RandomForestRegressor, something that could better capture the complex features we currently have and doesn't rely on linear relationships. 

Another model could possibly be a kernelized SVM. Given that our features/data clearly doesn't follow a simple, straight-line, linear relationship a kernelized SVM could also help better prediction while avoiding some of the overfitting a decision tree/random forest could bring.

# Milestone 4

## Evaluation of data, labels, and loss function
For the data, we decided to make a change by using the StandardScaler in addition to our already scaled data. We also pivoted away from simple linear regression and went forward with a gradient booster regressor. Since our initial linear regression model wasn't as accurate, we decided to use a model that could better capture the non-linear relationship the dataset features had with the target variable. 


## Comparing Training vs Test Error 
To train the model, we used the same X_train, X_test, y_train, and y_test datasets as our initial linear regression model to ensure that we could compare the models' performances fairly. We also scaled the X_train and X_test datasets using standard scaler. After training the gradient boosting model, we found training and testing error of 347.7402630199366 and 399.97546067610523 respectively. From this it is clear that the data does not seem to be overfitting in any significant way as training and testing error are relatively similar. 

## Fitting Graph and Comparison to Model 1 
Compared to model 1 which had training and testing error of 444.6857792808256 and 437.17367820600833 respectively, Model 2 is an improvement with lower training and testing errors. This is likely due to the fact that the Gradieng Boosting model better captures the non-linear relationship.

### 5. Extensive Steps 
anything on k-fold cross valid, hyper parameters?

## Plan for Next model 
For our next model, we want to continue the trend of using models that can capture non-linear relationships. One possibility is kernel ridge regression, although we currently don't have an overfitting problem and the model could be computationally expensive. Another possibility is a random forest regressor, which could also help with capturing relationships between features and the popularity of a song.

## Conclusion 
Overall, the 2nd model was a success in that we improved both training and testing error without seeing much overfitting. The model was able to perform well due to its innate design in using an ensemble of models as well as our data being standardized and scaled. Hyper-parameter tuning could definitely add to this model, and is something we will explore in the future.
