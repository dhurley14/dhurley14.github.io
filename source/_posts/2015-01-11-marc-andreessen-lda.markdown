---
layout: post
title: "Topic Analysis and Marc Andreessen's tweets"
date: 2015-01-11 22:43:07 -0500
comments: true
categories: [Machine Learning, LDA, Twitter, topic analysis]
---

##Background Motivation
I began this experiment with topic analysis on a friend's suggestion on utilizing the treasure trove that is twitter.
Marc Andreessen retweets a ton of different people all the time.  It's impossible to keep up with what those people's interests are,
and more specifically what pmarca's interests are.  I wanted to learn what exactly are the "Top Ten Things" that Marc Andreessen retweets, and how does he feel about these topics?
<!-- more -->

As we all know, a retweet basically widdles down to agreeing with the tweet's author.  This is where I decided to begin
researching topic analysis with Latent Dirichlet Allocation.

The first part of this blog series will cover a review of how I acquired the twitter data and the steps I took to get the the top
ten topics pmarca retweets. This blog owes a special thanks to the tutorial blog from [mathandpencil](http://blog.mathandpencil.com/using-latent-dirichlet-allocation-to-categorize-my-twitter-feed/).  The second part of this blog series will cover sentiment analysis on the tweets that are associated
with each topic.  Thus giving us an overview of how Marc Andreessen feels about specific topics he tweets about, based on people he 
retweets.

###Acquiring pmarca's Tweets

I utilized the [twython](https://pypi.python.org/pypi/twython/) library to acquire the tweets.  Unfortunately, Twitter's api limits you to the 3200 most recent tweets so a quick introduction to the API might be useful to some.  The file containing all of this logic is the aptly named (I think at least) get_tweets.py.

I first needed access to the Twitter API.  Twython makes this extrememly easy and all I need to do is add my `APP_KEY` and `APP_SECRET` for authorization.

``` Python
    from twython import Twython

    def get_authentication():
        """ Get a connection to the Twitter API using Twython.
        return a new instance of the Twython object.
        """
        APP_KEY = 'xxxx'
        APP_SECRET = 'xxxxx'
        twitter = Twython(APP_KEY, APP_SECRET)
        return twitter
```
The next step was to get the user's tweets.  The `**kwargs` were necessary so that I could loop through the Twitter API and acquire all 3200 of pmarca's most recent tweets (again Twitter doesn't allow you to access all of a user's tweets).

``` Python
    def get_tweets(username='pmarca', **kwargs):
        """ Get a user's timeline
        :param username: (default pmarca)
        :param tweetID: 
        """
        twitter = get_authentication()

        # to get all 3200 possible tweets, I must cycle
        # through, and change the max_id on each call to be the lowest
        # id , so that my next call gets all the tweets below that id,
        # and so on and so forth.
        user_timeline = ""
    
        if len(kwargs) == 0:
            user_timeline = twitter.get_user_timeline(screen_name=username, count=200)
        else:
            user_timeline = twitter.get_user_timeline(screen_name=username, count=200, max_id=kwargs['anId'])    
    
        return user_timeline
```

Now that I have my authentication and a way to get the user's timeline (note that `count=200` is the max amount of tweets from a user's timeline you can receive before having to paginate, which is what I will show next), I will build a python dict that will store the tweets and the hyperlinks that the tweet links to.  This will help me perform sentiment analysis on the topics using the tweets as my sentiment indicators.

To get the next oldest 200 tweets, and so on and so forth until I max out at 3200, I had to loop through get_tweets(), setting the `tweetID` parameter to the ID of the last tweet that I received in the previous API call.  I do this 16 times in total (hence, 3200 tweets).  I then write all of the tweets to a file called `pmarca_rts_append.txt`.

``` Python
def output_all_rts():
    user_timeline = get_tweets()
    
    # the below will build the first entry in our dict.  The dict will map tweet's and their corresponding URL's that were found in the tweets.
    urls = {user_timeline[i]['entities']['urls'][0]['expanded_url']: user_timeline[i]['text']  for i in xrange(len(user_timeline)) if user_timeline[i]['entities']['urls'] and "RT" in user_timeline[i]['text']}
    
    for i in xrange(16):
        user_timeline = get_tweets(username='pmarca',anId=user_timeline[len(user_timeline)-1]['id'])
        # Now I update that dict again to map more tweet's and URL's together
        # this will help when I have to perform sentiment analysis on each topic.
        urls.update({user_timeline[i]['entities']['urls'][0]['expanded_url']: user_timeline[i]['text'] for i in xrange(len(user_timeline))
            if user_timeline[i]['entities']['urls'] and "RT" in user_timeline[i]['text']})

    print len(urls)

    output_file = open("pmarca_rts_links_dict.txt", "w+b")
    output_file.write(str(urls))
    output_file.close()

if __name__ == '__main__':
    output_all_rts()

```

Now I have all of pmarca's retweets (which turns out to be 1/2 of all of his tweets returned by the API).
Next up will be to extract the text from the associated links.  

In order to build my LDA model I needed to find a way to extract the body of the text from each article for which I had links to.  On the [mathandpencil](http://blog.mathandpencil.com/using-latent-dirichlet-allocation-to-categorize-my-twitter-feed/) blog, Joseph Misiti uses Diffbot to acquire the text body.  Unfortunately my Diffbot account is no longer active so I used the extremely handy [python-boilerpipe wrapper](https://github.com/misja/python-boilerpipe) library to handle this for me.

First, I needed to strip out the URL's from the dictionary I built when I extracted the links from the tweets.  At this point, I figured it would be better to just
map the tweets to the article body itself.  So I wrote a script to do that.  

By passing in the dict I had saved previously `pmarca_rts_links_dict.txt` I was able to extract the text from the hyperlinks and map the body article text to the 
tweets (I used `|||` as the chars to separate the tweet and the article body text).

``` Python
    from boilerpipe.extract import Extractor
import ast, os, sys

""" Read a dict file mapping, get the urls, 

extract the text, send a mapping of the text 
and the associated url, close the file
01/19/2015
"""

def buildMapping(someDictFile):
    somedata = open(someDictFile).read()
    result = ast.literal_eval(somedata)
    keySize = len(result.keys())
    successRatio = 0
    linksLeft = 0
    target = open('content_link_mapping.txt','w')
    for key in result.keys():
    
        try:
            extractor = Extractor(extractor='ArticleSentencesExtractor',url=str(key))
            print("got the extractor")
            #target.write(str(result[key])+"|||"+extractor.getText().encode('utf-8')+'\n\n\n')
            target.write(result[key].encode('utf-8') + " ||| "+extractor.getText().replace("\n","").encode('utf-8')+"\n") #
            print("wrote data")
            successRatio += 1
            linksLeft += 1
            print("[+] SUCCESS    " + str(keySize - linksLeft))
        except Exception, e:
            linksLeft += 1
            print("[-] FAILURE - " + str(e)+ " --- " + str(keySize - linksLeft)) 

    target.close()
    print("Success Ratio : "+str(successRatio)+"/"+str(keySize))

if __name__=="__main__":
    buildMapping(sys.argv[1])
```
The boilerpipe python wrapper ended up extracting text from a little over 78% of the hyperlinks I passed through to it.  I have no idea if this is because
the hyperlinks I sent it were 78% articles and the rest video's or pictures but I'm fairly happy with that.  I ended up with around 600 articles of text I could use
to extract data from.


Next we clean up the corpus and the result can be found [here](https://raw.githubusercontent.com/dhurley14/pmarcaRTS/master/src/actual/newcorpus.txt) (The file is 
3.5 MB of just text FYI).
We now have a corpus we can use to build our LDA model.  

###LDA model results
I used the [gensim](http://radimrehurek.com/gensim/index.html) package which has an implementation of the LDA algorithm and a very nice tutorial on how to utilize it.  After building the model I got the top ten (and [16](https://github.com/dhurley14/pmarcaRTS/blob/master/src/actual/tmp/16topics.txt) after reading [this](http://a16z.com/2015/01/22/16-things/)) topics Marc Andreessen retweets the most.  After looking at the top words for each topic ([which can be viewed here](https://github.com/dhurley14/pmarcaRTS/blob/master/src/actual/tmp/10topics.txt)), I came up with the following list of topics (in no particular order):

#### 1. World Issues (economic & political)

#### 2. American Military (editorialized, sorry :(

#### 3. Healthcare

#### 4. Venture Capital

#### 5. Bitcoin Blockchain

#### 6. ??? 

#### 7. European Economic Policy

#### 8. Psychology

#### 9. Oil Crisis

#### 10. Gay rights

To generate a more comprehensive set of topics, it certainly would've been nice to have acquired more than approximately 700 articles.  I also do not fully understand how the LDA algorithm works so I'm sure by playing around with parameters in the LDA model I could more closely approximate the topics. I also believe that some of these topics seem far fetched because pmarca was probably only tweeting about a couple of these topics at the time. In addition to this, my sample size was limited to the most recent 3200 tweets, so whatever topics are there are the most commonly seen topics from pmarca's last 3200 tweets. Soon will be part 2 of this post, where I examine the sentiment of the tweets associated with these topics.  Until next time!
