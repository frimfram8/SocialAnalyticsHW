

```python
# Dependencies
import tweepy
import numpy as np
import pandas as pd
#import matplotlib as plt
import matplotlib.pyplot as plt
from matplotlib import style
style.use('ggplot')
from datetime import datetime
from config import consumer_key, consumer_secret, access_token, access_token_secret
#In truth, after spending about an hour looking at YouTube vids, SO pages, etc, I am not sure I understand how to
#use this "from config" and how to use a .gitignore file. I now have one, named config.gitignore, in my local HW
#folder, and in this file, I have put my Twitter API keys. Does this "from config import..." line 
#somehow tell my code to look at the config.gitignore file? No one can seem to explain this in plain English...

# Twitter API Keys
consumer_key = consumer_key
consumer_secret = consumer_secret
access_token = access_token
access_token_secret = access_token_secret

# Setup Tweepy API Authentication
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth, parser=tweepy.parsers.JSONParser())

from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
analyzer = SentimentIntensityAnalyzer()

```


```python
# Target User Account
#news_sources = ["@BBC", "@CNN", "@CBS", "@FoxNews", "@NYT"]

#news_compound_scores = {}

#for x in news_sources:

target_user1 = "@BBC"

# Counter
counter = 1

# Variables for holding sentiments, tweet text, and date (to pass in df later)
BBC_compound_list = []
BBC_positive_list = []
BBC_negative_list = []
BBC_neutral_list = []
BBC_tweet_text = []
BBC_tweet_date = []
BBC_tweets_ago = []

# Variable for max_id
oldest_tweet = None

# Loop through 5 pages of tweets (total 100 tweets)
for x in range(5):

    # Get all tweets from target user
    public_tweets = api.user_timeline(target_user1, max_id = oldest_tweet)

    # Loop through all tweets
    for tweet in public_tweets:

        # Run Vader Analysis on each tweet
        BBC_target_sample = tweet["text"]
        BBC_date = tweet["created_at"]

        BBC_results = analyzer.polarity_scores(BBC_target_sample)


        # Run analysis
        compound = BBC_results["compound"]
        pos = BBC_results["pos"]
        neu = BBC_results["neu"]
        neg = BBC_results["neg"]
        tweets_ago = counter

        # Print Analysis
        #print(target_sample)
        #print("BBC Compound Score:", compound)
        #print("BBC Positive Score:", pos)
        #print("BBC Neutral Score:", neu)
        #print("BBC Negative Score: ", neg)

         # Get Tweet ID, subtract 1, and assign to oldest_tweet
        oldest_tweet = tweet['id'] - 1

        # Add each value to the appropriate list
        BBC_compound_list.append(compound)
        BBC_positive_list.append(pos)
        BBC_negative_list.append(neg)
        BBC_neutral_list.append(neu)
        BBC_tweet_text.append(BBC_target_sample)
        BBC_tweet_date.append(BBC_date)
        BBC_tweets_ago.append(counter)

        # Add to counter 
        counter += 1
```


```python
# Print the Averages
print(f"User: {target_user1}")
print(f"Compound: {np.mean(BBC_compound_list):.3f}")
print(f"Positive: {np.mean(BBC_positive_list):.3f}")
print(f"Neutral: {np.mean(BBC_neutral_list):.3f}")
print(f"Negative: {np.mean(BBC_negative_list):.3f}")
```

    User: @BBC
    Compound: 0.092
    Positive: 0.091
    Neutral: 0.861
    Negative: 0.048



