# Musicbox-churn-rate-prediction
## The goal is to study the user behavior such as churn prediction based on the log data which includes the history of playing, searching and downloading the songs for users. We can also make recommendations to users based on the data.

## Introduction to the data
### 1. Play.log(17G)
- user_id (numeric) :5 digits-11digits
- device (categoric) :ar/ip
- song_id (numeric): 1 digit - 15 digits
- song_type (categoric) : 0/1/2
- song_name(object) : Chinese/Japanese character
- singer(object) : Chinese/Japanese character
- play_time(numeric): records as single play records(not cumulative)most dirty data and need lots of effort to clean up
- song_length(numeric): records in seconds
- paid_flag (categoric): 0/1
- file_name(object): mannually extracted from file_name, added as a new column when integrating file, as the record day
### 2. search.log(11G)
- user_id
- device
- song_id
- search time
- url
### 3. download.log(7G)
- user_id
- song_id
- song_name
- song_type
- singer

## The workflow
### 1. Download the data
### 2. Preprocessing---downsampling and add date and event label to each record
- Because the dataset is too large, I did a user-based downsampling to get the records for 10% of all the users.
- The file name is like "20170402_1_search.log.tar.gz", therefore when we read the records, we create a "date" column for the date, and a "event" label ("P", "D", "S") indicating it is a record for playing, downloading or searching.
- I also did other regular processings like imputing missing values, deleting robot users and so on.
### 3. Feature engineering (Spark)
- The dates ranged from 2017-03-30 to 2017-05-12. I defined the uses as churned uses if they did not have any activity in the last 14 days, which was called the churn window. Then the time window from the beginning to the start of churn window was called the feature window, which was used to create the frequency features. 
- I created the frequency features to represent the user behavior in the time window [1, 3, 7, 14, 30] such as freq_P_last_3 means how many songs this user played in the last 3 days.
- I also practiced using dataframe in Spark to process the data.
### 4. Model training
- I used several popular classification models to train a prediction model for whether a user would churn, including logistic regression, random forest and gradient boosing decision tree.
- The AUC score I got was around 0.9 and the accuracy score was around 0.85.

## Some thoughts on improvements and business insights
- Because we also have the "play_time" in the play log file, I also tried to create frequency features for "finished_songs", which is defined as whether the song was played more than 80% of its time and the total "play_time". The result is not include since it does not have a big improvement on the AUC or accuracy.
- We could seek a better way to define the churn.
- We could also build recommendation systems based on the song_type.
- A large fluctution was found in the Iphone users. Therefore we could improve the Iphone app such as API, latency to improve the user experience.

## Summry
This is a good project to practice the data science skills. The data is from real life and I learned some ways to process the data before feeding it to the models. The result could provide insights for improving the retention rate and implementing some recommendation system into the product, as well as improving the app for better user experience.
