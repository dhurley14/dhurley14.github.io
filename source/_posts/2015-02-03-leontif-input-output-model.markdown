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
[The Leontief Input - Output model](https://en.wikipedia.org/wiki/Input%E2%80%93output_model#Basic_derivation) amounts to breaking down an economy into it's constituent parts.  It can be used
to help project demand for specific parts of the economy.  Say an economy produces goods and services, 
and those goods and services also consume other goods and services (we'll call this the intermediate demand).  
The rest of the goods and services produced go to consumers (we'll call this the final demand).  We can represent this relationship with
an equation.  The following model is a simplified version of more accurate models used globally. 


$$
x = Cx + d
$$ 

where $$x$$ is the amount produced, $$Cx$$ is the intermediate demand and $$d$$ is the final demand. 
Assume there are three parts to a country, say, MadeUpLand's economy.  In MadeUpLand there is Services, Manufacturing and Agriculture.
Our rates for inputs consumed per unit of output per industry can be represented by the table below..

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



$$
\frac{x+y}{y-z}
$$
