---
title: 5 Methods for Implementing Signal Standardization
tags:
  - Quant
date: 2024-08-23 14:40:22
sticky: 10
---

### Introduction
Updated on 26/9: Really surprised that my mentor texted me and told me that my normalized method has improved their factors' performances. Hence for safety concern, I decide to close method 5 from now on:）I guess later in the reading week, when I'm not that busy for my studying, I can spare some time working on some posts again.

The raw signals generated from different logics and data sources can vary greatly, with differing value ranges. To better compare these signals in later stages and facilitate their integration into machine learning models, signal standardization is necessary. In long-short strategies, the sign of the signal typically indicates whether to go long or short, while the magnitude of the signal determines the position size. Therefore, a range of [-1,1] (where all signal values are mapped to this interval) is selected to achieve this standardization goal.
The following sections will discuss several commonly used signal standardization methods and their applicability in different scenarios. For demonstration purposes, the momentum of the *** Index is chosen as the raw signal in the subsequent discussion.

- **Tools Used**: Python, Jupyter Notebook
- **Note**:  All implementation code and some expresstions are omitted from this report, and only the essential results are displayed.  
### Method 1: Converting Signals into Three Discrete Positions (-1, 0, 1)
This method is straightforward and forcefully limits the signals to just three positions: -1, 0, and 1. It's a simple yet intuitive way to standardize the signals by restricting them to these discrete levels.
The results are as follows:
**Note:** The following three charts will be presented for all subsequent methods:

1. Histogram of Signal Distribution: Visually represents the distribution of positions and allows assessment of whether the standardization meets expectations.
2. Cumulative Strategy Return: Compares the cumulative return of the constructed strategy against the ***'s cumulative return.
3. Backtesting Statistics: Includes key backtesting metrics such as ******, offering a quantitative evaluation of the strategy's performance.

![image.png](images/post2-1.png)

### Method 2: Applying Decay via Weighted/Exponential Moving Averages
Building on the basic three-position strategy (-1, 0, 1), this method introduces decay through weighted or exponential moving averages to reduce turnover. In Method 1, the strategy often led to frequent trading. By introducing decay, the goal is to lower the turnover rate. One approach to achieve this is by using weighted or exponential moving averages.
It's important to note that this method is highly sensitive to the starting point, as different initial data choices can lead to significant variations in the standardized signals. However, the moving average approach ensures that the signals are still mapped within the range of [-1,1].
Here, we focus on the weighted moving average approach, though the exponential moving average is similar and can be implemented using ewm().
In this example, a 5-period weighted moving average is applied, with weights assigned from distant to recent data as follows: weights = [*, *, *, *, *].
The results are as follows:
![image.png](images/post2-2.png)
After testing this method, it indeed reduces the turnover rate, but both return and Sharpe ratio also drop significantly. Therefore, for momentum signals, this decay method may not be ideal.
### Method 3: Using Tsrank to Process Raw Signals and Construct Positions
In this method, a 20-period lookback is used to calculate the moving rank of the raw signal, which then determines the current position. Additionally, the *** function directly returns values within the range of [-1,1], so no further standardization is needed.
The results are as follows:
![image.png](images/post2-3.png)
Howerver, according to the above statistics, the performance of this approach is not very satisfactory.
### Method 4: Using Z-score Processing to Construct Positions
The calculation formula of Z-score is as follows:![image.png](images/post2-4.png)
**Note:** After Z-score processing, the data satisfies mean=0 and std=1, which makes different datasets more comparable. To map the values to the range [-1,1], we applied an additional normalization step. Currently, Z-score is one of the more commonly used methods for signal processing.
The results are as follows:
![image.png](images/post2-5.png)
According to the backtesting statistics, Z-score processing significantly reduced the turnover rate. However, for momentum factors, the performance was not as ideal.
### Method 5: Using *** Transformations to Construct Positions
The results are as follows:
![image.png](images/post2-10.png)
Compared to the backtesting statistics of the previous methods, Method 5 achieves the highest Sharpe ratio and the lowest turnover rate, making it the best-performing approach.
