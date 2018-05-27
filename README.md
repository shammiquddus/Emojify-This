# Emojiy-This
This project was inspired by the work done by Rahwan et. al at the MIT Media Lab in the Deepmoji project. We use a dataset of tweets with emojis from an online twitter archive to train a model that predicts the likely emojis for a given tweet. Tweets with emojis are used to train a bidirectional LSTM with dropout, using the open source GloVe Twitter word vectors which have been trained on a Twitter corpus. Our results show a “Top 1 emoji” accuracy of about 10.7% on the test set. We discuss methods to improve upon this result. Sample outputs from our model include:
![emoji_output](https://user-images.githubusercontent.com/31656996/40581492-cbd7af4e-610e-11e8-9d0e-9be17efcbb6a.png)

## Dataset
We used a large, publicly available set of JSON Twitter data from November 2017. We analyzed a total of about 4M tweets, but our final, cleaned, data set includes approximately 7,322 tweets. Pre-processing raw data took significant effort.

* Removing Metadata: The Twitter data contained a substantial amount of metadata apart from the actual text of the tweets. In our experimentation, we decided to focus only on the words of the tweet instead of using any of the other provided data (tweet data, location, userID, etc)

* URL and Foreign Language: We removed URLs and usernames from the data; usernames, in particular, were quite prevalent since many tweets are in fact retweets of other users’s posts. We also removed any non alphanumeric characters including punctuation and characters of non-Western scripts. Our focus is initially on just Western text. 

* Non-emoji tweets: We removed all tweets that did not contain emojis.

* Separating text and emoji: We separated out the text (X) of individual tweets from the emojis(Y) used in them. If a tweet contained multiple emojis, we create separate data points for each use. We made this decision to credit tweets that used multiple emojis, since the authors of these tweets likely felt stronger about their emoji usage than those authors who only used one emoji. After separating text and emojis, we indexed the emojis to assign numbers to each one; our data set contains 149 unique emojis.

* Tokenization: Lastly, we tokenized the X and passed each word through a 50 dimensional pre-trained Twitter GloVe model to generate embeddings.  

* Unbalanced dataset: Heart emoji was found in 30% of our datasets followed by sparkles emoji which was about 25%. Our model learned that the best way to maximize accuracy is to guess heart emoji and it will be statistically correct 30% of the time, regardless of meaning. Initially we were very excited to hit such a high accuracy but when we printed our results we saw that the first emoji was always heart by default. We hand engineered our dataset to make it more evenly distributed with no single emoji exceeding 300 observations in our dataset. The small size of our dataset is a significant limitation. We underestimated how much time it would take to clean up data. Radwan etl. al used millions of tweet to train for each single emoji. Due to small data size we only have a train and test set split by 90%-10%. We decided on this larger proportion of training data, because our data set is quite small and we wanted to ensure adequate data points for training. 
 
### Methods
We used a sequential baseline and a bidirectional LSTM structure implemented in Keras. The LSTM model was built using source code from Stanford University’s CS230 course.

* Baseline: Our baseline  model has two fully connected layers, one hidden layer and one output layer. The hidden layer uses a ReLu activation function with 64 hidden nodes. The output layer uses a sigmoid activation and contains 15 nodes. 

* Final: Our final model is shown in the figure below. Each bidirectional LSTM layer has 128 nodes with tanh activation, each dropout layer is set to 50%, with a fully-connected softmax activation layer. We print the top five probabilities from the softmax layer. We use a cross-entropy loss function, an adam optimizer and accuray as our performance metric when compiling the model.
