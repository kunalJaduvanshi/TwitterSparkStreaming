Sentiment Analysis of Live Twitter Stream Using Apache Spark 
============================================================

This application analyzes live tweets and predicts if they are positive, or negative. The application works by connecting 
to the Twitter stream, and applying a model built offline using Spark's machine learning library (Mllib) to classify 
the tweet's sentiment. Using the instructions on this page, you will be able to build the model on HDP Sandbox and then 
apply it to a live twitter stream.

Prerequisites
-------------

* Download the HDP 2.3 Sandbox from [here](http://hortonworks.com/products/hortonworks-sandbox/#install)
* Start the Sandbox, and add its IP address into your local machine's /etc/hosts file:

```bash
$ sudo echo "172.16.139.139 sandbox.hortonworks.com" >> /etc/hosts
```

* Log into the sandbox, and clone this repository:  


* Download the labeled training data into the sandbox. 


* Put the tweet data into hdfs, at a location /tmp/tweets


* Sign up for dev Twitter account and get the OAuth credentials [here for free](https://apps.twitter.com/). 


Build and package the code
-----------------------------------------

Compile the code using maven:

```bash
$ cd
$ cd spark-twitter-sentiment
$ mvn clean package 
``` 

Train the Model 
-----------------------------------------

```bash
$ spark-submit --master yarn-client \
               --driver-memory 1g \
               --executor-memory 2g \
               target/twittersentiment-0.0.1-jar-with-dependencies.jar \
               hdfs://tmp/tweets/dataset.csv
               trainedModel
```

Some Examples can be: 

Text =       I am Negative..
Actual Label = negative
Predicted Label = negative


Text =       Thank you, Sir. 
Actual Label = positive
Predicted Label = positive


Text =     I am KunalJaduvanshi, I am very Happy.
Actual Label = positive
Predicted Label = positive

REMINDER - YOU WILL NEED YOUR TWITTER CONSUMER DETAILS AND ACCESS TOKEN TO LIVE STREAM TWITTER DATA.

```bash
$ cd 
$ spark-submit \
    --class com.dhruv.Predict \
    --master yarn-client \
    --num-executors 2 \
    --executor-memory 512m \
    --executor-cores 2 \
    target/twittersentiment-0.0.1-jar-with-dependencies.jar \
    trainedModel \ 
    --consumerKey {your Twitter consumer key} \
    --consumerSecret {your Twitter consumer secret} \
    --accessToken {your Twitter access token} \
    --accessTokenSecret {your Twitter access token secret}
```

This command will set up spark streaming, connect to twitter using your dev credentials, and start printing tweets 
with predicted sentiment. Label 1.0 is a positive sentiment, and 0.0 is negative. Each tweet and its predicted label
is displayed like this:

```bash
(#Listened Chasing Pavements by Adele on #MixRadio #ListenNow #Pop #NowPlaying #19 http://t.co/qLXGoq8B8u,1.0)
(Work isn't going so bad, but if I did fix it wtf,0.0)
(RT @RandyAlley: Come on let's have a win Rovers! Good luck lads @Shaun_Lunt @TREVORBC83 http://t.co/tsDEZPrJIO,1.0)
(RT @ribbonchariots: dress shirt ftw!!!! &  the v gets deeper and deeper....... http://t.co/qAL3zIteVF,1.0)
```


