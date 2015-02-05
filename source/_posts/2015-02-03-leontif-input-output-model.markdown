---
layout: post
title: "Leontief input output model"
date: 2015-02-03 22:03:52 -0500
comments: true
categories: [Linear Algebra, numpy, scipy]
---

##Background Motivation
Oil prices have been falling dramatically.  Oil prices are now at 40% of what they started FY 2014 off.
The fall in oil prices has been a result of increasing oil production in the United States and 
softening global demand.  Falling oil prices are part of a global phenomenon of deflation.  
The United States is far off the Federal Reserve's goal of 2% inflation (currently up 0.7% in December 2014 from a year earlier).

<!--more-->

##Enter the Leontief Input - Output Model
[The Leontief Input - Output model](https://en.wikipedia.org/wiki/Input%E2%80%93output_model#Basic_derivation) 
amounts to breaking down an economy into it's constituent parts.  It can be used to help project demand for specific parts of the economy.  
Say an economy produces goods and services, and those goods and services also consume other goods and 
services (we'll call this the intermediate demand).  The rest of the goods and services produced go to 
consumers (we'll call this the final demand).  We can represent this relationship with an equation.  The following model 
is a simplified version of more accurate models used globally. 


$$
\hat{x} = C\hat{x} + \hat{d}
$$ 

where $$x$$ is a vector containing the total amount of units produced, per sector; $$Cx$$ is the intermediate demand (i.e. the demand
the Services industry has for all units produced by all industry's) and $$d$$ is the final demand (i.e. the consumer). 
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

We could then attempt to estimate the number of units our economy must produce in each sector to satisfy the demand from
our consuers $$\hat{d}$$ and the demand of other industries in our economy $$C$$ to predict $$\hat{x}$$


$$

(I - C)\hat{x} = \hat{d}
$$

$$

\vdots

$$


$$


\hat{x} = (I - C)^{-1} \cdot \hat{d}

$$


Substituting our variables with the actual values yields the following result..

$$

\hat{x} = \left(\begin{bmatrix}1&0&0\\0&1&0\\0&0&1\end{bmatrix} - \begin{bmatrix}0.45&0.35&0.15\\0.15&0.25&0.05\\0.05&0.05&0.25\end{bmatrix}\right)^{-1} \begin{bmatrix}7\\18\\26\end{bmatrix}

$$


$$

\vdots

$$

$$

\hat{x} = \begin{bmatrix}46.59\\35.9962\\40.1724\end{bmatrix}

$$

##Python numpy library..

```Python

here will be some python code explaining how to solve lin alg issues
with python

```






