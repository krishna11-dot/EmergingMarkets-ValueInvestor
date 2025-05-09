# EmergingMarkets-ValueInvestor

## Project Overview
A sophisticated trading system for 8 emerging markets that outperforms buy-and-hold strategies while minimizing capital exposure through reduced holding periods. Using advanced time series forecasting techniques, technical indicators, and optimized trading strategies to achieve consistent returns across diverse market conditions.
Background
This project was developed for a portfolio investment company that focuses on emerging markets worldwide. The company's investment strategy is based on value investing principles rather than reacting to daily market volatility. The goal was to establish a robust intelligent system using stock market data to aid investment decisions based on the intrinsic value of companies.
Data

Historical stock price data from 8 emerging markets for 2020 Q1-Q4 (training) and 2021 Q1 (testing)
Each market has different operating days based on the country and exchange
Features include Open, High, Low, Close prices and Volume

## Approach & Methodology
1. Feature Engineering

Technical indicators: RSI (Relative Strength Index), Bollinger Bands (20-day period)
Volatility metrics and market trend indicators
Volume analysis for trade confirmation

2. Model Development

LSTM Neural Networks: Implemented with 5-7 day sequences to capture temporal patterns
Transformer Models: For capturing longer-term dependencies in price movements
Hyperparameter Optimization: Using genetic algorithms to find optimal model configurations

3. Trading Strategy

Developed signal generation based on:

Price position relative to Bollinger Bands
RSI thresholds (overbought/oversold conditions)
Confirmation from model predictions


Optimized parameters:

Buy threshold: 0.53%
Stop-loss: 7.5% (optimized from initial 5%)
Maximum holding period: 9 days



4. Optimization

Genetic algorithm for parameter tuning
Risk management through optimized stop-loss placement
Capital allocation strategy to maximize returns

## Results

Prediction accuracy across markets with MAPE of 1.9%-7.2%
Trading strategies outperforming buy-and-hold by up to 42%
Reduced average holding period to 9 days while maintaining profitability
Consistent performance across multiple markets with minimal losses
Enhanced risk management through technical indicator integration

## Technical Implementation

 Python, PyTorch for deep learning models
 Pandas, NumPy for data manipulation
 Scikit-learn for traditional ML models and evaluation
 Matplotlib, Plotly for visualization
 Custom backtesting framework for strategy evaluation

## Future Improvements

Integration of macroeconomic indicators
Market sentiment analysis from news sources
Regime detection for adaptive strategy switching
Multi-market correlation analysis for portfolio optimization