```python
#make a df with columns twitter account, tweet text, tweet date, tweet compound score, tweet pos score, 
#tweet neg score, tweet neu score

BBC_df = pd.DataFrame({"Source Account":target_user1,"Tweet Text":BBC_tweet_text,"Tweet Date":BBC_tweet_date,
                      "Sentiment Compound Score":BBC_compound_list,"Sentiment Positive Score":BBC_positive_list,
                      "Sentiment Negative Score":BBC_negative_list, "Sentiment Neutral Score":BBC_neutral_list,
                      "Tweets Ago":BBC_tweets_ago})
BBC_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Sentiment Compound Score</th>
      <th>Sentiment Negative Score</th>
      <th>Sentiment Neutral Score</th>
      <th>Sentiment Positive Score</th>
      <th>Source Account</th>
      <th>Tweet Date</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>Sun Apr 08 19:02:05 +0000 2018</td>
      <td>😺💦 This is Maya the jaguar's very first swim! ...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.4939</td>
      <td>0.000</td>
      <td>0.686</td>
      <td>0.314</td>
      <td>@BBC</td>
      <td>Sun Apr 08 18:03:03 +0000 2018</td>
      <td>😂 Need a hand getting the kids to bed? https:/...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.2500</td>
      <td>0.088</td>
      <td>0.765</td>
      <td>0.147</td>
      <td>@BBC</td>
      <td>Sun Apr 08 17:25:08 +0000 2018</td>
      <td>'Silencing masks' and 'wife auctions': What li...</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>Sun Apr 08 17:07:02 +0000 2018</td>
      <td>🦖😂 Philomena Cunk examines the barbaric condit...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>Sun Apr 08 16:40:55 +0000 2018</td>
      <td>RT @bbcgetinspired: Meet Sue...\n\nShe is plan...</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
#reorganize columns
BBC_df = BBC_df[["Source Account","Tweet Text","Tweet Date","Tweets Ago","Sentiment Compound Score","Sentiment Positive Score",
                "Sentiment Negative Score","Sentiment Neutral Score"]]
BBC_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Source Account</th>
      <th>Tweet Text</th>
      <th>Tweet Date</th>
      <th>Tweets Ago</th>
      <th>Sentiment Compound Score</th>
      <th>Sentiment Positive Score</th>
      <th>Sentiment Negative Score</th>
      <th>Sentiment Neutral Score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>@BBC</td>
      <td>😺💦 This is Maya the jaguar's very first swim! ...</td>
      <td>Sun Apr 08 19:02:05 +0000 2018</td>
      <td>1</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>1.000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>@BBC</td>
      <td>😂 Need a hand getting the kids to bed? https:/...</td>
      <td>Sun Apr 08 18:03:03 +0000 2018</td>
      <td>2</td>
      <td>0.4939</td>
      <td>0.314</td>
      <td>0.000</td>
      <td>0.686</td>
    </tr>
    <tr>
      <th>2</th>
      <td>@BBC</td>
      <td>'Silencing masks' and 'wife auctions': What li...</td>
      <td>Sun Apr 08 17:25:08 +0000 2018</td>
      <td>3</td>
      <td>0.2500</td>
      <td>0.147</td>
      <td>0.088</td>
      <td>0.765</td>
    </tr>
    <tr>
      <th>3</th>
      <td>@BBC</td>
      <td>🦖😂 Philomena Cunk examines the barbaric condit...</td>
      <td>Sun Apr 08 17:07:02 +0000 2018</td>
      <td>4</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>1.000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>@BBC</td>
      <td>RT @bbcgetinspired: Meet Sue...\n\nShe is plan...</td>
      <td>Sun Apr 08 16:40:55 +0000 2018</td>
      <td>5</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>1.000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#export the df to csv
BBC_df.to_csv("Output/BBC.csv", encoding="utf-8", index=False, header=True)
print("exported!")
```

    exported!



```python
#x axis is tweets ago (look at Plot Sentiments activity)
#Y axis is compound sentiment
# Create plot
BBC_x_axis = BBC_df["Tweets Ago"]
BBC_y_axis = BBC_df["Sentiment Compound Score"]
plt.plot(BBC_x_axis,
         BBC_y_axis, marker="o", linewidth=0.5,
         alpha=0.8)

# # Incorporate the other graph properties
now = datetime.now()
now = now.strftime("%Y-%m-%d %H:%M")
plt.title(f"Sentiment Analysis of Tweets ({now}) for {target_user1}")
plt.xlim([BBC_x_axis.max(),BBC_x_axis.min()]) #Bonus
plt.ylabel("Tweet Compound Sentiment")
plt.xlabel("Tweets Ago")
plt.savefig("{target_user1}.png")
plt.show()


```


