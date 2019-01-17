# High Frequency Trading using Support Vector Machines
This project implements a high frequency trading strategy that utilizes Support Vector Machines to capture statistical arbitrage in the pricing of Class A and Class C Google stocks. 

This idea is heavily inspired by the following paper: http://cs229.stanford.edu/proj2015/028_report.pdf by Jiayu Wu. This project includes modifications into an actual trading strategy that uses rebalancing. 

TODO: Further Model Tuning

TODO: Model Explanations

TODO: Results Graphs

TODO: Diagnostic Graphs on labelling

TODO: Labelled profit vs. Unlabelled profit

## Abstract
Pairs Trading is a market-neutral trading strategy that matches a long position with a short position in a pair of highly correlated instruments such as two stocks. 
The idea is to wait for a alteration of the spread of the two stocks and then long the underperforming instrument while simultaneously shorting the overperforming instrument. 
The position should be closed once the instruments return to statistical norms, earning the trader a profit. A good pair should share as many the same intrinsic characteristics as possible. 

A canonical example of pairs trading would be with Pepsi and Coca Cola. Barring any drastic shifts in the characteristics of either company, the returns of Pepsi and Coca Cola should be very correlated. 

![pairs](imgs/pairs_graph.jpg)

In our project, we will be searching for pairs trading opportunities between Class A and Class C Google stocks. Since all of the underlying fundamentals of both instruments are similar with the exception of voting rights, this pair makes a very good candidate to explore. 

However, since this pair of of instruments is obviously closely related, many other players in the market are ready to profit off of any egregious mispricings within this pair. It is not expected for any mispricings to be available for a long time. As such, we need to work in a timeframe that is as fast-paced as possible. That is why we will be using a fast-paced high frequency pairs trading strategy to capture statistical arbitrage within the pricing of GOOGL and GOOG as soon as they occur. In our project, we will be creating features from the ticker data that we feed into a machine learning model to predict profitable pairs trading opportunities. 

## Dataset
Our dataset contains snapshots of GOOG and GOOGL over the span of roughly 2 years (10/2016 - 11/2018) at the minute-level resolution. 
Our data was gathered from QuantQuote.com, a reputable dealer of fine-resolution ticker datasets. 
Our dataset had some missing values for both tickers. From QuantQuote's website: "Missing data for certain minutes generally means that no trades occurred during that minute." 
We handled this by removing entries from both datasets in which at least 1 of the tickers had a missing entry. The reasoning behind this was that is that pairs trading is impossible in such instances. This only occured for about 0.1% of our dataset. 

![goog_googl](imgs/goog_googl.png)

## Pairs Trading Model
The canonical pairs trading spread model looks like: 

![spreadmodel](imgs/pairs_model.gif)

where ![datat](imgs/datat.gif) represents the returns of instrument ![A](imgs/A.gif) at time ![t](imgs/t.gif) and 
![dbtbt](imgs/dbtbt.gif) represents the returns of instrument ![B](imgs/B.gif) at time ![t](imgs/t.gif). 

![xt](imgs/xt.gif) represents the spread of the returns at time ![t](imgs/t.gif). One of the assumptions of this model 
is the fact that this residual term is mean-reverting. We can assume this especially since the intrinsic characteristics 
of both securities in this instance are very similar. 

![drift](imgs/drift.gif) represents the drift term. Sometimes the spread begins to trend instead of reverting to the original mean. The drift term is one of the biggest factors of risk in pairs trading. For our problem, we assume that the drift term is negligible compared to the returns of either instrument. 

![beta](imgs/beta.gif) represents the hedge ratio which serves to normalize the volatility between the instruments. 
![beta](imgs/beta.gif) tells us how much of instrument ![B](imgs/B.gif) to long/short for every 1 unit of ![A](imgs/A.gif) to long/short. We will use the close prices to calculate percent returns and for the other features. Past work has considered assumed that ![beta](imgs/beta.gif) remains constant over the duration of the dataset. For our dataset, however, different behavior in the spread is apparent in 2017 and 2018. This might be due to some change of intrinsic characteristics of the instruments. For our solution, we will assume treat ![beta](imgs/beta.gif) as variable and recalculate it periodically. 

## Ornstein-Uhlenbeck Stochastic Process

