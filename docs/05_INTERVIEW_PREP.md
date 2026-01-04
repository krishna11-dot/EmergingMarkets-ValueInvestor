# Interview Preparation Guide

This document prepares you for common interview questions about your stock prediction project.

---

## TABLE OF CONTENTS

1. [The 30-Second Elevator Pitch](#elevator-pitch)
2. [The 2-Minute Overview](#two-minute)
3. [The 5-Minute Deep Dive](#deep-dive)
4. [Common Technical Questions](#technical-questions)
5. [Common Design Questions](#design-questions)
6. [Common "Why" Questions](#why-questions)
7. [Tricky Follow-Up Questions](#tricky-questions)
8. [Red Flag Questions to Avoid](#red-flags)
9. [How to Handle "I Don't Know"](#dont-know)
10. [Questions to Ask the Interviewer](#ask-interviewer)

---

<a name="elevator-pitch"></a>
## 1. The 30-Second Elevator Pitch

**Question:** "Tell me about your stock prediction project."

**Answer:**
"I built an end-to-end stock prediction system using LSTM and Transformer models with attention mechanisms. It predicts next-day stock prices from 6-day sequences with 61 engineered features including technical indicators like Bollinger Bands, RSI, and volume metrics. The system generates trading signals, backtests three different strategies, and uses genetic algorithms to optimize parameters. I achieved 3-5% MAPE with 55-60% directional accuracy, and my optimized ML-based strategy outperformed buy-and-hold by 10% in backtesting."

**Key elements:**
- What: Stock prediction system
- How: LSTM + Transformer with attention
- Features: 61 engineered features
- Results: 3-5% MAPE, outperformed buy-and-hold
- Sophistication: Multiple strategies, genetic optimization

---

<a name="two-minute"></a>
## 2. The 2-Minute Overview

**Question:** "Can you walk me through your project in more detail?"

**Answer:**
"Sure. The project solves three problems: prediction, trading, and optimization.

For **prediction**, I start with daily OHLCV data from 2020-2021 across multiple stocks. I engineer 61 technical features including moving averages, RSI, MACD, Bollinger Bands, and volume indicators. The key insight was correctly transforming time series into tabular format using trend features like moving averages, volatility features like ATR, and volume confirmations. I use sliding windows of 6 days to predict day 7, training both LSTM and Transformer models with attention mechanisms to focus on relevant time steps.

For **trading**, I implemented three progressive strategies: basic Bollinger Bands as a baseline, enhanced Bollinger Bands with RSI and volume confirmation to reduce false signals, and ML-based predictions with stop-loss and take-profit for risk management. The enhanced strategy showed that combining ML with technical indicators works better than either alone.

For **optimization**, I use genetic algorithms to tune five strategy parameters: buy/sell thresholds, holding period, and risk management levels. This improved returns by 5-10% over default parameters.

The key finding was that different markets require different approaches. Some commodity-linked stocks perform better with rule-based strategies, while tech stocks respond well to ML predictions. My system automatically selects the best model and strategy per market."

**Time breakdown:**
- Problem (15 sec)
- Data & Features (30 sec)
- Models (30 sec)
- Strategies (30 sec)
- Results & Insights (15 sec)

---

<a name="deep-dive"></a>
## 3. The 5-Minute Deep Dive

**Question:** "Let's go deeper. Explain your system architecture."

**Answer:**
"I'll walk you through the five main components.

**Component 1: Data Pipeline**
I load multi-sheet Excel files with OHLCV data, parse dates, clean numeric values, and handle volume suffixes like '1.5M'. I add temporal features for seasonality, forward-fill missing trading days, and use Z-score outlier detection with winsorization to handle extreme values without losing data points. This creates a clean, continuous time series.

**Component 2: Feature Engineering**
This is where time series becomes tabular. I create 61 features across six categories: moving averages at multiple timeframes (5, 7, 10, 20 days), momentum indicators like RSI and MACD, Bollinger Bands with position and width features, volume features including ratios and on-balance volume, volatility measures using ATR, and market regime detection with ADX. Of these, 11 core features go into the models, while the rest support trading strategies. The variety of timeframes—5 days for short-term momentum, 20 days for medium trends—captures patterns at different scales.

**Component 3: Model Training**
I create 6-day sequences using sliding windows, scale features with MinMaxScaler fitted on training data only, and train two models. The LSTM has 3 layers with 64 hidden units and an attention mechanism that weights time steps by relevance. The Transformer uses positional encoding and multi-head attention with 4 heads to capture different pattern types simultaneously. Both use dropout for regularization and train for 50 epochs. I select the best model per market based on MAPE, typically achieving 3-5% error.

**Component 4: Trading Strategies**
I backtest three strategies on 2021 Q1 test data. Basic Bollinger Bands buys at the lower band and sells at the upper band—simple but noisy. Enhanced Bollinger Bands adds RSI below 30 and volume above average as confirmations, reducing false signals significantly. The ML-based strategy uses model predictions with a 0.5% threshold, 5% stop-loss, 10% take-profit, and 10-day holding limit. This triple safety net—prediction, stop-loss, and time limit—manages risk while capturing opportunities.

**Component 5: Optimization & Adaptation**
Genetic algorithms optimize the five strategy parameters using a population of 30 over 20 generations. The fitness function balances returns against penalties for excessive trading, long holdings, and large drawdowns. This typically improves performance by 5-10%. Critically, I discovered that some markets like commodity-linked stocks need longer Bollinger windows and perform better with rule-based strategies, so I implement market-specific overrides.

The final system is fully automated—load Excel, generate predictions, execute optimized strategies, and produce comparison reports. The hybrid approach of ML + technical indicators + genetic optimization + market-specific adaptation proved more robust than any single technique."

**Visual aid:** If possible, have the architecture diagram from [04_ARCHITECTURE_FLOW.md](04_ARCHITECTURE_FLOW.md) ready to reference.

---

<a name="technical-questions"></a>
## 4. Common Technical Questions

### Q: "How did you handle the temporal nature of stock data?"

**Answer:**
"Three key principles. First, strict temporal splitting—I train on 2020 and test on 2021 Q1, never shuffling data, to prevent future information from leaking into the past. Second, I use sliding windows to create sequences while maintaining order. Third, all features like moving averages only use past data; for example, the SMA on day 10 uses days 3-9, never day 11. This ensures realistic, deployable predictions."

---

### Q: "Why did you use both LSTM and Transformer?"

**Answer:**
"Different markets have different patterns. LSTMs excel at capturing sequential dependencies and trends, making them robust for trend-following stocks. Transformers with attention can model long-range dependencies and complex multi-factor interactions, helping with more volatile or pattern-rich stocks. Rather than assuming one model fits all markets, I train both and automatically select the best performer based on MAPE. This competition-driven approach gave me 2-3% better overall performance than using LSTM alone."

---

### Q: "What does the attention mechanism do?"

**Answer:**
"Attention lets the model focus on the most relevant time steps. For example, when predicting Tuesday's price, the most recent day (Monday) is typically more important than six days ago. Attention computes a weight for each day—maybe Monday gets 40% weight, last Tuesday 30%, and earlier days progressively less. The model then creates a weighted sum of the LSTM outputs. This improves accuracy because not all days in the sequence matter equally, and it provides interpretability—we can visualize which days the model focused on."

---

### Q: "How do you prevent overfitting?"

**Answer:**
"Multiple techniques. First, I use dropout with 20% for LSTM and 10% for Transformer, randomly dropping neurons during training to force robustness. Second, I limit training to 50 epochs rather than hundreds. Third, I use temporal split rather than random split, testing on completely unseen future data. Fourth, I use regularization inherently in the genetic algorithm's fitness function, which penalizes overly complex strategies. Finally, I validate that training and testing performance are close—if training MAPE is 1% but testing is 10%, that's a red flag for overfitting."

---

### Q: "Why MinMaxScaler instead of StandardScaler?"

**Answer:**
"MinMaxScaler transforms features to [0, 1], which works well with neural networks that use sigmoid and tanh activations in LSTMs. StandardScaler (mean=0, std=1) can produce negative values and unbounded ranges, which can slow down training. For neural networks on tabular-like time series data, MinMaxScaler is standard practice. The critical part is fitting on training data only and applying the same transformation to test data to avoid data leakage."

---

### Q: "How did you choose hyperparameters like 64 hidden units and 3 layers?"

**Answer:**
"I started with common defaults from the literature—64 hidden units and 3 layers are industry-standard for time series LSTMs. I tested a few variations (32 vs. 64 vs. 128 units, 2 vs. 3 vs. 4 layers) and found 64/3 provided a good balance between model capacity and training time without overfitting on my dataset size. With more time, I'd use Bayesian optimization for systematic hyperparameter tuning, but these defaults performed well."

---

### Q: "What's your train/test split ratio?"

**Answer:**
"It's approximately 80/20 by volume—240 trading days in 2020 for training, 60 days in 2021 Q1 for testing. But more importantly, it's a temporal split, not random. I intentionally chose 2020 for training because it includes the COVID crash, exposing the model to extreme volatility. Testing on 2021 Q1, a recovery period, validates that the model generalizes to different market regimes."

---

<a name="design-questions"></a>
## 5. Common Design Questions

### Q: "Why did you create 61 features if you only use 11?"

**Answer:**
"The 61 features serve multiple purposes. The 11 core features are direct model inputs—price, volume, moving averages, RSI, MACD, ATR, and Bollinger Band position. These capture price action, momentum, trend, and volatility. The remaining 50 features support the trading strategies—for example, Enhanced Bollinger Bands uses RSI thresholds, volume moving averages, and BB signals that aren't model inputs. Additionally, creating a broad set of features initially lets me explore what drives stock prices. I could narrow this down further, but these features are computationally cheap to calculate and provide flexibility for different strategies."

---

### Q: "Why three trading strategies instead of just using the best one?"

**Answer:**
"Progressive sophistication demonstrates understanding. Basic Bollinger Bands establishes a simple, interpretable baseline that's industry-standard. Enhanced Bollinger Bands shows I understand how to reduce false signals through confirmation—adding RSI for momentum and volume for conviction. The ML-based strategy leverages learned patterns but includes rule-based risk management. Having all three lets me compare approaches and prove that hybrid methods outperform pure ML or pure rule-based. Also, different strategies work better in different markets, so flexibility is valuable."

---

### Q: "How did you decide on 6-day sequences?"

**Answer:**
"Empirical testing and domain knowledge. Initially, I tried 5-day sequences but found the model was underpredicting—it didn't have enough context. Six days represents approximately one trading week plus one day, aligning with how traders think about weekly patterns. I tested 10-day sequences, but longer windows diluted the signal with noise. Six days provided the sweet spot between sufficient context and recent relevance. This is a hyperparameter that could be further optimized per market."

---

### Q: "Why genetic algorithm for optimization instead of grid search?"

**Answer:**
"I'm optimizing five continuous parameters simultaneously—buy threshold, sell threshold, holding period, stop-loss, and take-profit. Grid search would require testing every combination. Even with just 10 values per parameter, that's 10^5 = 100,000 combinations. At 1 second per backtest, that's over 24 hours. Genetic algorithms explore the space more efficiently by combining traits of good solutions and mutating them. With a population of 30 and 20 generations, I only test 600 parameter sets but find near-optimal solutions. GA also handles non-smooth fitness landscapes better than gradient-based methods."

---

### Q: "Why Bollinger Bands as the primary indicator?"

**Answer:**
"Three reasons: statistical foundation, adaptability, and industry acceptance. Bollinger Bands use 2 standard deviations, so touching a band is a statistically significant event—approximately 5% probability under normal distribution. The bands adapt to volatility—widening in chaotic markets to reduce false signals and narrowing in calm markets to capture opportunities. And they're an industry standard used globally, making my results comparable to established benchmarks. I then enhance them with RSI and volume to add momentum and conviction confirmation."

---

<a name="why-questions"></a>
## 6. Common "Why" Questions

### Q: "Why not use ARIMA or Prophet?"

**Answer:**
"ARIMA is limited to univariate, linear relationships. It would only use closing prices, ignoring volume, RSI, and other critical features. Stock markets exhibit non-linear dynamics like momentum bursts and volatility clustering that ARIMA can't capture. Prophet is designed for long-term seasonality (yearly patterns) and struggles with short-term daily predictions. Additionally, Prophet's failure in the Zillow case—where it worked historically but failed during regime change—shows its brittleness. Deep learning models can be retrained to adapt to new regimes and leverage multivariate features."

---

### Q: "Why MAPE as the primary metric?"

**Answer:**
"MAPE is scale-invariant, meaning I can fairly compare models across stocks with different price ranges—a $3 error on a $10 stock is different from a $3 error on a $1000 stock. MAPE captures this as 30% vs. 0.3%. It's also interpretable: '3% MAPE' means I'm off by 3% on average, which is easy to communicate to stakeholders. That said, I also track directional accuracy because getting the trend direction correct matters more for trading profitability than exact price prediction. A model with 4% MAPE and 60% directional accuracy is more valuable than one with 3% MAPE and 50% directional accuracy."

---

### Q: "Why stop-loss and take-profit?"

**Answer:**
"Risk management. Models can be wrong, and unexpected events like earnings surprises or geopolitical news can crash stocks. Without stop-loss, a single bad prediction could wipe out weeks of gains. For example, during the March 2020 crash, stocks without stop-loss fell 30-40%, while a 5% stop-loss exits early and preserves capital. Take-profit locks in gains before reversals. The 2:1 reward-to-risk ratio (10% take-profit, 5% stop-loss) follows trading best practices. These rules are safety nets that protect against model overconfidence and black swan events."

---

### Q: "Why market-specific parameters?"

**Answer:**
"Backtest analysis showed that one-size-fits-all underperforms. For example, South Africa - Impala Platinum, a commodity-linked stock, exhibited strong trending behavior. The default 20-day Bollinger window generated too many whipsaw signals. Increasing to 30 days and using rule-based strategies instead of ML improved returns by 8%. This taught me that markets have different characteristics—some trend, some mean-revert, some are driven by external commodity prices. Flexibility to adapt strategies per market is essential for real-world performance."

---

### Q: "Why 2020 for training when it includes COVID crash?"

**Answer:**
"It's actually an advantage. Training on 2020 exposes the model to extreme volatility—COVID crash in March, V-shaped recovery through summer, consolidation in fall. This diversity makes the model more robust than training on calm, predictable years. When I test on 2021 Q1 recovery, the model has already seen both crashes and recoveries, so it generalizes better. If I had trained only on pre-COVID data, the model would fail during volatility spikes."

---

<a name="tricky-questions"></a>
## 7. Tricky Follow-Up Questions

### Q: "If your model is so good, why aren't you trading with it?"

**Answer:**
"This is a learning project focused on understanding ML for time series, not a production trading system. There are several limitations I'd need to address before live trading: transaction costs and slippage aren't included in backtesting, the model doesn't account for liquidity constraints or market impact, it only handles batch processing rather than real-time data streams, and critically, past performance doesn't guarantee future results—markets change. Additionally, I'd need to implement proper risk management for a portfolio, not just individual stocks. That said, the project demonstrates I understand the full pipeline from data to predictions to strategy to evaluation."

---

### Q: "Your directional accuracy is only 60%. Isn't that barely better than random?"

**Answer:**
"60% directional accuracy, combined with risk management, is actually quite profitable. Here's the math: with 2:1 reward-risk (10% take-profit, 5% stop-loss) and 60% win rate, the expected value per trade is (0.6 × 10%) + (0.4 × -5%) = 6% - 2% = +4% per trade. Over 10 trades, that's 40% return. Also, even professional traders typically have 50-60% win rates—it's the risk-reward ratio that matters. A 40% win rate with 3:1 reward-risk is profitable, while a 70% win rate with 1:2 reward-risk loses money. My system balances win rate with risk management for positive expectancy."

---

### Q: "What if your best model changes daily? Do you switch models mid-stream?"

**Answer:**
"No, I select the best model per market for the entire test period based on initial validation metrics. Switching models daily would introduce instability and overfitting risk—you'd be chasing recent performance rather than trusting long-term patterns. In production, I'd retrain models periodically (e.g., monthly or quarterly) and reevaluate model selection then, but not on a day-to-day basis. The goal is strategic model choice, not tactical chasing."

---

### Q: "How do you know your genetic algorithm found the global optimum?"

**Answer:**
"I don't, and that's okay. Genetic algorithms are heuristic optimizers—they find good solutions efficiently but don't guarantee global optima. However, the fitness landscape for trading strategy parameters is complex and non-convex, meaning there's no closed-form solution anyway. GA explores broadly through mutation and crossover, reducing the risk of getting stuck in local optima compared to gradient descent. More importantly, even if there's a slightly better parameter set out there, my optimized parameters improve returns by 5-10% over defaults, which proves the approach adds value. Perfection isn't the goal; improvement is."

---

### Q: "Why didn't you use deep reinforcement learning for trading?"

**Answer:**
"Reinforcement learning (RL) is compelling but significantly more complex. RL requires defining a reward function, action space, and state representation, then training an agent through trial and error. For this project, I wanted to focus on core time series prediction and demonstrate understanding of supervised learning, feature engineering, and strategy design. RL would add another layer of complexity that might obscure these fundamentals. That said, RL is a great next step—I could treat buy/hold/sell as actions, portfolio return as reward, and use Deep Q-Learning or Policy Gradients. But starting with supervised learning + rule-based strategies is a more interpretable foundation."

---

<a name="red-flags"></a>
## 8. Red Flag Questions to Avoid

### DON'T SAY: "ChatGPT wrote most of the code."

**Why it's bad:** Suggests you don't understand what you're presenting.

**Instead say:** "I used ChatGPT to help debug and explore libraries, but I designed the architecture, chose the features, and understand every decision."

---

### DON'T SAY: "I just followed a tutorial."

**Why it's bad:** Implies no original thinking.

**Instead say:** "I learned from tutorials and research papers, then adapted the approach to multi-company stock data with market-specific parameters and genetic optimization."

---

### DON'T SAY: "I don't know how LSTM works internally."

**Why it's bad:** Shows lack of depth.

**Instead say:** "LSTMs use three gates—forget, input, and output—to selectively remember and forget information across time steps. I can walk through the math if you'd like."

---

### DON'T SAY: "This will definitely make money."

**Why it's bad:** Overpromising, naive about markets.

**Instead say:** "Backtesting shows promise, but I understand limitations—no transaction costs, potential overfitting, and that markets evolve. This demonstrates methodology, not a guaranteed trading system."

---

### DON'T SAY: "I used these features because everyone does."

**Why it's bad:** No critical thinking.

**Instead say:** "I used these features because they capture different market aspects—SMA for trend, RSI for momentum, ATR for volatility, and volume for conviction. Each serves a specific purpose in understanding price movements."

---

<a name="dont-know"></a>
## 9. How to Handle "I Don't Know"

### Strategy 1: Partial Knowledge

**Question:** "What's the exact mathematical formulation of the Transformer attention mechanism?"

**Answer:** "I understand the conceptual flow—queries, keys, and values are projected from the input, attention scores are computed as the dot product of queries and keys scaled by the square root of the dimension, then softmax normalizes these scores, and finally they weight the values. I'd need to look up the exact matrix notation, but I can explain how it lets the model attend to relevant positions."

---

### Strategy 2: Acknowledge and Redirect

**Question:** "What's the vanishing gradient problem in RNNs?"

**Answer:** "I know it's related to gradients diminishing during backpropagation through time, making it hard to learn long-term dependencies. That's actually why I used LSTMs—they address this with their gating mechanisms that help gradients flow. I'd need to review the exact math of how gradients decay, but the practical implication is that LSTMs outperform vanilla RNNs for sequences."

---

### Strategy 3: Honest Learning

**Question:** "Have you heard of Bayesian hyperparameter optimization?"

**Answer:** "I've read about it—it builds a probabilistic model of the objective function and uses that to select promising hyperparameters to test next, making it more efficient than random search. I haven't implemented it yet, but it's on my list for improving this project. I used genetic algorithms for strategy parameters and manual tuning for model hyperparameters."

---

### Strategy 4: Compare to What You Know

**Question:** "How does GRU differ from LSTM?"

**Answer:** "I know GRU is a simplified version of LSTM with fewer parameters—it combines the forget and input gates into a single update gate. I chose LSTM for this project because it's more expressive and standard in the literature, but GRU could be worth testing as it trains faster. I haven't done a head-to-head comparison on this dataset."

---

<a name="ask-interviewer"></a>
## 10. Questions to Ask the Interviewer

### About the Role

1. "What kind of time series problems does your team work on most frequently?"
2. "How do you balance model complexity with interpretability in your projects?"
3. "What does your typical ML project lifecycle look like from research to production?"

### About the Team

4. "What tools and frameworks does your team use for ML development?"
5. "How do you approach feature engineering in your domain?"
6. "Do you have examples of production ML models I could learn from?"

### About Technical Growth

7. "What areas of ML or data science do you think I should focus on developing further?"
8. "Are there specific techniques or methodologies your team uses that I should study?"
9. "What's the biggest technical challenge your team is currently facing?"

### About Project Feedback

10. "Based on what I've shown, what improvements would you suggest for this project?"
11. "Are there aspects of my approach that concern you or seem risky?"
12. "If you were to extend this project, what would be the next most valuable addition?"

---

## General Interview Tips

### Do:
- ✓ Use the architecture diagram as a visual aid
- ✓ Be honest about limitations and what you'd improve
- ✓ Connect technical decisions to business impact (e.g., "This improved returns by 10%")
- ✓ Show enthusiasm for learning from mistakes
- ✓ Ask clarifying questions if you don't understand the interviewer's question

### Don't:
- ✗ Memorize answers word-for-word (sound robotic)
- ✗ Pretend to know things you don't
- ✗ Get defensive about design choices
- ✗ Overuse jargon without explaining
- ✗ Focus only on what worked; talk about what you learned from failures too

---

## Practice Presentation Structure

**Recommended Flow:**

1. **Hook (10 seconds):** "I built a stock prediction system that outperformed buy-and-hold by 10%."

2. **Problem (20 seconds):** "Stock markets are hard to predict. I wanted to learn if ML could generate profitable trading signals."

3. **Approach (60 seconds):** Walk through data → features → models → strategies → optimization.

4. **Results (20 seconds):** Key metrics (3-5% MAPE, 60% directional accuracy, 25% returns).

5. **Learnings (30 seconds):** "Most important insight: different markets need different approaches. Also learned the importance of risk management and how to balance ML with domain knowledge."

6. **Questions:** "I'm happy to dive deeper into any part—data pipeline, model architecture, trading strategies, or results."

---

**Final Advice:**

The best interview answer is the **honest** one. If you followed a tutorial, say so but explain what you learned and how you extended it. If you made mistakes, share them and what you learned. If you don't know something, say "I don't know the exact details, but here's what I understand..."

Interviewers value **authentic learning and growth** far more than someone who pretends to know everything.

Good luck!