![png](NewsMood_files/NewsMood_6_0.png)



```python
#repeat for each news source
#(I at first tried to loop this for each news source and preserve the compound sentiment variable for each news source
#in a dictionary to later use them in my bar graph... but I didn't really figure that one out and wanted to keep 
#and not overwrite the compound sentiment variable each time the code ran for a new news source. But that doesn't 
#really scale and so I inelegantly just did the code 5 times for each news source...

target_user2 = "@CNN"

# Counter
counter = 1

# Variables for holding sentiments, tweet text, and date (to pass in df later)
CNN_compound_list = []
CNN_positive_list = []
CNN_negative_list = []
CNN_neutral_list = []
CNN_tweet_text = []
CNN_tweet_date = []
CNN_tweets_ago = []

# Variable for max_id
oldest_tweet = None

# Loop through 5 pages of tweets (total 100 tweets)
for x in range(5):

    # Get all tweets from target user
    public_tweets = api.user_timeline(target_user2, max_id = oldest_tweet)

    # Loop through all tweets
    for tweet in public_tweets:

        # Run Vader Analysis on each tweet
        CNN_target_sample = tweet["text"]
        CNN_date = tweet["created_at"]

        CNN_results = analyzer.polarity_scores(CNN_target_sample)
    

        # Run analysis
        compound = CNN_results["compound"]
        pos = CNN_results["pos"]
        neu = CNN_results["neu"]
        neg = CNN_results["neg"]
        tweets_ago = counter
        
        # Print Analysis
        #print(target_sample)
        #print("BBC Compound Score:", compound)
        #print("BBC Positive Score:", pos)
        #print("BBC Neutral Score:", neu)
        #print("BBC Negative Score: ", neg)
        
         # Get Tweet ID, subtract 1, and assign to oldest_tweet
        oldest_tweet = tweet['id'] - 1

        # Add each value to the appropriate list
        CNN_compound_list.append(compound)
        CNN_positive_list.append(pos)
        CNN_negative_list.append(neg)
        CNN_neutral_list.append(neu)
        CNN_tweet_text.append(CNN_target_sample)
        CNN_tweet_date.append(CNN_date)
        CNN_tweets_ago.append(counter)
        
        # Add to counter 
        counter += 1
```


```python
# Print the Averages
print(f"User: {target_user2}")
print(f"Compound: {np.mean(CNN_compound_list):.3f}")
print(f"Positive: {np.mean(CNN_positive_list):.3f}")
print(f"Neutral: {np.mean(CNN_neutral_list):.3f}")
print(f"Negative: {np.mean(CNN_negative_list):.3f}")
```

    User: @CNN
    Compound: -0.087
    Positive: 0.068
    Neutral: 0.823
    Negative: 0.110



```python
#make a df with columns twitter account, tweet text, tweet date, tweet compound score, tweet pos score, 
#tweet neg score, tweet neu score

CNN_df = pd.DataFrame({"Source Account":target_user2,"Tweet Text":CNN_tweet_text,"Tweet Date":CNN_tweet_date,
                      "Sentiment Compound Score":CNN_compound_list,"Sentiment Positive Score":CNN_positive_list,
                      "Sentiment Negative Score":CNN_negative_list, "Sentiment Neutral Score":CNN_neutral_list,
                      "Tweets Ago":CNN_tweets_ago})
CNN_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Sentiment Compound Score</th>
      <th>Sentiment Negative Score</th>
      <th>Sentiment Neutral Score</th>
      <th>Sentiment Positive Score</th>
      <th>Source Account</th>
      <th>Tweet Date</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.7906</td>
      <td>0.368</td>
      <td>0.632</td>
      <td>0.000</td>
      <td>@CNN</td>
      <td>Mon Apr 09 04:00:19 +0000 2018</td>
      <td>Son of former NHL player among those dead in C...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@CNN</td>
      <td>Mon Apr 09 03:37:04 +0000 2018</td>
      <td>Deutsche Bank, Germany's biggest lender, is ge...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.5859</td>
      <td>0.000</td>
      <td>0.759</td>
      <td>0.241</td>
      <td>@CNN</td>
      <td>Mon Apr 09 03:30:06 +0000 2018</td>
      <td>16-year-old breaks Commonwealth Games record t...</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.5719</td>
      <td>0.236</td>
      <td>0.764</td>
      <td>0.000</td>
      <td>@CNN</td>
      <td>Mon Apr 09 03:13:55 +0000 2018</td>
      <td>"A Quiet Place" leads box office as horror kee...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@CNN</td>
      <td>Mon Apr 09 03:00:19 +0000 2018</td>
      <td>4 questions Congress must ask Facebook CEO Mar...</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
#export the df to csv
CNN_df.to_csv("Output/CNN.csv", encoding="utf-8", index=False, header=True)
print("exported!")
```

    exported!



