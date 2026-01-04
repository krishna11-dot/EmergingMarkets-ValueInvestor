# Quick Reference Guide

One-page summary of your stock prediction system for quick review.

---

## 🎯 30-Second Pitch

"I built an end-to-end stock prediction system using LSTM and Transformer models with attention mechanisms. It predicts next-day stock prices from 6-day sequences with 61 engineered features including Bollinger Bands, RSI, and volume metrics. The system generates trading signals, backtests three strategies, and uses genetic algorithms to optimize parameters. I achieved 3-5% MAPE with 60% directional accuracy, outperforming buy-and-hold by 10%."

---

## 📊 Key Numbers (Memorize These!)

| What | Value |
|------|-------|
| Features Created | 61 |
| Features Used in Models | 11 core features |
| Sequence Length | 6 days |
| Models | 2 (LSTM, Transformer) |
| Strategies | 3 (BB, Enhanced BB, ML-based) |
| Training Data | 2020 (~240 days) |
| Test Data | 2021 Q1 (~60 days) |
| MAPE | 3-5% |
| Directional Accuracy | 55-60% |
| Outperformance | +10% vs buy-and-hold |
| Stop-Loss | 5% default |
| Take-Profit | 10% default |
| Holding Limit | 10 days default |

---

## 🏗️ System Architecture (5 Components)

```
1. DATA → Load Excel, clean, fill gaps, detect outliers
2. FEATURES → Engineer 61 features (trend, volatility, volume)
3. MODELS → Train LSTM & Transformer, select best by MAPE
4. STRATEGIES → Backtest 3 strategies (BB, Enhanced, ML-based)
5. OPTIMIZE → Genetic algorithm tunes 5 parameters
```

---

## 📚 11 Core Features Used

| Feature | Purpose |
|---------|---------|
| Price, Open, High, Low | OHLC price action |
| Volume | Market participation |
| Volume_ratio | Unusual activity detection |
| SMA_7 | Short-term trend |
| RSI_7 | Momentum (overbought/oversold) |
| MACD | Trend strength |
| ATR_7 | Volatility |
| BB_position | Position within Bollinger Bands |

---

## 🧠 Models

**LSTM:**
- 3 layers, 64 hidden units
- Attention mechanism
- Dropout: 20%
- Good for: Sequential trends

**Transformer:**
- 2 layers, 4 attention heads
- Positional encoding
- Dropout: 10%
- Good for: Complex patterns

**Selection:** Best MAPE per market

---

## 📈 Three Trading Strategies

**1. Bollinger Bands (Baseline):**
- Buy: price ≤ lower band
- Sell: price ≥ upper band

**2. Enhanced BB:**
- Buy: BB_lower + RSI<30 + high volume
- Sell: BB_upper + RSI>70 + high volume

**3. ML-Based (Best):**
- Buy: predicted_change > 0.5%
- Sell: predicted_change < -0.5% OR stop-loss OR take-profit OR holding limit

---

## 🎯 Why Questions (Top 5)

**Q: Why two models?**
A: Different markets → different patterns. LSTM for trends, Transformer for complexity. Select best per market.

**Q: Why not ARIMA?**
A: ARIMA = univariate, linear. Stocks = multivariate, non-linear. Need LSTM/Transformer.

**Q: Why 6-day sequences?**
A: Empirical testing. 5 days underpredicted, 10+ diluted signal. 6 days = optimal context.

**Q: Why MAPE?**
A: Scale-invariant, interpretable as %, industry standard. Plus directional accuracy for trading.

**Q: Why stop-loss/take-profit?**
A: Risk management. Protects against model errors and black swans. 2:1 reward-risk ratio.

---

## ⚡ Key Insights

1. **Not all markets are equal** → Market-specific parameters improve 5-10%
2. **Volume confirms signals** → High volume = reliable signals
3. **Hybrid > Pure** → ML + technical indicators + risk management wins
4. **Direction > Precision** → 60% directional + 2:1 risk-reward = profitable
5. **Feature engineering critical** → Trend + volatility + volume features matter

