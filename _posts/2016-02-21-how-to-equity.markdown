---
layout: post
title: "How To Equity"
date: 2016-02-21 14:36:29 -0500
comments: true
categories: [startups, equity, algebra]
---

## Hark! An Angel!

So recently I was asked by a close friend if I could help him understand his total compensation package.  The problem was how to project how much equity he needed in order to make back the money he was missing out on based on a market rate salary.  I ended up generalizing his situation and felt it might help others out if I posted the solution here.  So here is my explanation on how to calculate how much equity you (as an early startup employee) will need in order to make up for a loss in salary, assuming the startup cannot afford to pay you a market-rate salary.


Being purely logical, the output of this equation is what you should expect.  Most founders will not offer you this but this is a great tool to help get a 
general understanding of how much money you would need to make back on a liquidation event at your startup up when you take on an under market salary in exchange for equity.

<!-- more -->

After doing an explicit example (with a bit of help from a stranger on the Internet) I generalized the specific outcomes so that it could help others who find themselves in similar situations.  YMMV.

## How to 101: Market Rate Salary

Let's say you're interviewing at a startup and the company offers you some salary POST-TAX which we will call $$s_{0}$$, and stock options.  Given that salary 
($$s_{0}$$), how do you come up with an appropriate amount of options that will givey you a return on the investment of an undermarket salary along 
with the risk of joining an early stage (pre series A) startup?  

Looking at your offered salary of $$s_{0}$$, let's say a market salary for your position (POST-TAX) is actually $$s_{m}$$.  Feel free to insert numbers as we go along or follow along until the end of the post where we reach our final model from basic principles.
Assuming you work at this startup for $$N$$ years you will give up approximately...  

$$
[(s_{m} - s_{0}) \cdot N]
$$

in salary.  This step is important, so take some time and really try to simulate just how much salary you are giving up.  Your market rate should jump every year
 or so as the rate at which you learn at a startup is exponentially faster than at BigCo.  
So to figure out how much equity you would need to make up for that loss of salary, you would just take that number, divide it by the startup's optimal outcome,
which we will call $$\Omega$$ (say the company sells for 18 Billion `USD`, $$\Omega \equiv 18\,Billion$$), and now you're done!

... Except you probably haven't filed an 83b, subject to [AMT](https://en.wikipedia.org/wiki/Alternative_minimum_tax) which in the U.S. is approximately 35% so you'll want to account for that in your calculation

$$
\frac{[(s_{m} - s_{0}) \cdot N] \cdot (1.35)}{\Omega}
$$

You should also consider the strike price for these options as well, as you will need to spend your post-tax salary to purchase those, and include some dilution factor (a conservative value is 25% over 4 years, which is 6.25% per year so you'll need to calculate that by hand to really figure out what constant to use.  Let's up this value from $$1.35$$ to $$1.60$$ to help insulate us from this.  Now that we know what we need AT EXIT, we're all set? Right? Right?!?! 

## Exit to the Right of the Aircraft

So given our optimal outcome $$\Omega$$ we need to calculate the risk of not hitting that target.  Instead of using this pure value we should weigh it according to the probability of $$\Omega$$ actually occurring.  Our expected outcome will now be $$p(\Omega) \cdot \Omega$$.  So our new equation for our necessary equity becomes...

$$
\frac{[(s_{m} - s_{0}) \cdot N] \cdot (1.60)}{p(\Omega) \cdot \Omega}
$$

This will give us a nice buffer in case we miss this optimal target.  HOWEVER THIS IS STILL JUST TO BREAK EVEN.
The idea is to get a nice upside for lowering your standard of living for $$N$$ years along with the increased stress and work necessary for helping to build a company.  So, let's double this value

$$
2 \cdot \left (\frac{[(s_{m} - s_{0}) \cdot N] \cdot (1.60)}{p(\Omega)\cdot\Omega}  \right )
$$

This will represent how much (logically) you would be within your right to ask for. 

To calculate how much this percent will represent after debt is paid down and taking into account the preferred shareholders stake (assuming this represents about 25% of the pie), I'm assuming 25% of the outcome  will go to preferred stock holders and other forms of debt, before it reaches ESOs.

$$
0.75 \cdot \left (2 \cdot \left (\frac{[(s_{m} - s_{0}) \cdot N] \cdot (1.60)}{p(\Omega)\cdot\Omega}  \right ) \right )
$$

This equation will approximate how much of the company you will truly own by the time your company exits.

### TL;DR


$$0.75 \equiv Percent\, common\, stock\, options\, represent\, out\, of\, total\, options\, pool. $$

$$2 \equiv Upside$$

$$s_{m} \equiv Market\, salary\, for\, your\, position.$$

$$s_{0} \equiv Salary\, offered.$$

$$1.60 \equiv AMT\, and\, 6.25%\, dilution\, /\, annum.$$

$$\Omega \equiv Outcome\, of\, Exit$$

$$p(\Omega) \equiv Probability\, of\, outcome.$$

$$
2 \cdot \left (\frac{[(s_{m} - s_{0}) \cdot N] \cdot (1.60)}{p(\Omega)\cdot\Omega}  \right ) \equiv How\, many\, points\, to\, ask\, for
$$

... and finally, what you can reasonably expect your points to represent out of the total $$\Omega$$ left over after debt and prefferred shareholders get their 
slice ...
$$
0.75 \cdot \left (2 \cdot \left (\frac{[(s_{m} - s_{0}) \cdot N] \cdot (1.60)}{p(\Omega)\cdot\Omega}  \right ) \right )
$$

## Conclusion

You should never join a startup because you want to be rich.  Join one because you will have responsibilities you would have to wait 10+ years to have; join one 
because you like the people that started the company;  join one because you will be forced to jump out of your comfort zone; join one because you want to test
yourself.  Do not join one if your dream is to get rich.  There are far easier and safer ways to do this.  However, there should be decent payout for the fact that you did take a risk, imbued some amount of increased stress, and possibly damaged a few relationships along the way.

The obvious objective is to secure a market salary in the first place.  At this point any options would simply be icing on the proverbial cake.  However sometimes 
this is not possible so you have to take a salary cut.  This post is designed to help you as an early startup employee prevent yourself from becoming [one](http://www.nytimes.com/2015/12/27/technology/when-a-unicorn-start-up-stumbles-its-employees-get-hurt.html?_r=0) of [these](http://blog.nawbo-sv.org/incentive-stock-option-horror-story/) other [guys](http://venturebeat.com/2012/02/27/a-classic-startup-horror-story-the-ma-bait-and-switch/)

I hope this helps some people out there with difficult decisions to make.