```python
#x axis is tweets ago (look at Plot Sentiments activity)
#Y axis is compound sentiment
# Create plot
CNN_x_axis = CNN_df["Tweets Ago"]
CNN_y_axis = CNN_df["Sentiment Compound Score"]
plt.plot(CNN_x_axis,
         CNN_y_axis, marker="o", linewidth=0.5,
         alpha=0.8)

# # Incorporate the other graph properties
now = datetime.now()
now = now.strftime("%Y-%m-%d %H:%M")
plt.title(f"Sentiment Analysis of Tweets ({now}) for {target_user2}")
plt.xlim([CNN_x_axis.max(),CNN_x_axis.min()]) #Bonus
plt.ylabel("Tweet Compound Sentiment")
plt.xlabel("Tweets Ago")
plt.savefig("{target_user2}.png")
plt.show()

```


![png](NewsMood_files/NewsMood_11_0.png)



```python
#repeat for each news source
#(I chose not to loop this so that I could preserve the compound variable for each news source and later use them 
#in bar graph... but there's probably a more elegant way to do that)
target_user3 = "@CBS"

# Counter
counter = 1

# Variables for holding sentiments, tweet text, and date (to pass in df later)
CBS_compound_list = []
CBS_positive_list = []
CBS_negative_list = []
CBS_neutral_list = []
CBS_tweet_text = []
CBS_tweet_date = []
CBS_tweets_ago = []

# Variable for max_id
oldest_tweet = None

# Loop through 5 pages of tweets (total 100 tweets)
for x in range(5):

    # Get all tweets from target user
    public_tweets = api.user_timeline(target_user3, max_id = oldest_tweet)

    # Loop through all tweets
    for tweet in public_tweets:

        # Run Vader Analysis on each tweet
        CBS_target_sample = tweet["text"]
        CBS_date = tweet["created_at"]

        CBS_results = analyzer.polarity_scores(CBS_target_sample)
    

        # Run analysis
        compound = CBS_results["compound"]
        pos = CBS_results["pos"]
        neu = CBS_results["neu"]
        neg = CBS_results["neg"]
        tweets_ago = counter
        
        # Print Analysis
        #print(target_sample)
        #print("BBC Compound Score:", compound)
        #print("BBC Positive Score:", pos)
        #print("BBC Neutral Score:", neu)
        #print("BBC Negative Score: ", neg)
        
         # Get Tweet ID, subtract 1, and assign to oldest_tweet
        oldest_tweet = tweet['id'] - 1

        # Add each value to the appropriate list
        CBS_compound_list.append(compound)
        CBS_positive_list.append(pos)
        CBS_negative_list.append(neg)
        CBS_neutral_list.append(neu)
        CBS_tweet_text.append(CBS_target_sample)
        CBS_tweet_date.append(CBS_date)
        CBS_tweets_ago.append(counter)
        
        # Add to counter 
        counter += 1
```


```python
# Print the Averages
print(f"User: {target_user3}")
print(f"Compound: {np.mean(CBS_compound_list):.3f}")
print(f"Positive: {np.mean(CBS_positive_list):.3f}")
print(f"Neutral: {np.mean(CBS_neutral_list):.3f}")
print(f"Negative: {np.mean(CBS_negative_list):.3f}")
```

    User: @CBS
    Compound: 0.370
    Positive: 0.152
    Neutral: 0.829
    Negative: 0.019



