# Multiplicatif-Marketing-Mix-Modeling-Implementation with PySTAN  
> Adapted from [sibylhe code](https://github.com/sibylhe/mmm_stan?tab=readme-ov-file) based on this [paper](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/46001.pdf).  

## 1. Introduction
Marketing Mix Model, or Media Mix Model (MMM) is used by advertisers to measure how their media spending contributes to sales, so as to optimize future budget allocation. ROAS (return on ad spend) and mROAS (marginal ROAS) are the key metrics to look at. High ROAS indicates the channel is efficient, high mROAS means increasing spend in the channel will yield a high return based on current spending level.
#### Procedures :  
1. Fit a regression model with priors on coefficients, using media channels' impressions (or spending) and control variables to predict sales;

2. Decompose sales to each media channel's contribution. Channel contribution is calculated by comparing original sales and predicted sales upon removal of the channel;

3. Compute ROAS and mROAS using channel contribution and spending.

### Intuition of MMM :
- Offline channel's influence is hard to track. E.g., a customer saw a TV ad, and made a purchase at store.
- Media channels' influences are intertwined.

![Customer journey](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/customer.png)

## 1.1 Multiplicative MMM
Since media channels work interactively, a multiplicative model structure is adopted: <br/> <br/> 
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/es_1.jpg) <br/> 
Take log of both sides, we get the linear form (log-log model): <br/>  <br/>  
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/eq2.jpg) <br/> <br/>
### Constraints on Coefficients : <br/>

1. Media coefficients are positive.  <br/>

2. Control variables like discount, macro economy, event/retail holiday are expected to have positive impact on sales, their coefficients should also be positive. <br/>

## 1.2 AdStock
Media effect on sales may lag behind the original exposure and extend several weeks. The carry-over effect is modeled by Adstock:  <br/>
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/eq3.jpg)  <br/> <br/>
L: length of the media effect <br/>
P: peak/delay of the media effect, how many weeks it's lagging behind first exposure <br/>
D: decay/retention rate of the media channel, concentration of the effect <br/>
The media effect of current weeks is a weighted average of current week and previous (L− 1) weeks. <br/>
#### Adstock with Varying Decay
The larger the decay, the more scattered the effect.  <br/>
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/graph_1.jpg) <br/>

## 1.3 Dminishing Return
After a certain saturation point, increasing spend will yield diminishing marginal return, the channel will be losing efficiency as you keep overspending on it. The diminishing return is modeled by Hill function: <br/>
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/eq5.jpg) <br/>
#### Hill function with varying K and S 
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/graph2.jpg) <br/>

## 2.  Model Specification & Implementation
### Data
Four years' (209 weeks) records of sales, media impression and media spending at weekly level.

### 1. Media Variables

Media Impression (prefix='mdip_'): impressions of 13 media channels: direct mail, insert, newspaper, digital audio, radio, TV, digital video, social media, online display, email, SMS, affiliates, SEM.
Media Spending (prefix='mdsp_'): spending of media channels.
### 2. Control Variables

Macro Economy (prefix='me_'): CPI, gas price.
Markdown (prefix='mrkdn_'): markdown/discount.
Store Count ('st_ct')
Retail Holidays (prefix='hldy_'): one-hot encoded.
Seasonality (prefix='seas_'): month, with Nov and Dec further broken into to weeks. One-hot encoded.
### 3. Sales Variable ('sales')
## 2.1 Control Model
## 2.2 Marketing Mix Model
## 2.3 Diminishing Return
