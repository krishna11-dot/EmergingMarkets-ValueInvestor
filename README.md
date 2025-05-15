# EmergingMarkets-ValueInvestor
## Project Overview
A sophisticated trading system for 8 emerging markets that outperforms buy-and-hold strategies while minimizing capital exposure through reduced holding periods. Using advanced time series forecasting techniques, technical indicators, and optimized trading strategies to achieve consistent returns across diverse market conditions.

## Background
This project was developed for a portfolio investment company that focuses on emerging markets worldwide. The company's investment strategy is based on value investing principles rather than reacting to daily market volatility. The goal was to establish a robust intelligent system using stock market data to aid investment decisions based on the intrinsic value of companies.

## Data
- Historical stock price data from 8 emerging markets for 2020 Q1-Q4 (training) and 2021 Q1 (testing)
- Each market has different operating days based on the country and exchange
- Features include Open, High, Low, Close prices and Volume

## Approach & Methodology
1. Feature Engineering
   - Technical indicators: RSI, MACD, Bollinger Bands with multiple timeframes
   - Volatility metrics (ATR, historical volatility) and market regime detection (ADX)
   - Multi-timeframe volume analysis (5, 10, 20-day)

2. Model Development
   - LSTM Neural Networks: Implemented with 6-7 day sequences and attention mechanism
   - Transformer Models: For capturing longer-term dependencies with positional encoding
   - Model comparison across markets with focus on MAPE and directional accuracy

3. Trading Strategy
   - Developed multiple strategies:
     - Standard Bollinger Bands
     - Enhanced Bollinger Bands with RSI and volume confirmation
     - Model-based prediction strategies
   - Market-specific approach selection based on historical performance
   - Optimized parameters using genetic algorithms:
     - Buy threshold: 0.18% to 0.94% (market-specific)
     - Sell threshold: -0.25% to -0.99% (market-specific)
     - Stop-loss: 2.1% to 9.2% (optimized per market)
     - Take-profit: 2.0% to 18.9% (optimized per market)
     - Holding period: 5 to 19 days (market-dependent)

4. Optimization
   - Genetic algorithm with custom fitness function balancing returns, trading frequency, and drawdowns
   - Market-specific parameter optimization
   - Adaptive strategy selection for diverse market conditions

## Results
- Transformer models outperformed LSTM across all markets with MAPE of 1.6%-7.1%
- Optimized strategies outperformed buy-and-hold in 5 of 8 markets, with best outperformance of 19.15%
- Reduced average holding period to 1.6-7.1 days while maintaining profitability
- Identified market-specific characteristics requiring tailored approaches
- Enhanced risk management through dynamic stop-loss placement

## Technical Implementation
- Python, PyTorch for deep learning models
- Pandas, NumPy for data manipulation
- Scikit-learn for evaluation metrics
- Matplotlib, Plotly for visualization
- Genetic algorithms for parameter optimization

## Future Improvements
- Integration of macroeconomic indicators
- Market sentiment analysis from news sources
- Regime detection for adaptive strategy switching
- Multi-market correlation analysis for portfolio optimization