```python
#make a df with columns twitter account, tweet text, tweet date, tweet compound score, tweet pos score, 
#tweet neg score, tweet neu score

CBS_df = pd.DataFrame({"Source Account":target_user3,"Tweet Text":CBS_tweet_text,"Tweet Date":CBS_tweet_date,
                      "Sentiment Compound Score":CBS_compound_list,"Sentiment Positive Score":CBS_positive_list,
                      "Sentiment Negative Score":CBS_negative_list, "Sentiment Neutral Score":CBS_neutral_list,
                      "Tweets Ago":CBS_tweets_ago})
CBS_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Sentiment Compound Score</th>
      <th>Sentiment Negative Score</th>
      <th>Sentiment Neutral Score</th>
      <th>Sentiment Positive Score</th>
      <th>Source Account</th>
      <th>Tweet Date</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.6467</td>
      <td>0.0</td>
      <td>0.720</td>
      <td>0.280</td>
      <td>@CBS</td>
      <td>Mon Apr 09 01:42:06 +0000 2018</td>
      <td>It's time to celebrate! @MomCBS will be back f...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.7096</td>
      <td>0.0</td>
      <td>0.742</td>
      <td>0.258</td>
      <td>@CBS</td>
      <td>Sun Apr 08 22:00:01 +0000 2018</td>
      <td>@OldDominion's Matthew Ramsey is here with a f...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.5562</td>
      <td>0.0</td>
      <td>0.854</td>
      <td>0.146</td>
      <td>@CBS</td>
      <td>Sun Apr 08 18:38:13 +0000 2018</td>
      <td>It all comes down to this! Stream Round 4 of #...</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.4199</td>
      <td>0.0</td>
      <td>0.892</td>
      <td>0.108</td>
      <td>@CBS</td>
      <td>Sun Apr 08 15:00:00 +0000 2018</td>
      <td>@reba is ready for her hosting duties! In just...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.6696</td>
      <td>0.0</td>
      <td>0.817</td>
      <td>0.183</td>
      <td>@CBS</td>
      <td>Sat Apr 07 19:35:08 +0000 2018</td>
      <td>Watch some of the biggest names in golf compet...</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
#export the df to csv
CBS_df.to_csv("Output/CBS.csv", encoding="utf-8", index=False, header=True)
print("exported!")
```

    exported!



```python
#x axis is tweets ago (look at Plot Sentiments activity)
#Y axis is compound sentiment
# Create plot
CBS_x_axis = CBS_df["Tweets Ago"]
CBS_y_axis = CBS_df["Sentiment Compound Score"]
plt.plot(CBS_x_axis,
         CBS_y_axis, marker="o", linewidth=0.5,
         alpha=0.8)

# # Incorporate the other graph properties
now = datetime.now()
now = now.strftime("%Y-%m-%d %H:%M")
plt.title(f"Sentiment Analysis of Tweets ({now}) for {target_user3}")
plt.xlim([CBS_x_axis.max(),CBS_x_axis.min()]) #Bonus
plt.ylabel("Tweet Compound Sentiment")
plt.xlabel("Tweets Ago")
plt.savefig("{target_user3}.png")
plt.show()

```


![png](NewsMood_files/NewsMood_16_0.png)