---

## 🔧 Technical Concepts (Quick Definitions)

**LSTM:** Neural network with memory for sequences
**Transformer:** Attention-based model for sequences
**Attention:** Focus on most relevant time steps
**Sliding Window:** Create overlapping sequences
**MAPE:** Average % error (scale-invariant)
**Bollinger Bands:** Price ± 2 std deviations (mean reversion)
**RSI:** Momentum (0-100), <30 oversold, >70 overbought
**ATR:** Average daily price movement (volatility)
**Genetic Algorithm:** Evolution-based optimization
**Dropout:** Randomly ignore neurons (prevent overfitting)

---

## ✅ Strengths to Highlight

- End-to-end system (data → predictions → trading)
- Advanced techniques (attention, genetic algorithms)
- Comprehensive features (61 engineered)
- Multiple strategies (progressive sophistication)
- Risk management (stop-loss, take-profit)
- Market-specific adaptation (flexible)
- Proven outperformance (+10% vs baseline)

---

## ⚠️ Limitations to Acknowledge

- No transaction costs in backtest
- No real-time deployment
- Past performance ≠ future results
- Doesn't handle black swans
- Requires periodic retraining
- Limited to daily data (not intraday)

---

## 🎤 Sample Mini-Answers

**"How does attention work?"**
"Attention weights each time step by relevance. Recent days get more weight than older days. Improves accuracy by focusing on what matters."

**"How do you prevent overfitting?"**
"Dropout (20%), temporal split (train 2020, test 2021), limit epochs (50), validate train/test MAPE are similar."

**"Why genetic algorithm?"**
"5 parameters × 10 values = 100K combinations. GA finds good solutions in 600 tests via evolution. Efficient for complex spaces."

**"What's directional accuracy?"**
"Percentage of correct up/down predictions. More important than exact price for trading profitability."

**"What would you improve?"**
"Add news sentiment, real-time deployment, portfolio optimization, ensemble methods, Bayesian hyperparameter tuning."

---

## 📖 Pipeline (15 Steps)

1. Load Excel data
2. Parse dates, clean values
3. Fill trading gaps
4. Detect & handle outliers
5. Engineer 61 features
6. Split: 2020 train, 2021 test
7. Create 6-day sequences
8. Scale with MinMaxScaler
9. Train LSTM
10. Train Transformer
11. Select best model (MAPE)
12. Backtest 3 strategies
13. Optimize with genetic algorithm
14. Generate reports
15. Apply market-specific overrides

---

## 🚫 Don't Say

- "ChatGPT wrote it"
- "I just followed a tutorial"
- "This will make you rich"
- "I don't know how LSTM works"
- Vague "good performance"

---

## ✅ Do Say

- "I designed the architecture and understand every decision"
- "I learned from tutorials then adapted with market-specific parameters"
- "Backtesting shows promise but has limitations"
- "LSTMs use gates to selectively remember/forget information"
- Precise "3-5% MAPE, 60% directional, 10% outperformance"

---

## 📝 Interview Flow

1. **Hook:** "Outperformed buy-and-hold by 10%"
2. **Problem:** "Can ML generate profitable signals?"
3. **Approach:** Data → Features → Models → Strategies → Optimize
4. **Results:** 3-5% MAPE, 60% directional, 25% returns
5. **Learnings:** Market-specific, volume critical, hybrid wins
6. **Questions:** "Happy to dive deeper into any part"

---

## 🎯 Final Checklist

Before interview:
- [ ] Memorize 30-second pitch
- [ ] Know all key numbers
- [ ] Understand top 5 "why" questions
- [ ] Can draw architecture diagram
- [ ] Prepared 2-3 limitations
- [ ] Prepared 2-3 improvements
- [ ] Have 3 questions for interviewer

---

## 💡 Remember

**Goal:** Show you understand what you built, why you made decisions, and what you learned.

**Strategy:** Be honest, be specific, connect technical choices to results.

**Secret:** Admitting limitations and showing learning beats pretending to know everything.

---

**Good luck!** 🚀
