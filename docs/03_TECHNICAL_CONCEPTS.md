# Technical Concepts Explained Simply

This document explains all the technical jargon and concepts used in the project in simple, understandable terms. No PhD required!

---

## TABLE OF CONTENTS

### Models & Architectures
1. [LSTM (Long Short-Term Memory)](#lstm)
2. [Transformer](#transformer)
3. [Attention Mechanism](#attention)
4. [Sequence Modeling](#sequences)

### Time Series Concepts
5. [Time Series vs. Tabular Data](#time-series-vs-tabular)
6. [Sliding Window](#sliding-window)
7. [Stationarity](#stationarity)
8. [Data Leakage](#data-leakage)

### Technical Indicators
9. [Bollinger Bands](#bollinger-bands)
10. [RSI (Relative Strength Index)](#rsi)
11. [MACD (Moving Average Convergence Divergence)](#macd)
12. [ATR (Average True Range)](#atr)
13. [ADX (Average Directional Index)](#adx)
14. [Moving Averages (SMA, EMA)](#moving-averages)
15. [Volume Indicators](#volume-indicators)

### ML Concepts
16. [Overfitting vs. Underfitting](#overfitting)
17. [Feature Engineering](#feature-engineering)
18. [Scaling/Normalization](#scaling)
19. [Dropout](#dropout)
20. [Batch Size & Epochs](#batch-epochs)

### Evaluation Metrics
21. [MAPE (Mean Absolute Percentage Error)](#mape)
22. [MAE (Mean Absolute Error)](#mae)
23. [RMSE (Root Mean Squared Error)](#rmse)
24. [R² (R-Squared)](#r-squared)
25. [Directional Accuracy](#directional-accuracy)

### Trading Concepts
26. [Backtesting](#backtesting)
27. [Stop-Loss & Take-Profit](#stop-loss-take-profit)
28. [Win Rate](#win-rate)
29. [Drawdown](#drawdown)
30. [Buy & Hold](#buy-hold)

### Optimization
31. [Genetic Algorithm](#genetic-algorithm)
32. [Fitness Function](#fitness-function)
33. [Hyperparameters](#hyperparameters)

---

<a name="lstm"></a>
## 1. LSTM (Long Short-Term Memory)

**What it is:**
A type of neural network specifically designed for sequences (like time series, text, speech).

**Simple analogy:**
Imagine you're reading a book. Regular neural networks forget what they read two pages ago. LSTMs have a "memory" that remembers important information from earlier pages.

**How it works:**
```
Day 1: Price $100 → LSTM remembers "started at $100"
Day 2: Price $105 → LSTM remembers "upward trend"
Day 3: Price $110 → LSTM uses memory: "strong upward trend" → predicts Day 4 will be higher
```

**Three gates (you don't need to memorize this, but it's good to know):**
1. **Forget gate:** Decides what to forget from memory
2. **Input gate:** Decides what new information to remember
3. **Output gate:** Decides what to output based on memory

**Why it's good for stocks:**
- Remembers trends over multiple days
- Can distinguish between short-term noise and long-term patterns
- Industry-proven for time series

**Limitations:**
- Struggles with very long sequences (> 50 time steps)
- Treats all time steps somewhat equally (we add attention to fix this)
- Can be slow to train

---

<a name="transformer"></a>
## 2. Transformer

**What it is:**
A newer type of neural network that uses "attention" to process sequences.

**Simple analogy:**
Imagine you're a detective solving a case. Instead of examining clues in order (like LSTM), you immediately look at ALL clues and decide which ones are most important. That's attention.

**Key innovation:**
Transformers don't process data sequentially (Day 1 → Day 2 → Day 3). They look at all days at once and use attention to focus on relevant ones.

**How it's different from LSTM:**

| Aspect | LSTM | Transformer |
|--------|------|-------------|
| **Processing** | Sequential (one day at a time) | Parallel (all days at once) |
| **Memory** | Hidden state | Attention scores |
| **Speed** | Slower | Faster (parallel computation) |
| **Long sequences** | Struggles | Excellent |

**Components:**
1. **Positional Encoding:** Tells the model "Day 1 comes before Day 2"
2. **Multi-Head Attention:** Multiple "detectives" looking at clues from different angles
3. **Feedforward Layers:** Processes the attended information

**Why it's good for stocks:**
- Can capture long-range dependencies (e.g., "Monday's news affects Friday's price")
- Attention reveals which days/features matter most
- State-of-the-art for many sequence tasks

**Limitations:**
- Needs more data to train well
- More complex to understand
- Can overfit on small datasets

---

<a name="attention"></a>
## 3. Attention Mechanism

**What it is:**
A way for models to focus on the most relevant parts of the input.

**Real-world analogy:**
You're at a party with 50 people talking. You can't listen to everyone equally, so you pay attention to your friend's conversation (high attention) and tune out background noise (low attention).

**How it works in our project:**

**Example: Predicting Tuesday's price**
```
Input: [Wed, Thu, Fri, Mon, Tue] prices

Without Attention:
All days weighted equally: 20% each

With Attention:
Mon: 40% weight (most recent, very important)
Tue: 30% weight (yesterday, important)
Fri: 15% weight (relevant)
Thu: 10% weight (less relevant)
Wed: 5% weight (least relevant)
```

**The Math (simplified):**
```
1. Score each time step: How important is this day?
2. Softmax scores: Convert to probabilities (sum to 100%)
3. Weight the inputs: Important days contribute more
4. Sum weighted inputs: Final attended output
```

**Multi-Head Attention (in Transformer):**
Instead of one attention mechanism, use 4 (or more):
- Head 1 might attend to trends
- Head 2 might attend to volume spikes
- Head 3 might attend to volatility
- Head 4 might attend to recent prices

**Why it helps:**
- Improves accuracy: Model focuses on what matters
- Interpretability: We can see which days mattered
- Flexibility: Different patterns need different attention

**Visual:**
```
Attention Weights Heatmap:
Day:    Wed  Thu  Fri  Mon  Tue
Weight: ░░   ░░░  ░░░░ ████ ███
        5%   10%  15%  40%  30%

Prediction heavily relies on Mon and Tue (most recent days)
```

---

<a name="sequences"></a>
## 4. Sequence Modeling

**What it is:**
Using a sequence (ordered series) of data points to make predictions.

**Key concept:**
In sequences, ORDER MATTERS.
```
[1, 2, 3] ≠ [3, 2, 1]
[Mon $100, Tue $105] ≠ [Tue $105, Mon $100]
```

**Our approach:**
Use 6 consecutive days to predict day 7.

**Sliding Window Example:**
```
Data: [Day1, Day2, Day3, Day4, Day5, Day6, Day7, Day8]

Window 1: [Day1, Day2, Day3, Day4, Day5, Day6] → Predict Day7
Window 2: [Day2, Day3, Day4, Day5, Day6, Day7] → Predict Day8
Window 3: [Day3, Day4, Day5, Day6, Day7, Day8] → Predict Day9
```

**Input shape for models:**
```
[batch_size, sequence_length, features]
[16, 6, 11]
  ↑   ↑   ↑
  |   |   └─ 11 features (Price, Volume, RSI, etc.)
  |   └───── 6 days
  └───────── 16 sequences processed together
```

**Why sequences work for stocks:**
- Captures trends: "Price has been rising for 5 days"
- Captures patterns: "Wednesdays often have higher volume"
- Captures momentum: "Rapid price changes indicate volatility"

---

<a name="time-series-vs-tabular"></a>
## 5. Time Series vs. Tabular Data

**Tabular Data (Regular ML):**
```
| Age | Income | Credit Score | → Loan Approved? |
|-----|--------|--------------|------------------|
| 25  | 50K    | 700          | Yes              |
| 45  | 80K    | 650          | No               |

Rows are independent. Order doesn't matter.
```

**Time Series Data:**
```
| Date       | Price | Volume | → Next Day Price |
|------------|-------|--------|------------------|
| 2020-01-01 | 100   | 1000   |                  |
| 2020-01-02 | 105   | 1500   |                  |
| 2020-01-03 | 103   | 800    |                  |

Rows are dependent. Order MATTERS. Cannot shuffle.
```

**Key Differences:**

| Aspect | Tabular | Time Series |
|--------|---------|-------------|
| **Order** | Doesn't matter | Critical |
| **Shuffling** | OK | NOT OK (breaks temporal order) |
| **Train/Test Split** | Random split | Temporal split (past→future) |
| **Dependencies** | Rows independent | Rows dependent on previous rows |

**How to Convert Time Series → Tabular (Feature Engineering):**

This is what the mentor emphasized! We transform time-dependent data into features:

**Original (Time Series):**
```
Date: Jan 1, Price: 100
Date: Jan 2, Price: 105
Date: Jan 3, Price: 103
```

**Transformed (Tabular-like features):**
```
Date: Jan 3
- Price: 103                    (current value)
- Price_1d_change: -1.9%        (trend)
- SMA_7: 102                    (average)
- Volatility_7: 2.5             (volatility)
- Volume_ratio: 1.2             (volume activity)
```

**The three types of features (from mentor feedback):**
1. **Trend:** SMA, EMA, MACD → "Which direction is it going?"
2. **Volatility:** ATR, Bollinger Band width → "How uncertain is it?"
3. **Seasonality/Cycles:** Day of week, monthly patterns → "Are there regular patterns?"

---

<a name="sliding-window"></a>
## 6. Sliding Window

**What it is:**
A technique to create training samples from time series by "sliding" a window across the data.

**Visual Explanation:**
```
Full Data: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

Window Size = 3:
┌─────┐
│1 2 3│ → Predict 4
└─────┘

  ┌─────┐
  │2 3 4│ → Predict 5
  └─────┘

    ┌─────┐
    │3 4 5│ → Predict 6
    └─────┘

... and so on
```

**In our project (window=6):**
```
Day:     Mon Tue Wed Thu Fri Mon Tue
Price:   100 102 105 104 106 108 110

Sample 1: [100, 102, 105, 104, 106, 108] → Predict 110
Sample 2: [102, 105, 104, 106, 108, 110] → Predict (next day)
```

**Why use sliding windows?**
- Creates many training samples from limited data
- Maintains temporal order
- Standard technique for time series ML

**Overlap:**
Notice samples overlap (Day 2 appears in both Sample 1 and Sample 2). This is OK and expected.

---

<a name="stationarity"></a>
## 7. Stationarity

**What it is:**
Data is stationary if its statistical properties (mean, variance) don't change over time.

**Simple examples:**

**Stationary:**
```
Temperature in a climate-controlled room:
Jan: 70°F ± 2
Feb: 70°F ± 2
Mar: 70°F ± 2
(Mean and variance constant)
```

**Non-Stationary:**
```
Stock price over years:
2015: $50 ± $5
2020: $100 ± $10
2025: $200 ± $20
(Mean and variance increase)
```

**Why it matters:**
Many traditional models (like ARIMA) require stationary data.

**How to make data stationary:**
1. **Differencing:** Use price changes instead of absolute prices
   ```
   Instead of: [100, 105, 103]
   Use: [-, +5%, -1.9%]
   ```

2. **Log transformation:** Reduces exponential growth
   ```
   Instead of: [100, 200, 400]
   Use: [log(100), log(200), log(400)] = [4.6, 5.3, 6.0]
   ```

**Our project:**
Deep learning models (LSTM, Transformer) can handle non-stationary data, so we use raw prices. But we DO create change features (Price_1d_change) which are stationary.

---

<a name="data-leakage"></a>
## 8. Data Leakage

**What it is:**
When information from the future accidentally "leaks" into training data, leading to unrealistic performance.

**Example of LEAKAGE (BAD):**
```
Training data (2020):
- Feature: Price on Jan 1, 2020
- LEAKED FEATURE: Price on Dec 31, 2020 (future!)
- Target: Price on Jan 2, 2020

Model learns: "Use end-of-year price to predict next day"
Training accuracy: 99% (WOW!)
Real-world: 0% (Can't use future prices)
```

**How leakage happens in time series:**

**1. Wrong Split (Random):**
```
Data: [Jan, Feb, Mar, Apr, May]
Random split:
Train: [Jan, Mar, May]  ← May is in the future!
Test:  [Feb, Apr]       ← Feb is in the past!

Model sees future (May) and predicts past (Feb) → LEAKAGE
```

**2. Future Features:**
```
Creating features:
- SMA_7 at Jan 1 using data from Dec 26 - Jan 2
  ↑ Uses Jan 2 which is in the future! LEAKAGE!

Correct:
- SMA_7 at Jan 1 using data from Dec 25 - Dec 31
  ↑ Only uses past data ✓
```

**3. Target Shift:**
```
Wrong:
df['Target'] = df['Price']  ← Predicting same-day price (trivial)

Correct:
df['Target'] = df['Price'].shift(-1)  ← Predicting next-day price ✓
```

**How we prevent leakage:**

1. **Temporal Split:**
   ```
   Train: 2020 (all past)
   Test: 2021 Q1 (all future)
   No overlap ✓
   ```

2. **Forward-fill only:**
   ```
   Missing data on Jan 15:
   ✓ Use Jan 14 value (past)
   ✗ Use Jan 16 value (future)
   ```

3. **Feature calculation:**
   ```
   For day T, only use data from T-7 to T-1 (never T or T+1)
   ```

**Why it's critical:**
"If your model has data leakage, your backtesting results are lies. You'll think your strategy is profitable when it's actually losing money in real trading."

---

<a name="bollinger-bands"></a>
## 9. Bollinger Bands

**What it is:**
Three lines on a price chart that show if a stock is expensive or cheap relative to recent prices.

**The three bands:**
```
Upper Band = 20-day Average + (2 × Standard Deviation)
Middle Band = 20-day Average (mean)
Lower Band = 20-day Average - (2 × Standard Deviation)
```

**Visual:**
```
Price Chart:
120 ────────────────────── Upper Band (overbought)
110 ──────/\──────/\─────── Middle Band (average)
100 ────/──\────/──\────── Lower Band (oversold)
```

**Simple interpretation:**
- Price touches **Upper Band** → Stock is "expensive" → Might fall back to average → **SELL**
- Price touches **Lower Band** → Stock is "cheap" → Might rise back to average → **BUY**
- Price at **Middle Band** → Stock is "fairly valued" → **HOLD**

**Why 2 standard deviations?**
Statistics: ~95% of data falls within 2 standard deviations.
So touching a band is a rare, significant event.

**Adaptive to volatility:**
```
Calm market (low volatility):
Upper: 105 ─────
Middle: 100 ─────
Lower: 95 ─────
Narrow bands → Trade more often

Volatile market (high volatility):
Upper: 120 ─────────
Middle: 100 ─────────
Lower: 80 ─────────
Wide bands → Trade less often (avoids false signals)
```

**BB Position (Feature):**
```
BB_position = (Price - Lower Band) / (Upper Band - Lower Band)

Values:
0.0 = At lower band (oversold)
0.5 = At middle band (neutral)
1.0 = At upper band (overbought)
```

**Why we use it:**
- Industry standard (trusted by traders worldwide)
- Self-adjusting (bands adapt to volatility)
- Statistical foundation (not arbitrary)

---

<a name="rsi"></a>
## 10. RSI (Relative Strength Index)

**What it is:**
A number between 0 and 100 that shows if a stock has risen too much (overbought) or fallen too much (oversold).

**Simple formula concept:**
```
RSI = 100 - (100 / (1 + RS))
where RS = Average Gain / Average Loss over N days
```

**You don't need to calculate it, just understand:**
- RSI near 0 → Stock has been falling a lot → Oversold → Might bounce up
- RSI near 100 → Stock has been rising a lot → Overbought → Might fall down
- RSI around 50 → Neutral

**Standard thresholds:**
```
RSI > 70: Overbought (sell signal)
RSI < 30: Oversold (buy signal)
RSI 30-70: Neutral (no clear signal)
```

**Example:**
```
Stock price movement over 7 days:
Day 1: +$2 (gain)
Day 2: +$3 (gain)
Day 3: -$1 (loss)
Day 4: +$4 (gain)
Day 5: +$2 (gain)
Day 6: +$1 (gain)
Day 7: -$0.5 (loss)

Average gain: ($2+$3+$4+$2+$1) / 5 = $2.4
Average loss: ($1+$0.5) / 2 = $0.75
RS = 2.4 / 0.75 = 3.2
RSI = 100 - (100 / (1+3.2)) = 76

RSI = 76 → Overbought → Might be due for a pullback
```

**Why we use RSI:**
- Complements Bollinger Bands (BB shows price position, RSI shows momentum)
- Reduces false signals (e.g., price at lower BB + RSI < 30 = strong buy)
- Standard 7-day and 14-day periods capture different timeframes

---

<a name="macd"></a>
## 11. MACD (Moving Average Convergence Divergence)

**What it is:**
A trend-following indicator that shows when two moving averages are getting closer (converging) or farther apart (diverging).

**Three components:**

**1. MACD Line:**
```
MACD = 12-day EMA - 26-day EMA
(Fast average minus slow average)
```

**2. Signal Line:**
```
Signal = 9-day EMA of MACD
(Smoothed version of MACD)
```

**3. Histogram:**
```
Histogram = MACD - Signal
(Distance between MACD and Signal)
```

**Simple interpretation:**
```
MACD > 0 → Short-term average > Long-term average → Uptrend
MACD < 0 → Short-term average < Long-term average → Downtrend

MACD crosses above Signal → Buy signal
MACD crosses below Signal → Sell signal

Histogram growing → Trend strengthening
Histogram shrinking → Trend weakening
```

**Visual:**
```
Price: ───────/─────────
       Going up

12-day EMA: ─────/───────  (faster, reacts quickly)
26-day EMA: ────/────────  (slower, lags behind)

MACD = Fast - Slow = Positive (uptrend)
```

**Why we use MACD:**
- Captures trend direction and strength
- Earlier signals than simple moving average crossovers
- Combines momentum and trend in one indicator

---

<a name="atr"></a>
## 12. ATR (Average True Range)

**What it is:**
A measure of how much a stock's price moves (volatility) on average per day.

**Simple concept:**
```
True Range (for one day) = Biggest of:
1. High - Low (today's range)
2. High - Previous Close
3. Previous Close - Low

ATR = Average of True Range over N days (typically 7, 14, or 20)
```

**Example:**
```
Day 1:
High: $105
Low: $100
Previous Close: $102

True Range = max(105-100, 105-102, 102-100) = max(5, 3, 2) = 5

ATR_7 = Average of last 7 days' True Ranges
```

**What ATR tells you:**
```
ATR = $2 → Stock moves about $2 per day (low volatility)
ATR = $10 → Stock moves about $10 per day (high volatility)
```

**Why we use ATR:**

**1. Feature for prediction:**
High ATR → Unpredictable, risky
Low ATR → Stable, safer

**2. Setting stop-loss:**
```
If ATR = $2 (low volatility):
Stop-loss = 2 × ATR = $4

If ATR = $10 (high volatility):
Stop-loss = 2 × ATR = $20

Adaptive to stock's natural movement
```

**3. Market regime detection:**
ATR increasing → Volatility rising → Caution
ATR decreasing → Volatility falling → Safer

---

<a name="adx"></a>
## 13. ADX (Average Directional Index)

**What it is:**
A number between 0 and 100 that tells you HOW STRONG a trend is (but NOT the direction).

**Simple interpretation:**
```
ADX < 20: Weak trend (choppy, sideways market)
ADX 20-40: Moderate trend
ADX > 40: Strong trend
ADX > 60: Very strong trend
```

**Key insight:**
ADX doesn't tell you if the trend is UP or DOWN, only how strong it is.

**Example:**
```
Scenario 1:
Prices: [100, 101, 100, 99, 100, 101]
ADX = 15 (weak trend, choppy)
→ Use mean-reversion strategies (Bollinger Bands)

Scenario 2:
Prices: [100, 102, 105, 108, 112, 115]
ADX = 55 (strong uptrend)
→ Use trend-following strategies (hold winners)
```

**How ADX is calculated (simplified):**
```
1. Calculate +DM (positive directional movement) and -DM (negative)
2. Smooth them over 14 days
3. Calculate DI+ and DI- (directional indicators)
4. ADX = Average of |DI+ - DI-| over 14 days
```

**Why we use ADX:**

**Market Regime Detection:**
```
ADX < 20 → Ranging market → Use Bollinger Bands
ADX > 40 → Trending market → Use longer BB window or trend-following
```

This is why South Africa - Impala Platinum uses a 30-day BB window—it's a trending (high ADX) market!

---

<a name="moving-averages"></a>
## 14. Moving Averages (SMA, EMA)

**What it is:**
The average price over the last N days, updated daily.

**SMA (Simple Moving Average):**
```
SMA_7 on Jan 8 = Average of prices from Jan 1-7
SMA_7 on Jan 9 = Average of prices from Jan 2-8
... and so on
```

**Example:**
```
Prices: [100, 102, 105, 104, 106, 108, 110]
SMA_7 = (100 + 102 + 105 + 104 + 106 + 108 + 110) / 7 = 105
```

**EMA (Exponential Moving Average):**
Like SMA but gives more weight to recent days.
```
EMA_7 weights:
Today: 40%
Yesterday: 25%
2 days ago: 15%
... (weights decay exponentially)
```

**Visual comparison:**
```
Price: ────────/\──────/\────────
        Volatile

SMA: ──────────────────────────────
      Smooth, lags behind

EMA: ────────/──────/──────────────
      Smooth, responds faster
```

**Why different periods?**
```
SMA_5: Captures very short-term trends (1 week)
SMA_20: Captures medium-term trends (1 month)
SMA_50: Captures long-term trends (3 months)
```

**Golden Cross (famous pattern):**
```
When SMA_50 crosses above SMA_200 → Strong buy signal
When SMA_50 crosses below SMA_200 → Strong sell signal
```

**Why we use them:**
- Smooths out daily noise
- Shows trend direction
- Foundation for many other indicators (MACD, Bollinger Bands)

---

<a name="volume-indicators"></a>
## 15. Volume Indicators

**What is Volume?**
The number of shares traded in a day.

**Why volume matters:**
> "Volume features significantly improve directional accuracy" - Mentor feedback

**Key concept:**
Price movement + High volume = Strong, reliable signal
Price movement + Low volume = Weak, unreliable signal

**Volume Ratio:**
```
Volume_ratio = Today's Volume / 10-day Average Volume

Volume_ratio = 0.5 → Only half the usual volume (weak)
Volume_ratio = 1.0 → Normal volume
Volume_ratio = 2.0 → Double the usual volume (strong breakout!)
```

**OBV (On-Balance Volume):**
Cumulative volume that increases on up days and decreases on down days.
```
Day 1: Price +5%, Volume 1000 → OBV = +1000
Day 2: Price -2%, Volume 800 → OBV = 1000 - 800 = 200
Day 3: Price +3%, Volume 1500 → OBV = 200 + 1500 = 1700
```

OBV rising → Buying pressure → Bullish
OBV falling → Selling pressure → Bearish

**Price-Volume Relationship:**
```
Scenario 1: Price ↑, Volume ↑ → Strong uptrend ✓
Scenario 2: Price ↑, Volume ↓ → Weak uptrend, might reverse ⚠
Scenario 3: Price ↓, Volume ↑ → Strong downtrend (panic selling) ✓
Scenario 4: Price ↓, Volume ↓ → Weak downtrend, might bounce ⚠
```

**Why emphasized in feedback:**
Volume confirms price movements. Without volume confirmation, price moves are often false signals.

---

<a name="overfitting"></a>
## 16. Overfitting vs. Underfitting

**Overfitting:**
Model memorizes training data instead of learning general patterns.

**Analogy:**
Imagine studying for an exam:
- **Overfitting:** You memorize the exact 50 practice problems. You ace the practice test (100%) but fail the real exam (40%) because you didn't learn the concepts.
- **Good fit:** You understand the concepts. You score 85% on practice and 80% on the real exam.

**Visual:**
```
Underfitting:
Data: ────●─────●─────●─────●────
Model: ──────────────────────────  (straight line, misses all points)
Training: 60%
Testing: 55%
Issue: Too simple, didn't learn enough

Good Fit:
Data: ────●─────●─────●─────●────
Model: ────●───●────●────●─────  (smooth curve through points)
Training: 85%
Testing: 82%
Issue: None, generalizes well

Overfitting:
Data: ────●─────●─────●─────●────
Model: ──●─●─●──●─●─●──●─●─●────  (zigzag through every point)
Training: 99%
Testing: 40%
Issue: Memorized noise, doesn't generalize
```

**In our project:**

**Signs of overfitting:**
- Training MAPE: 1% (amazing!)
- Testing MAPE: 15% (terrible!)
- Model learned noise, not patterns

**How we prevent it:**
1. **Dropout:** Randomly ignore 20% of neurons during training
2. **Temporal split:** Test on future data (2021) unseen during training (2020)
3. **Don't overtrain:** Stop at 50 epochs (not 1000)
4. **Regularization:** Penalty for overly complex models

---

<a name="feature-engineering"></a>
## 17. Feature Engineering

**What it is:**
Creating new useful features from raw data.

**Raw data (not very useful alone):**
```
Date, Price, Volume
```

**Engineered features (much more useful):**
```
Date, Price, Volume,
Price_change, SMA_7, RSI, MACD, Volume_ratio,
Bollinger_position, ATR, Trend, Volatility, ...
```

**Why it's critical:**
> "The fact that you took trend and rolling window averages shows that you have correctly converted time series data into tabular form." - Mentor

**Good features capture:**
1. **Trend:** Where is it going? (SMA, EMA)
2. **Momentum:** How fast is it going? (RSI, ROC)
3. **Volatility:** How uncertain is it? (ATR, Bollinger width)
4. **Volume:** How much conviction? (Volume ratio, OBV)
5. **Context:** Where is it relative to history? (BB position)

**Example transformation:**
```
Raw:
Date: Jan 1, Price: 100
Date: Jan 2, Price: 105
Date: Jan 3, Price: 103

Engineered (for Jan 3):
Price: 103
Price_1d_change: -1.9% (103-105)/105
Price_5d_change: +3% (if Jan 1 was 100)
SMA_7: 102 (if we have 7 days)
RSI_7: 65 (momentum indicator)
Volume_ratio: 1.2 (above average)
```

**The art:**
Not all features are useful. Too many → overfitting. Too few → underfitting.
We create 61, use 11 for models, and let models learn which matter.

---

<a name="scaling"></a>
## 18. Scaling/Normalization

**What it is:**
Transforming features to a similar range so models train better.

**The problem:**
```
Features without scaling:
Price: 100, 105, 110 (range: 100-110)
Volume: 1,000,000, 1,500,000, 2,000,000 (range: 1M-2M)

Model struggles because Volume numbers are 10,000× larger
```

**MinMaxScaler (what we use):**
Transforms all features to range [0, 1].
```
Formula:
scaled = (value - min) / (max - min)

Example (Price):
Min = 100, Max = 110
Price 100 → (100-100)/(110-100) = 0.0
Price 105 → (105-100)/(110-100) = 0.5
Price 110 → (110-100)/(110-100) = 1.0
```

**After scaling:**
```
Price: 0.0, 0.5, 1.0
Volume: 0.0, 0.5, 1.0

Now both features are in range [0, 1] → Model trains better
```

**Why it matters:**
- Neural networks train faster and better with scaled data
- Prevents large-valued features from dominating
- Standard practice in deep learning

**Critical:** We scale training data and apply the SAME scaling to test data.
```
train_scaler.fit(train_data)  # Learn min/max from training
train_scaled = train_scaler.transform(train_data)
test_scaled = train_scaler.transform(test_data)  # Use training min/max

DO NOT: test_scaler.fit(test_data)  # This is data leakage!
```

---

<a name="dropout"></a>
## 19. Dropout

**What it is:**
Randomly "turn off" a percentage of neurons during training to prevent overfitting.

**Simple analogy:**
Imagine studying for an exam with a group of 10 friends. If you always rely on the same 2 friends for answers, you don't learn (overfitting on those 2 friends).

Instead, each study session, randomly different friends are absent. Now you must learn from everyone and understand the material yourself.

**How it works:**
```
Training step 1:
[Neuron 1] ✓ Active
[Neuron 2] ✗ Dropped (randomly)
[Neuron 3] ✓ Active
[Neuron 4] ✗ Dropped (randomly)
[Neuron 5] ✓ Active

Training step 2:
[Neuron 1] ✗ Dropped (randomly)
[Neuron 2] ✓ Active
[Neuron 3] ✓ Active
[Neuron 4] ✓ Active
[Neuron 5] ✗ Dropped (randomly)
```

**Dropout rate = 0.2 means:**
Each neuron has a 20% chance of being turned off during each training step.

**Why it prevents overfitting:**
- Forces model to learn robust features (can't rely on specific neurons)
- Creates an "ensemble" effect (many sub-networks)
- Regularizes the model

**Testing/Inference:**
ALL neurons are active (no dropout). We average their outputs.

**In our project:**
```python
LSTM: dropout=0.2 (20%)
Transformer: dropout=0.1 (10%)
```

Lower dropout for Transformer because it's already less prone to overfitting with attention.

---

<a name="batch-epochs"></a>
## 20. Batch Size & Epochs

**Epoch:**
One complete pass through the entire training dataset.

**Example:**
```
Training data: 1000 samples
1 epoch = Model sees all 1000 samples once

50 epochs = Model sees all 1000 samples 50 times
```

**Batch Size:**
Number of samples processed together before updating model weights.

**Example:**
```
Training data: 1000 samples
Batch size: 16

Number of batches = 1000 / 16 = 62.5 ≈ 63 batches per epoch

Training flow:
Batch 1: Samples 1-16 → Update weights
Batch 2: Samples 17-32 → Update weights
...
Batch 63: Samples 993-1000 → Update weights
↑ This is 1 epoch
```

**Why batches?**

| Batch Size | Pros | Cons |
|------------|------|------|
| **1 (online)** | Updates every sample, fine-grained | Slow, noisy updates |
| **16 (small)** | Good balance, faster | Some noise |
| **1000 (full batch)** | Smooth updates | Slow, memory issues |

**Our choice: Batch size = 16**
- Fast enough (parallel processing)
- Small enough to avoid overfitting
- Standard for time series

**Epochs = 50:**
- Too few (5): Underfitting, model didn't learn enough
- Just right (50): Good performance
- Too many (500): Overfitting, memorizes noise

We could add early stopping: "Stop if performance stops improving."

---

<a name="mape"></a>
## 21. MAPE (Mean Absolute Percentage Error)

**Formula:**
```
MAPE = (1/n) × Σ |Actual - Predicted| / |Actual| × 100%
```

**Simple explanation:**
Average percentage error across all predictions.

**Example:**
```
Day 1: Actual = $100, Predicted = $103
Error = |100 - 103| / 100 = 3%

Day 2: Actual = $50, Predicted = $52
Error = |50 - 52| / 50 = 4%

Day 3: Actual = $200, Predicted = $196
Error = |200 - 196| / 200 = 2%

MAPE = (3% + 4% + 2%) / 3 = 3%
```

**Interpretation:**
```
MAPE = 1% → Excellent (rare in stocks)
MAPE = 3-5% → Good (our project)
MAPE = 10% → Mediocre
MAPE = 20%+ → Poor
```

**Why we use it:**
- **Scale-invariant:** Fair comparison across different price ranges
- **Interpretable:** "On average, I'm off by 3%"
- **Industry standard:** Commonly used in forecasting

**Limitation:**
Undefined if actual = 0. But stock prices are never 0, so no issue.

---

<a name="mae"></a>
## 22. MAE (Mean Absolute Error)

**Formula:**
```
MAE = (1/n) × Σ |Actual - Predicted|
```

**Simple explanation:**
Average absolute error in dollars (or whatever unit).

**Example:**
```
Day 1: Actual = $100, Predicted = $103, Error = $3
Day 2: Actual = $50, Predicted = $52, Error = $2
Day 3: Actual = $200, Predicted = $196, Error = $4

MAE = ($3 + $2 + $4) / 3 = $3
```

**Interpretation:**
"On average, my predictions are off by $3."

**Difference from MAPE:**
```
Stock A ($10 stock):
Predicted: $13, Actual: $10, Error: $3
MAE contribution: $3
MAPE contribution: 30%

Stock B ($100 stock):
Predicted: $103, Actual: $100, Error: $3
MAE contribution: $3
MAPE contribution: 3%

Same MAE but very different MAPE!
```

**Why MAE alone isn't enough:**
Not scale-invariant. $3 error is huge for a $10 stock but tiny for a $1000 stock.

---

<a name="rmse"></a>
## 23. RMSE (Root Mean Squared Error)

**Formula:**
```
RMSE = √[(1/n) × Σ (Actual - Predicted)²]
```

**Simple explanation:**
Like MAE but penalizes large errors more heavily.

**Example:**
```
Prediction Set 1:
Errors: $2, $2, $2, $2, $2
MAE = $2
RMSE = √[($4 + $4 + $4 + $4 + $4) / 5] = √$4 = $2

Prediction Set 2:
Errors: $0, $0, $0, $0, $10
MAE = ($0 + $0 + $0 + $0 + $10) / 5 = $2 (same as Set 1)
RMSE = √[($0 + $0 + $0 + $0 + $100) / 5] = √$20 = $4.47 (worse!)

RMSE penalized the large $10 error more
```

**When RMSE is useful:**
- When large errors are particularly bad (e.g., risk management)
- Detecting outliers (RMSE >> MAE indicates large errors exist)

**Interpretation:**
```
RMSE = $5 for a $100 stock → About 5% error
RMSE > MAE → Model has some large errors
RMSE ≈ MAE → Errors are consistent (good)
```

---

<a name="r-squared"></a>
## 24. R² (R-Squared)

**What it is:**
Percentage of variance in the target explained by the model.

**Formula:**
```
R² = 1 - (Sum of Squared Errors / Total Variance)
```

**Simple explanation:**
How much better is your model than just predicting the average?

**Range:**
```
R² = 1.0 → Perfect predictions
R² = 0.8 → 80% of variance explained (good)
R² = 0.0 → No better than predicting average (bad)
R² < 0 → Worse than predicting average (very bad!)
```

**Example:**
```
Actual prices: [100, 110, 105, 115, 120]
Average = 110

Dumb model (always predict average):
Predictions: [110, 110, 110, 110, 110]
R² = 0

Smart model:
Predictions: [102, 108, 106, 114, 118]
R² = 0.85

Smart model explains 85% of variance!
```

**Interpretation in trading:**
```
R² = 0.6 → Model captures 60% of price movements
Remaining 40% is noise, unpredictable events, etc.
```

**Limitation:**
R² doesn't tell you if predictions are useful for trading. High R² but poor directional accuracy = useless.

---

<a name="directional-accuracy"></a>
## 25. Directional Accuracy

**What it is:**
Percentage of times the model correctly predicts if the price will go UP or DOWN.

**Formula:**
```
Directional Accuracy = (Correct Direction Predictions / Total Predictions) × 100%
```

**Example:**
```
Day 1: Actual +5%, Predicted +3% → Correct (both UP) ✓
Day 2: Actual -2%, Predicted -1% → Correct (both DOWN) ✓
Day 3: Actual +3%, Predicted -1% → Wrong (UP vs DOWN) ✗
Day 4: Actual -1%, Predicted +2% → Wrong (DOWN vs UP) ✗
Day 5: Actual +2%, Predicted +1% → Correct (both UP) ✓

Directional Accuracy = 3/5 = 60%
```

**Why it's more important than MAPE for trading:**

```
Scenario A:
Actual: $100 → $105 (+5%)
Predicted: $104 (+4%)
MAPE: 1% (excellent!)
Direction: Correct ✓
Trading: BUY → Profit $5

Scenario B:
Actual: $100 → $105 (+5%)
Predicted: $99 (-1%)
MAPE: 6% (worse)
Direction: WRONG ✗
Trading: SELL → Missed $5 profit (or lost money)
```

**Interpretation:**
```
50% → Random (coin flip), useless
55% → Slight edge, might be profitable
60% → Good, likely profitable with risk management
65%+ → Excellent, highly profitable
```

**From mentor feedback:**
> "Getting direction right is more important than exact price."

---

<a name="backtesting"></a>
## 26. Backtesting

**What it is:**
Testing a trading strategy on historical data to see if it would have been profitable.

**Simple analogy:**
It's like a practice exam. You're testing if your study strategy works before the real exam.

**How it works:**
```
1. Historical data: 2021 Q1 prices
2. Strategy: Buy when ML predicts +1% rise
3. Simulate trades:
   - Jan 5: Predict +2% → BUY at $100
   - Jan 6: Price goes to $102 → SELL, profit $2
   - Jan 8: Predict +1% → BUY at $103
   - Jan 9: Price drops to $101 → Stop-loss, loss $2
   ... repeat for all days
4. Calculate: Total return, win rate, drawdown
```

**Critical rules:**

**1. No future data (no peeking!):**
```
Wrong: Use Jan 10 price to make Jan 5 decision
Right: Only use data up to Jan 4 to make Jan 5 decision
```

**2. Simulate realistic trading:**
```
Include:
- Transaction costs (if available)
- Slippage (buy/sell at slightly worse prices)
- Can't buy fractional shares (round down)

Exclude (simplified backtesting):
- Assume perfect execution
- Ignore liquidity constraints
```

**Performance metrics:**
```
Portfolio Return: Did I make money?
Win Rate: What % of trades were profitable?
Max Drawdown: Worst loss from peak?
Sharpe Ratio: Risk-adjusted return
```

**Why backtest:**
Proves your strategy works (or doesn't) before risking real money.

**Important disclaimer:**
> "Past performance ≠ future results"

Just because a strategy worked in 2021 doesn't guarantee it will work in 2026.

---

<a name="stop-loss-take-profit"></a>
## 27. Stop-Loss & Take-Profit

**Stop-Loss:**
Automatic sell order when price drops to a certain level to limit losses.

**Example:**
```
Buy at: $100
Stop-loss: 5%
If price drops to $95 → Automatically SELL
Loss limited to: $5 (5%)
```

**Take-Profit:**
Automatic sell order when price rises to a certain level to lock in gains.

**Example:**
```
Buy at: $100
Take-profit: 10%
If price rises to $110 → Automatically SELL
Profit locked in: $10 (10%)
```

**Why they're essential:**

**Without stop-loss:**
```
Buy at $100
Price drops to $80, $60, $40...
Hold forever hoping it recovers
Risk: 100% loss if company goes bankrupt
```

**With stop-loss:**
```
Buy at $100
Price drops to $95 → Exit
Loss: 5%
Capital preserved: $95 available for next trade
```

**Real-world example:**
```
March 2020 COVID crash:
Stocks without stop-loss: -40% (still holding)
Stocks with 5% stop-loss: -5% (exited early, preserved capital)
```

**Risk-Reward Ratio:**
```
Stop-loss: 5% (risk)
Take-profit: 10% (reward)
Ratio: 1:2 (risk $5 to make $10)

Good trades have reward > risk
```

---

<a name="win-rate"></a>
## 28. Win Rate

**What it is:**
Percentage of trades that were profitable.

**Formula:**
```
Win Rate = (Profitable Trades / Total Trades) × 100%
```

**Example:**
```
Total trades: 100
Profitable: 58
Losses: 42

Win Rate = 58 / 100 = 58%
```

**Interpretation:**
```
50% → Break-even (assuming equal profit/loss sizes)
55% → Slight edge, profitable if risk-reward is good
60% → Good
70%+ → Excellent (rare in real trading)
```

**Important nuance:**
Win rate alone doesn't guarantee profitability!

```
Scenario A:
Win Rate: 90%
Average Win: $1
Average Loss: $20
Result: Lose money overall

Example: Win $1 nine times ($9), lose $20 once (-$20) = -$11 total

Scenario B:
Win Rate: 40%
Average Win: $20
Average Loss: $5
Result: Make money overall

Example: Win $20 four times ($80), lose $5 six times (-$30) = +$50 total
```

**Better metric: Expectancy**
```
Expectancy = (Win Rate × Avg Win) - (Loss Rate × Avg Loss)

Positive expectancy → Profitable strategy
Negative expectancy → Losing strategy
```

---

<a name="drawdown"></a>
## 29. Drawdown

**What it is:**
The decline from a peak to a trough (how much you lost from your highest point).

**Example:**
```
Portfolio value over time:
Jan: $1000 (peak)
Feb: $950
Mar: $900 (trough)
Apr: $920

Drawdown = (1000 - 900) / 1000 = 10%
```

**Maximum Drawdown (MDD):**
The worst (largest) drawdown in your entire trading history.

**Example:**
```
Peak 1: $1000 → Trough 1: $900 → Drawdown 1: 10%
Peak 2: $1100 → Trough 2: $900 → Drawdown 2: 18%
Peak 3: $1200 → Trough 3: $1000 → Drawdown 3: 17%

Maximum Drawdown = 18% (worst case)
```

**Why it matters:**

**1. Risk tolerance:**
```
Can you stomach a 20% loss?
If not, adjust strategy to reduce drawdown.
```

**2. Recovery difficulty:**
```
Lose 50% → Need 100% gain to recover
  $100 → $50 (lose 50%)
  $50 → $100 (gain 100%)

Lose 10% → Need 11% gain to recover
  $100 → $90 (lose 10%)
  $90 → $100 (gain 11%)
```

**3. Strategy evaluation:**
```
Strategy A: +30% return, 25% MDD (high risk)
Strategy B: +20% return, 10% MDD (lower risk)

Risk-adjusted, Strategy B might be better
```

**Acceptable drawdowns:**
```
< 10%: Conservative, low risk
10-20%: Moderate risk
20-30%: Aggressive
> 30%: Very risky, dangerous
```

---

<a name="buy-hold"></a>
## 30. Buy & Hold

**What it is:**
A passive strategy where you buy a stock and hold it for a long time regardless of price fluctuations.

**Example:**
```
Jan 1: Buy stock at $100
... (ignore all price movements)
Dec 31: Sell stock at $120

Return = (120 - 100) / 100 = 20%
```

**Why it's the baseline:**
Any active trading strategy (like ours) must outperform buy-and-hold to be worthwhile.

**Logic:**
```
If buy-and-hold returns 20% and my strategy returns 15%:
Why bother? Just buy and hold.

If buy-and-hold returns 20% and my strategy returns 25%:
Great! Active trading added value.
```

**Pros of buy-and-hold:**
- Simple: No decision-making
- Tax-efficient: No frequent trades
- Proven: Works well for long-term investors
- Low stress: Ignore daily volatility

**Cons of buy-and-hold:**
- Miss short-term opportunities
- Exposed to crashes (no protection)
- Requires patience (years)

**Our project:**
We compare all strategies against buy-and-hold to measure "alpha" (excess return).

```
Buy-and-hold return: 15%
ML strategy return: 22%
Alpha (outperformance): +7%
```

---

<a name="genetic-algorithm"></a>
## 31. Genetic Algorithm

**What it is:**
An optimization technique inspired by natural evolution.

**Analogy:**
Think of breeding dogs:
1. Start with 30 random dogs (population)
2. Test them: Which are fastest? (fitness)
3. Keep the fastest 10 (selection)
4. Breed them to create new puppies (crossover)
5. Some puppies have random mutations (mutation)
6. Repeat for 20 generations
7. Final generation has very fast dogs!

**How it works for our project:**

**Goal:** Find best trading parameters
```
Parameters to optimize:
- buy_threshold
- sell_threshold
- holding_period
- stop_loss_pct
- take_profit_pct
```

**Process:**
```
Generation 1:
Create 30 random parameter sets:
Set 1: [0.005, -0.005, 10, 5, 10]
Set 2: [0.01, -0.01, 15, 7, 12]
...
Set 30: [0.003, -0.008, 8, 4, 15]

Evaluate each:
Set 1 → Backtest → Return: 12%
Set 2 → Backtest → Return: 18%
...
Set 30 → Backtest → Return: 8%

Selection (keep best 10):
Sets: 2, 7, 11, 15, 18, 21, 22, 25, 27, 29

Crossover (breed best sets):
Parent A: [0.01, -0.01, 15, 7, 12]
Parent B: [0.008, -0.009, 12, 6, 11]
Child: [0.01, -0.009, 15, 6, 11]  ← Mix of parents

Mutation (small random changes):
Before: [0.01, -0.009, 15, 6, 11]
After:  [0.01, -0.009, 14, 6, 11]  ← Changed 15 to 14 randomly

Generation 2:
New population of 30 (best 10 + 20 children)
Evaluate, select, crossover, mutate...

... Repeat for 20 generations

Final:
Best parameter set emerges
```

**Why GA works:**
- Explores wide search space efficiently
- Doesn't get stuck in local optima (mutation helps)
- Good for complex, non-smooth optimization problems

---

<a name="fitness-function"></a>
## 32. Fitness Function

**What it is:**
The objective function that genetic algorithm tries to maximize.

**Our fitness function:**
```python
fitness = portfolio_return - penalties
```

**Components:**

**1. Portfolio Return (main goal):**
```
Higher return = Higher fitness
Return 20% → Fitness +20
Return 5% → Fitness +5
```

**2. Penalty for Excessive Trades:**
```
Too many trades → Churning → Bad
If trades > 100:
  penalty = (trades - 100) * 0.1
```

**3. Penalty for Long Holdings:**
```
Holding too long → Capital tied up → Bad
If avg_holding > 15 days:
  penalty = (avg_holding - 15) * 0.5
```

**4. Penalty for Large Drawdown:**
```
High risk → Bad
If max_drawdown > 15%:
  penalty = (max_drawdown - 15) * 2
```

**5. Penalty for Underperformance:**
```
Worse than buy-and-hold → Bad
If portfolio_return < buy_and_hold:
  penalty = (buy_and_hold - portfolio_return) * 3
```

**Example calculation:**
```
Parameter Set A:
Portfolio Return: 25%
Trades: 120 (penalty: 2)
Avg Holding: 18 days (penalty: 1.5)
Max Drawdown: 12% (no penalty)
Buy & Hold: 20% (no penalty, we beat it)

Fitness = 25 - 2 - 1.5 = 21.5

Parameter Set B:
Portfolio Return: 30%
Trades: 200 (penalty: 10)
Avg Holding: 25 days (penalty: 5)
Max Drawdown: 22% (penalty: 14)
Buy & Hold: 20% (no penalty, we beat it)

Fitness = 30 - 10 - 5 - 14 = 1

Set A is better! (Higher fitness)
```

**Why penalties matter:**
Without penalties, GA would find unrealistic strategies (e.g., trade 1000 times/day, hold forever, take huge risks).

---

<a name="hyperparameters"></a>
## 33. Hyperparameters

**What they are:**
Settings you choose BEFORE training the model (not learned from data).

**Analogy:**
Baking a cake:
- **Hyperparameters:** Oven temperature, baking time (you set these)
- **Parameters:** Amount of each ingredient absorbed during baking (learned from cooking process)

**Examples in our project:**

**LSTM Hyperparameters:**
```
hidden_dim: 64        (number of neurons in LSTM layer)
num_layers: 3         (how many LSTM layers stacked)
dropout: 0.2          (regularization strength)
learning_rate: 0.001  (how fast model learns)
batch_size: 16        (samples per update)
epochs: 50            (training iterations)
seq_length: 6         (days in input sequence)
```

**Transformer Hyperparameters:**
```
d_model: 64           (embedding dimension)
nhead: 4              (number of attention heads)
num_layers: 2         (encoder layers)
dropout: 0.1
```

**How to choose hyperparameters:**

**1. Literature/Default:**
"Industry standard is X" → Start with X

**2. Trial and Error:**
Try a few values, see what works

**3. Hyperparameter Tuning:**
Systematic search (grid search, random search, Bayesian optimization)

**4. Domain Knowledge:**
"Stock data is noisy → Use more dropout"

**Hyperparameters vs. Parameters:**

| Aspect | Hyperparameters | Parameters |
|--------|----------------|------------|
| **Set by** | You (manually) | Model (learned) |
| **When** | Before training | During training |
| **Examples** | Learning rate, layers | Weights, biases |
| **Count** | ~10 | Millions |

**Why they matter:**
Bad hyperparameters → Model won't learn well
Good hyperparameters → Model performs optimally

**Our approach:**
Start with standard values, adjust based on performance.

---

## Summary

**Key Takeaways:**

1. **Models (LSTM, Transformer):** Learn patterns from sequences
2. **Time Series:** Order matters, no data leakage, temporal split
3. **Features:** Transform time series with trend, volatility, volume
4. **Indicators (BB, RSI, MACD, ATR):** Capture market behavior
5. **Metrics (MAPE, Directional Accuracy):** Measure prediction quality
6. **Trading (Stop-loss, Backtesting):** Manage risk, validate strategies
7. **Optimization (Genetic Algorithm):** Find best parameters

**For Interviews:**
You don't need to memorize formulas, but you should understand:
- **What** each concept does
- **Why** it's useful
- **When** to apply it
- **Trade-offs** involved

**Honesty is key:**
"I understand the concept and why it's useful, but I'd need to look up the exact formula" is a perfectly acceptable answer.