```python
#repeat for each news source
#(I chose not to loop this so that I could preserve the compound variable for each news source and later use them 
#in bar graph... but there's probably a more elegant way to do that)
target_user4 = "@FoxNews"

# Counter
counter = 1

# Variables for holding sentiments, tweet text, and date (to pass in df later)
FOX_compound_list = []
FOX_positive_list = []
FOX_negative_list = []
FOX_neutral_list = []
FOX_tweet_text = []
FOX_tweet_date = []
FOX_tweets_ago = []

# Variable for max_id
oldest_tweet = None

# Loop through 5 pages of tweets (total 100 tweets)
for x in range(5):

    # Get all tweets from target user
    public_tweets = api.user_timeline(target_user4, max_id = oldest_tweet)

    # Loop through all tweets
    for tweet in public_tweets:

        # Run Vader Analysis on each tweet
        FOX_target_sample = tweet["text"]
        FOX_date = tweet["created_at"]

        FOX_results = analyzer.polarity_scores(FOX_target_sample)
    

        # Run analysis
        compound = FOX_results["compound"]
        pos = FOX_results["pos"]
        neu = FOX_results["neu"]
        neg = FOX_results["neg"]
        tweets_ago = counter
        
        # Print Analysis
        #print(target_sample)
        #print("BBC Compound Score:", compound)
        #print("BBC Positive Score:", pos)
        #print("BBC Neutral Score:", neu)
        #print("BBC Negative Score: ", neg)
        
         # Get Tweet ID, subtract 1, and assign to oldest_tweet
        oldest_tweet = tweet['id'] - 1

        # Add each value to the appropriate list
        FOX_compound_list.append(compound)
        FOX_positive_list.append(pos)
        FOX_negative_list.append(neg)
        FOX_neutral_list.append(neu)
        FOX_tweet_text.append(FOX_target_sample)
        FOX_tweet_date.append(FOX_date)
        FOX_tweets_ago.append(counter)
        
        # Add to counter 
        counter += 1
```


```python
# Print the Averages
print(f"User: {target_user4}")
print(f"Compound: {np.mean(FOX_compound_list):.3f}")
print(f"Positive: {np.mean(FOX_positive_list):.3f}")
print(f"Neutral: {np.mean(FOX_neutral_list):.3f}")
print(f"Negative: {np.mean(FOX_negative_list):.3f}")
```

    User: @FoxNews
    Compound: -0.082
    Positive: 0.067
    Neutral: 0.825
    Negative: 0.108



```python
#make a df with columns twitter account, tweet text, tweet date, tweet compound score, tweet pos score, 
#tweet neg score, tweet neu score

FOX_df = pd.DataFrame({"Source Account":target_user4,"Tweet Text":FOX_tweet_text,"Tweet Date":FOX_tweet_date,
                      "Sentiment Compound Score":FOX_compound_list,"Sentiment Positive Score":FOX_positive_list,
                      "Sentiment Negative Score":FOX_negative_list, "Sentiment Neutral Score":FOX_neutral_list,
                      "Tweets Ago":FOX_tweets_ago})
FOX_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Sentiment Compound Score</th>
      <th>Sentiment Negative Score</th>
      <th>Sentiment Neutral Score</th>
      <th>Sentiment Positive Score</th>
      <th>Source Account</th>
      <th>Tweet Date</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.3612</td>
      <td>0.000</td>
      <td>0.878</td>
      <td>0.122</td>
      <td>@FoxNews</td>
      <td>Mon Apr 09 04:10:00 +0000 2018</td>
      <td>North Korea has told the @realDonaldTrump admi...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.4939</td>
      <td>0.000</td>
      <td>0.873</td>
      <td>0.127</td>
      <td>@FoxNews</td>
      <td>Mon Apr 09 04:05:00 +0000 2018</td>
      <td>Rain or shine, soldiers keep watch over the To...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.3818</td>
      <td>0.000</td>
      <td>0.885</td>
      <td>0.115</td>
      <td>@FoxNews</td>
      <td>Mon Apr 09 04:00:01 +0000 2018</td>
      <td>.@FrankLuntz: "It's [@POTUS's] own tweets that...</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@FoxNews</td>
      <td>Mon Apr 09 03:58:00 +0000 2018</td>
      <td>.@kdeleon: "This President, @realDonaldTrump, ...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.2960</td>
      <td>0.099</td>
      <td>0.901</td>
      <td>0.000</td>
      <td>@FoxNews</td>
      <td>Mon Apr 09 03:57:00 +0000 2018</td>
      <td>.@RepMcCaul: "When the drug cartel sees our mi...</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
#export the df to csv
FOX_df.to_csv("Output/FOX.csv", encoding="utf-8", index=False, header=True)
print("exported!")
```

    exported!



