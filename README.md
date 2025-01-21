# Multiplicatif-Marketing-Mix-Modeling-Implementation with PySTAN  
Adapted from [sibylhe code](https://github.com/sibylhe/mmm_stan?tab=readme-ov-file) based on this [paper](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/46001.pdf).  

# 1. Introduction
Marketing Mix Model, or Media Mix Model (MMM) is used by advertisers to measure how their media spending contributes to sales, so as to optimize future budget allocation. ROAS (return on ad spend) and mROAS (marginal ROAS) are the key metrics to look at. High ROAS indicates the channel is efficient, high mROAS means increasing spend in the channel will yield a high return based on current spending level.
# Procedures :  
1. Fit a regression model with priors on coefficients, using media channels' impressions (or spending) and control variables to predict sales;

2. Decompose sales to each media channel's contribution. Channel contribution is calculated by comparing original sales and predicted sales upon removal of the channel;

3. Compute ROAS and mROAS using channel contribution and spending.

# Intuition of MMM :
Offline channel's influence is hard to track. E.g., a customer saw a TV ad, and made a purchase at store.
Media channels' influences are intertwined.

![Customer journey](https://github.com/BOUGHANMIChaima/Multiplicatif-Marketing-Mix-Modeling-Implementation/blob/main/customer.png)

# 1.1 Multiplicative MMM
# 1.2 AdStock
# 1.3 Dminishing Return
# 2.  Model Specification & Implementation
# Data
# 2.1 Control Model
# 2.2 Marketing Mix Model
# 2.3 Diminishing Return
