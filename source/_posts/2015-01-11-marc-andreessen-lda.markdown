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

Now that I have my authentication and a way to get the user's timeline (note that `count=200` is the max amount of tweets from a user's timeline you can receive before having to paginate, which is what I will show next).

To get the next oldest 200 tweets, and so on and so forth until I max out at 3200, I had to loop through get_tweets(), setting the `tweetID` parameter to the ID of the last tweet that I received in the previous API call.  I do this 16 times in total.  I then write all of the tweets to a file called `pmarca_rts_append.txt`.

``` Python
def output_all_rts():
    user_timeline = get_tweets()
    
    rts = [user_timeline[i]['text'].encode('utf-8') for i in xrange(len(user_timeline)) 
        if "RT " in user_timeline[i]['text']]
        
    for i in xrange(16):
        user_timeline = get_tweets(username='pmarca',anId=user_timeline[len(user_timeline)-1]['id'])
        [rts.append(user_timeline[i]['text'].encode('utf-8')) for i in xrange(len(user_timeline))
            if "RT " in user_timeline[i]['text']] #filter RT's
            
    #print len(rts)
    output_file = open("pmarca_rts_append.txt", "a+b")
    for line in rts:
        output_file.write(''.join(line)+"\n")
    output_file.close()


if __name__ == '__main__':
    output_all_rts()

```

Now I have all of pmarca's retweets (which turns out to be 1/2 of all of his tweets returned by the API).
Next is to build a python dictionary that will store the links from the retweets as well as the tweets themselves.  I need to store the tweets alongside the articles the tweet links to so that I can go back and send the article data linked in the tweet back through my LDA model and find the topics those tweets are associated with.  I can then perform sentiment analysis on that tweet, aggregate the sentiment per topic, and finish my analysis.

###Getting the URLs

The following grabs the urls from all the retweets
``` Python
import re, os, sys


tweetURLdict = {}
for line in open("pmarca_rts_append.txt").readlines():
    results = re.search("(?P<url>https?://t.co/[A-Za-z]{10})", line.strip()) # some urls are malformed so I was only able to extract perfect ones, hence {10}
    if results:
        #print results.group("url")
        tweetURLdict[results.group("url")] = str(line).replace("\xe2\x80\x99","'").replace("\xe2\x80\x98","'").replace("\xe2\x80\x9c", '"').replace("\xe2\x80\x9d",'"').replace("\xe2\x80\x93",'-').replace("\xe2\x80\x94",'--').replace("\xe2\x80\xa6",'...').replace("\xc3\xb6","o").replace("\xf0\x9f\x98\x8a",":)")

target = open('tweetdict.txt','a')
target.write(str(tweetURLdict))
print len(tweetURLdict)

```

Surprisingly, out of the approximately 900 RT's with links, nearly 800 of them were malformed when returned from the twitter API. Some looked like this `RT @natashakhanhk: A brasher generation taking over from established pro-democracy fighters portends more political struggle ahead http://t…`  I think this has to do with the fact that these were retweets.  When anyone retweets something I think Twitter stores the link data somewhere else, not displaying or truncating the actual hyperlink text so that more user text can be fit into a tweet.  Just a theory.  Anyways, I now had 108 properly formed links to deal with.  Still plenty to have fun with :)


In order to build my LDA model I needed to find a way to extract the body of the text from each article.  On the [mathandpencil](http://blog.mathandpencil.com/using-latent-dirichlet-allocation-to-categorize-my-twitter-feed/) blog, Joseph Misiti uses Diffbot to acquire the text body and I followed in his footsteps.  Diffbot gives each new signup a 14 day free trial with 10,000 daily calls, which was more than I needed at the time (although I wish I did do another sweep of pmarca's timeline this month so that I could at least double the amount of article links in my data).

First, I needed to strip out the URL's from the dictionary I built when I extracted the links from the tweets.

``` Python
    tweetDICTurls = ast.literal_eval(open("tweetdict.txt").read())
    urls = " ".join([u.strip().replace('"','') for u in tweetDICTurls.keys()])
```
###Building the corpus
Now that I have just the URLs, I can send them to Diffbot to extract the body of each article.  Diffbot makes this extremely simple with their API.

``` Python
DIFF_BOT_TOKEN = 'xxx'

def create_bulk_job(urls, name):
    """ urls will be whitespace separated values
    
    """
    apiUrl = 'http://api.diffbot.com/v3/article'
    params = dict(token=DIFF_BOT_TOKEN, name=name, urls=urls, apiUrl=apiUrl)
    response = requests.post('http://api.diffbot.com/v3/bulk',data=params)
    moarData = json.loads(response.content)
    # response from diffbot wrote data here..
    target = open('output_from_diffbot.txt','a')
    target.write(str(moarData))
    print moarData['jobs'][0]['downloadJson'] #this will print the link to the JSON where I can download the corpus.
```

Next we clean up the corpus and the result can be found [here](https://github.com/dhurley14/pmarcaRTS/blob/master/src/corpus.txt).
We now have a corpus we can use to build our LDA model.  

###LDA model results
I used the [gensim](http://radimrehurek.com/gensim/index.html) package which has an implementation of the LDA algorithm and a very nice tutorial on how to utilize it.  After building the model I got the top ten topics Marc Andreessen retweets the most (at least during the 3200 tweets ending in November 2014).  After looking at the top words for each topic ([which can be viewed here](https://github.com/dhurley14/pmarcaRTS/blob/master/src/bettertopics.txt)), I came up with the following list of topics (in no particular order):

####1. Venture Capital
####2. Miscellaneous
####3. Employment
####4. Labor issues
####5. Monetary policy
####6. Healthcare technology
####7. Web services
####8. Education 
####9. Inflation
####10. Global economics and finances.

To generate a more comprehensive set of topics, it certainly would've been nice to have not had to throw out 800 articles due to malformed hyperlinks.  I could probably make another call to the Twitter API and acquire another 100 articles from Marc Andreessen's timeline.  Unfortunately my free 14 day free trial with Diffbot is over.  Soon will be part 2 of this post, where I examine the sentiment of each topic using the text from the tweets.  Until next time!