```python
#x axis is tweets ago (look at Plot Sentiments activity)
#Y axis is compound sentiment
# Create plot
FOX_x_axis = FOX_df["Tweets Ago"]
FOX_y_axis = FOX_df["Sentiment Compound Score"]
plt.plot(FOX_x_axis,
         FOX_y_axis, marker="o", linewidth=0.5,
         alpha=0.8)

# # Incorporate the other graph properties
now = datetime.now()
now = now.strftime("%Y-%m-%d %H:%M")
plt.title(f"Sentiment Analysis of Tweets ({now}) for {target_user4}")
plt.xlim([FOX_x_axis.max(),FOX_x_axis.min()]) #Bonus
plt.ylabel("Tweet Compound Sentiment")
plt.xlabel("Tweets Ago")
plt.savefig("{target_user4}.png")
plt.show()
```


![png](NewsMood_files/NewsMood_21_0.png)



```python
#repeat for each news source
#(I chose not to loop this so that I could preserve the compound variable for each news source and later use them 
#in bar graph... but there's probably a more elegant way to do that)
target_user5 = "@NYT"

# Counter
counter = 1

# Variables for holding sentiments, tweet text, and date (to pass in df later)
NYT_compound_list = []
NYT_positive_list = []
NYT_negative_list = []
NYT_neutral_list = []
NYT_tweet_text = []
NYT_tweet_date = []
NYT_tweets_ago = []

# Variable for max_id
oldest_tweet = None

# Loop through 5 pages of tweets (total 100 tweets)
for x in range(5):

    # Get all tweets from target user
    public_tweets = api.user_timeline(target_user5, max_id = oldest_tweet)

    # Loop through all tweets
    for tweet in public_tweets:

        # Run Vader Analysis on each tweet
        NYT_target_sample = tweet["text"]
        NYT_date = tweet["created_at"]

        NYT_results = analyzer.polarity_scores(NYT_target_sample)
    

        # Run analysis
        compound = NYT_results["compound"]
        pos = NYT_results["pos"]
        neu = NYT_results["neu"]
        neg = NYT_results["neg"]
        tweets_ago = counter
        
        # Print Analysis
        #print(target_sample)
        #print("BBC Compound Score:", compound)
        #print("BBC Positive Score:", pos)
        #print("BBC Neutral Score:", neu)
        #print("BBC Negative Score: ", neg)
        
         # Get Tweet ID, subtract 1, and assign to oldest_tweet
        oldest_tweet = tweet['id'] - 1

        # Add each value to the appropriate list
        NYT_compound_list.append(compound)
        NYT_positive_list.append(pos)
        NYT_negative_list.append(neg)
        NYT_neutral_list.append(neu)
        NYT_tweet_text.append(NYT_target_sample)
        NYT_tweet_date.append(NYT_date)
        NYT_tweets_ago.append(counter)
        
        # Add to counter 
        counter += 1
```


```python
# Print the Averages
print(f"User: {target_user5}")
print(f"Compound: {np.mean(NYT_compound_list):.3f}")
print(f"Positive: {np.mean(NYT_positive_list):.3f}")
print(f"Neutral: {np.mean(NYT_neutral_list):.3f}")
print(f"Negative: {np.mean(NYT_negative_list):.3f}")
```

    User: @NYT
    Compound: -0.047
    Positive: 0.065
    Neutral: 0.840
    Negative: 0.095



