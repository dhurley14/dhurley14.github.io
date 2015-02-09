---
layout: post
title: "Introduction to the Leontief IO Model "
date: 2015-02-08 22:03:52 -0500
comments: true
categories: [Linear Algebra, numpy, scipy]
---

##Background Motivation
Oil prices have been falling dramatically.  Oil prices are now at 40% of what they started FY 2014 off.
The fall in oil prices has been a result of increasing shale oil production in the United States and 
softening global demand.  Falling oil prices are part of a global phenomenon of deflation.  
The United States is far off the Federal Reserve's goal of 2% inflation (currently up 0.7% in December 2014 from a year earlier).
Being able to predict demand is imperative to all sectors of an economy.  In this post I will introduce the
Leontief IO model and examine just how accurate it can be at predicting demand using historical data from the [BEA](http://bea.gov/industry/io_annual.htm)

This post will contain an introduction to the components of the model.  I will present a basic example using fictional data.
I will then present a model utilizing real world data.  In a future post I will build a new model that can be used to predict values of 
sector production $$\hat{x}$$ using statistics and the Fourier transform.

<!--more-->

##Enter the Leontief Input - Output Model
[The Leontief Input - Output model](https://en.wikipedia.org/wiki/Input%E2%80%93output_model#Basic_derivation) 
amounts to breaking down an economy into it's constituent parts so that firms can project demand for their specific sector of the economy.  
Say an economy produces goods and services, and those goods and services also consume other goods and 
services (we'll call this the intermediate demand).  The rest of the goods and services produced go to 
consumers (we'll call this the final demand).  We can represent this relationship with an equation.  The following model 
is a simplified version of more accurate models used globally. 


$$
\hat{x} = C\hat{x} + \hat{d}
$$ 

where $$\hat{x}$$ is a vector containing the total amount of units produced, per sector; $$C\hat{x}$$ is the intermediate demand (i.e. the demand
the Services industry has for all units produced by all industry's) and $$\hat{d}$$ is the final demand (i.e. the consumer). 
Assume there are three parts to a country, say, MadeUpLand's economy.  In MadeUpLand there is Services, Manufacturing and Agriculture.
Our rates for units consumed per unit of output per industry can be represented by the table below..

<table>
  <tr>
    <td>Purchased From | </td><td>Manufacturing | </td><td>Agriculture | </td><td>Services</td>
  </tr>
  <tr>
    <td>------------------------ | </td><td>----------------------- | </td><td>----------------- | </td><td>--------------------</td>
  </tr>
  <tr>
    <td>Manufacturing</td><td>.45</td><td>.35</td><td>.15</td>
  </tr>
  <tr>
    <td>Agriculture</td><td>.15</td><td>.25</td><td>.05</td>
  </tr>
  <tr>
    <td>Services</td><td>.05</td><td>.05</td><td>.25</td>
  </tr>
</table>


We can remove the headings of this table which will yield us our matrix $$C$$..


$$
C = \begin{bmatrix}0.45&0.35&0.15\\0.15&0.25&0.05\\0.05&0.05&0.25\end{bmatrix}
$$


Assuming our economists are able to accurately predict the number of units we can expect our consumers $$d$$ to consume, say..


$$
\hat{d} = \begin{bmatrix}7\\18\\26\end{bmatrix}
$$

We could then attempt to estimate the number of units our economy must produce, $$\hat{x}$$, in each sector to satisfy the demand from
our consumers $$\hat{d}$$ and the demand of other industries in our economy $$C$$.  The following transforms our equation to satisfy this constraint.


$$

(I - C)\cdot\hat{x} = \hat{d}
$$

$$

\vdots

$$


$$


\hat{x} = (I - C)^{-1} \cdot \hat{d}

$$


Substituting our variables with the actual values yields the following result..

$$

\hat{x} = \left(\begin{bmatrix}1&0&0\\0&1&0\\0&0&1\end{bmatrix} - \begin{bmatrix}0.45&0.35&0.15\\0.15&0.25&0.05\\0.05&0.05&0.25\end{bmatrix}\right)^{-1} . \begin{bmatrix}7\\18\\26\end{bmatrix}

$$


$$

\vdots

$$

$$

\hat{x} = \begin{bmatrix}46.59\\35.9962\\40.1724\end{bmatrix}

$$


##How good is this model? - Working with BEA data

So doing all the matrix operations on the Leontief model is pretty basic stuff.  How do we make this interesting?  Let's use real data
from the BEA and test that the above mathematical steps are correct.

I grabbed data from the [BEA input output accounts data](http://bea.gov/industry/io_annual.htm).  After about a half hour of examining the data,
I was able to figure out what parts I needed to computer the appropriate values to be used in the model.  First I will go over our data set.

The data set contains total industry output vectors ($$\hat{x}$$).  I will reiterate now, the point of this post is to introduce a real world
data set and attempt to prove that the Leontief model is correct, and that the mathematical steps above are reproducible on real world data.
This won't be a mathematical proof. Considering we are given $$\hat{x}$$, we must compute values for $$\hat{d}$$ and $$C$$.  

To compute values for $$C$$ (this algorithm will be imperative in the next post when we build our extrapolative model) I introduce
a new matrix $$U$$.  $$U$$ contains the units consumed by the sectors of the economy.  $$C_{i,j}$$ will be computed as 

$$
\frac{U_{i,j}}{\hat{x}_{j}}
$$

$$\hat{d}$$ will be defined as the difference between the total units the economy produced per sector, $$\hat{x}$$, and the amount each sector consumed, $$C \cdot \hat{x}$$.

The data set contains total industry output vectors by sector for each year from 1997 - 2013.  For this example I will use only the 2013 data to
build a kind of proof of concept that the Leontief model is correct.  The industry output vector will be represented as $$\hat{x}$$.  
I will introduce a new matrix $$U$$ which will contain the units consumed by industry (the input to each sector).  I will then utilize
$$U$$ to compute the consumption rates for which $$C$$ will contain.

I acquired the data for $$\hat{x}$$ by just copying the column Total Industry Output from the "Make" excel file into a Python list.
To build $$U$$ I parsed the file [fixedData.csv](https://github.com/dhurley14/LeontiefIOModel/blob/master/src/betterData.csv) which 
is a modified version of the data from the BEA ["Use"](http://bea.gov/industry/xls/io-annual/IOUse_Before_Redefinitions_PRO_1997-2013_Sector.xlsx) excel file.


```Python
import csv
import os
import sys
from scipy import linalg
import numpy as np
import math

""" Create a script that will wrangle and format our IO data
    and then put it into a scipy matrix, which we can then save
    and use later.

"""


def save_table(someFile, theDelimiter=',',startPoint=2,endPoint=17):
    """ loads data from csv file
    into a numpy matrix and saves
    the matrix data

    someFile csv: a csv file containing our matrix data.
    theDelimiter char: character to split columns of data, set to ','.
    
    """
    x = [1.0/481026,1.0/580284, 1.0/496708, 1.0/1206919, 1.0/5787100, 1.0/1458137, 1.0/1392835, 1.0/1024517, 1.0/1223897, 1.0/5133698,
        1.0/3691586,1.0/2564883,1.0/1218544,1.0/740624,1.0/2706124]
    afile = open(someFile, 'rb')
    data = csv.reader(afile, delimiter=theDelimiter)

    with open('betterData.csv','wb') as csvfile:
        target = csv.writer(csvfile, delimiter=',')
        for row in data:
            print row[startPoint:endPoint]
            target.writerow(row[startPoint:endPoint])

    data = np.genfromtxt('betterData.csv',dtype=None,delimiter=theDelimiter,skip_header=1)
    #for row in data:
    #    print row[2:]
    U = [row for row in data]
    print U


```
outputs this..

`[array([ 94325,    160,      0,   1939, 275162,   1584,   3193,     92,
            1,     49,   2030,   1094,   7517,     81,   2663]), 
            array([  2473,  54705,  33987,  10292, 568596,     50,     57,   2015,
          301,   4060,   1341,    457,   1163,    454,  18612]), 
          array([ 4168,  3563,  2191,  2192, 55702,  4641, 10797,  5912,  4326,
       68779,  9216, 25255, 10772,  3593, 29092]), ...`

Now that we have our U, we can compute C and d.  Notice I set the variable `x` above to be 1.0/val instead of the actual
output values because in Python multiplication is 25% faster than [division](http://stackoverflow.com/a/226563).

```
new-host-2:~ devin$ time python -c 'for i in xrange(int(1e8)): t=12341234234.234 / 2.0'

real	0m12.162s
user	0m12.131s
sys	0m0.018s
new-host-2:~ devin$ time python -c 'for i in xrange(int(1e8)): t=12341234234.234 * 0.5'

real	0m9.270s
user	0m9.244s
sys	0m0.015s
```

Here is the python code that computes $$C$$.

```Python
# table2 will contain the rates
    # at which each sector consumes.
    table2 = []
    for row in U:
        i = 0
        row2 = []
        for item in row:
            item = item*x[i]
            row2.append(math.fabs(float(item)))
            i += 1
            #print row2
        print row2
        table2.append(row2)
    print "\n\ntable2 after loop"
    print table2

    # transform table2 into a useable format
    # this will be C
    C = np.matrix(table2)
    C = C.astype(float)
    np.save('myMatrix',C)
    print C
```

Next we will compute $$\hat{d}$$

```Python
print "printing vec now.."

    # put x back into it's original form, not 1.0/val
    vecx = np.matrix([[1.0/float(item)] for item in x]) 
    print vecx
    B = C.dot(vecx)
    #B = np.load('myMatrix.npy')
    print("\nB:")
    print B
    print("\n our vector d")
    vecd = vecx - B
    print vecd
```

And finally our equation $$\hat{x} = (I - C)^{-1} \cdot \hat{d}$$

```Python
    print ("\nNow we will compute our function to find vecx")
    print("computing x=(I-C)^-1 . d")
    
    # create identity matrix by computing A dot inverse(A)
    I = A.dot(A.I)

    prod = (I - A)
    result = prod.I
    print result.dot(vecd)
    print "The above should be equal to vecx"
    print vecx
    
```

The resulting $$\hat{x}$$ = `[[  481026.00000002]
 [  580284.00000003]
 [  496708.00000001]
 [ 1206919.00000001]
 [ 5787100.00000011]
 [ 1458137.00000003]
 [ 1392835.00000001]
 [ 1024517.00000002]
 [ 1223897.00000001]
 [ 5133698.00000016]
 [ 3691586.0000002 ]
 [ 2564883.        ]
 [ 1218544.00000002]
 [  740624.00000001]
 [ 2706124.00000001]]`

 which, disregarding rounding numerical approximation errors, is exactly our precomputed total industrial output vector
 `[[  481026.]
 [  580284.]
 [  496708.]
 [ 1206919.]
 [ 5787100.]
 [ 1458137.]
 [ 1392835.]
 [ 1024517.]
 [ 1223897.]
 [ 5133698.]
 [ 3691586.]
 [ 2564883.]
 [ 1218544.]
 [  740624.]
 [ 2706124.]]`

 Next time we will use this algorithm of computing $$C$$ and $$\hat{d}$$ over all of our historical data,
 perform some statistical analysis on this and then try to extrapolate values for $$\hat{x}$$.  Until next time!
