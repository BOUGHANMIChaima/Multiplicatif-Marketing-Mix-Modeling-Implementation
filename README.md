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
### Model Architecture
The model is built in a stacked way. Three models are trained: <br/>
Control Model <br/>
Marketing Mix Model <br/>
Diminishing Return Model <br/>
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/model_.jpg)
## 2.1 Control Model / Base sales Model
Goal: <br/>
predict base sales (X_ctrl) as an input variable to MMM, this represents the baseline sales trend without any marketing activities.
## 2.2 Marketing Mix Model
Goal: <br/>
Find appropriate adstock parameters for media channels; <br/>
Decompose sales to media channels' contribution (and non-marketing contribution). <br/>
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/eq6.jpg)  <br/>
#### Distribution of Media Coefficients
red line: mean, green line: median
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/graph_3.jpg)  <br/>
#### Decompose sales to media channels' contribution
Each media channel's contribution = total sales - sales upon removal of the channel
In the previous model fitting step, parameters of the log-log model have been found: <br/>
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/eq_7.jpg)  <br/>
Plug them into the multiplicative model: <br/>
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/eq8.jpg)  <br/>
RMSE (log-log model): 0.04977
MAPE (multiplicative model): 15.71%
## 2.3 Diminishing Return
Goal: for each channel, find the relationship (fit a Hill function) between spending and contribution, so that ROAS and marginal ROAS can be calculated.  <br/>
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/eq9.jpg)  <br/>
### Calculate overall ROAS and weekly ROAS
Overall ROAS = total media contribution / total media spending <br/>
Weekly ROAS = weekly media contribution / weekly media spending <br/>
## 3. Results & Marketing Budget Optimization
### Media Channel Contribution
80% sales are contributed by non-marketing factors, marketing channels contributed 20% sales.
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/graph4.jpg)
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/graph5.jpg)
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/graph6.jpg)
![](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/graph7.jpg)