```python
#make a df with columns twitter account, tweet text, tweet date, tweet compound score, tweet pos score, 
#tweet neg score, tweet neu score
NYT_df = pd.DataFrame({"Source Account":target_user5,"Tweet Text":NYT_tweet_text,"Tweet Date":NYT_tweet_date,
                      "Sentiment Compound Score":NYT_compound_list,"Sentiment Positive Score":NYT_positive_list,
                      "Sentiment Negative Score":NYT_negative_list, "Sentiment Neutral Score":NYT_neutral_list,
                      "Tweets Ago":NYT_tweets_ago})
NYT_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Sentiment Compound Score</th>
      <th>Sentiment Negative Score</th>
      <th>Sentiment Neutral Score</th>
      <th>Sentiment Positive Score</th>
      <th>Source Account</th>
      <th>Tweet Date</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.3400</td>
      <td>0.000</td>
      <td>0.745</td>
      <td>0.255</td>
      <td>@NYT</td>
      <td>Mon Apr 09 04:04:57 +0000 2018</td>
      <td>19th-Century Playbills to Be Restored and Digi...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.5106</td>
      <td>0.000</td>
      <td>0.732</td>
      <td>0.268</td>
      <td>@NYT</td>
      <td>Mon Apr 09 03:50:56 +0000 2018</td>
      <td>Trust: ‘Trust’ Season 1, Episode 3: Change of ...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.3919</td>
      <td>0.100</td>
      <td>0.687</td>
      <td>0.213</td>
      <td>@NYT</td>
      <td>Mon Apr 09 03:49:56 +0000 2018</td>
      <td>On Golf: Jordan Spieth Misses a Victory, but G...</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@NYT</td>
      <td>Mon Apr 09 03:04:57 +0000 2018</td>
      <td>Billions: ‘Billions’ Season 3, Episode 3: Boss...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.6872</td>
      <td>0.288</td>
      <td>0.712</td>
      <td>0.000</td>
      <td>@NYT</td>
      <td>Mon Apr 09 02:49:57 +0000 2018</td>
      <td>Orioles 8, Yankees 7 | 12 innings: More Whiffs...</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
#export the df to csv
NYT_df.to_csv("Output/NYT.csv", encoding="utf-8", index=False, header=True)
print("exported!")
```

    exported!



```python
#x axis is tweets ago (look at Plot Sentiments activity)
#Y axis is compound sentiment
# Create plot
NYT_x_axis = NYT_df["Tweets Ago"]
NYT_y_axis = NYT_df["Sentiment Compound Score"]
plt.plot(NYT_x_axis,
         NYT_y_axis, marker="o", linewidth=0.5,
         alpha=0.8)

# # Incorporate the other graph properties
now = datetime.now()
now = now.strftime("%Y-%m-%d %H:%M")
plt.title(f"Sentiment Analysis of Tweets ({now}) for {target_user5}")
plt.xlim([NYT_x_axis.max(),NYT_x_axis.min()]) #Bonus
plt.ylabel("Tweet Compound Sentiment")
plt.xlabel("Tweets Ago")
plt.savefig("{target_user5}.png")
plt.show()
```


![png](NewsMood_files/NewsMood_26_0.png)



```python
#bar graph: target users in x, compound means in y
#create arrays for news sources and their compound means
news_sources = ["@BBC", "@CNN", "@CBS", "@FoxNews", "@NYT"]
compound_means = [np.mean(BBC_compound_list), np.mean(CNN_compound_list), np.mean(CBS_compound_list),
                 np.mean(FOX_compound_list), np.mean(NYT_compound_list)]
x_axis = np.arange(len(news_sources))

# apply align="edge" to ensure our bars line up with our tick marks
plt.bar(x_axis, compound_means, color='r', alpha=0.5, align="edge")
plt.axhline(0, color='black')

# Tell matplotlib where we would like to place each of our x axis headers
tick_locations = [value+0.4 for value in x_axis]
plt.xticks(tick_locations, news_sources)

# Sets the x limits of the current chart
plt.xlim(-0.25, len(x_axis))

# Sets the y limits of the current chart
plt.ylim(-1, 1)

# Give our chart some labels and a tile
plt.title("Twitter News Sentiments")
plt.xlabel("News Source")
plt.ylabel("Compound Sentiment of Last 100 Tweets")

# Save an image of the chart and print it to the screen
plt.savefig("NewsSentiments.png")
plt.show()
```


![png](NewsMood_files/NewsMood_27_0.png)



```python
#3 Observable Trends:
#1: Most major news sources seem to consciously try and keep their tweets neutral in tone. All sources scanned here
#had neutral sentiment scores in the .8 range, with pos and neg score in the .0 or .1 ranges. Thus their compound
#scores were relatively close to zero. I suspect the major news sources actually use sentiment analysis 
#of their Twitter feeds and other public materials (even articles) in order to ensure this.

#2:CBS had more positive tweets than the other sources here.

#3: The NYT was the source whose tweets hold closest to a zero compound score overall. 


```
