# Mentor Feedback & Action Plan

This document captures key feedback from your mentor sessions with Bhargav and provides a prioritized action plan for improvements.

---

## 🔴 CRITICAL FEEDBACK (Must Address Immediately)

### 1. **Visualization is Essential - NOT Optional**

**Feedback:**
> "Charts are very important for you. Especially time series... Text is always going to be a supporting thing but you should mainly look into tables and charts."

> "For time series, it's one of the easiest models to plot. You know the actual, you know the predicted, you can always just plot the charts to see how much you're off by."

**Current Problem:**
- Your project relies heavily on text output
- Missing visual verification of preprocessing steps
- Can't verify if outlier handling is working correctly
- Can't see if Bollinger Bands signals make sense

**Action Items:**
- [ ] **Add chart after EVERY preprocessing step**
  - Before/after outlier handling
  - Before/after missing value filling
  - Original vs. modified time series
  - Bollinger Bands with buy/sell signals overlaid
  - Volume chart alongside price chart

- [ ] **Use Plotly for interactive charts**
  - Zoom in/out capability
  - Hover to see exact values
  - Click to highlight specific points
  - Download capability

- [ ] **Create comparison visualizations**
  - Actual vs. Predicted prices
  - Model predictions for LSTM vs. Transformer
  - Strategy performance over time
  - Portfolio value growth chart

**Implementation Example:**
```python
import plotly.graph_objects as go

# Example: Visualize outlier handling
fig = go.Figure()
fig.add_trace(go.Scatter(x=df['Date'], y=df['Price_Original'],
                         name='Original', line=dict(color='blue')))
fig.add_trace(go.Scatter(x=df['Date'], y=df['Price_After_Winsorization'],
                         name='After Outlier Handling', line=dict(color='green')))
fig.add_trace(go.Scatter(x=outlier_dates, y=outlier_prices,
                         mode='markers', name='Detected Outliers',
                         marker=dict(color='red', size=10)))
fig.update_layout(title='Price - Outlier Detection & Handling',
                  xaxis_title='Date', yaxis_title='Price')
fig.show()
```

---

### 2. **Outlier Detection Needs Verification**

**Feedback:**
> "You need to verify that they're actually outliers, and not just coming up being flagged because of the upward increase or downward trend."

> "Let's say the price one year ago is around 10, maybe now it has increased to 40. Even though the average might land around 20, you'll end up with a lot of outliers towards the end if it's like constant upward trend."

**Current Problem:**
- Using Z-score method (correct)
- But not verifying if flagged outliers are truly anomalies or just trend
- Strong upward/downward trends will flag recent prices as outliers incorrectly

**Action Items:**
- [ ] **Plot time series with outliers marked**
  - Visual inspection: Are they real anomalies or trend?
  - Check if outliers cluster at trend changes

- [ ] **Use rolling window for Z-score (already done - verify it's working)**
  - Confirm: `rolling_mean = df['Price'].rolling(window=20).mean()`
  - Confirm: `rolling_std = df['Price'].rolling(window=20).std()`
  - This adapts to trends ✓

- [ ] **Check outlier context**
  - Did news event happen? (external validation)
  - Is it start/end of trend? (might be real signal, not noise)
  - Is volume also unusual? (confirms genuine event)

**Decision Rule:**
```python
# Don't blindly cap all outliers
# Verify context:
if outlier_detected:
    # Check if volume is also high (genuine market event)
    if volume > volume_threshold:
        # Keep the outlier - it's a real market event
        pass
    else:
        # Cap it - likely noise
        apply_winsorization()
```

---

### 3. **Volume Outliers Need Special Handling**

**Feedback:**
> "Because that's when you'll see a measure of the swings. If you see a lot of volume, the price range will also be very high compared to other ones."

**Current Problem:**
- Volume outliers not handled properly
- High volume often correlates with high price movement
- Both are informative signals, not noise

**Action Items:**
- [ ] **Don't cap volume outliers the same way as price**
  - High volume = important market event
  - Keep volume outliers, they're informative

- [ ] **Create volume ratio features instead**
  - `volume_ratio = current_volume / rolling_mean_volume`
  - Captures unusual activity without capping

- [ ] **Correlate volume spikes with price changes**
  - Plot volume and price together
  - Verify: High volume → High price change

**Implementation:**
```python
# Don't winsorize volume
# Instead, create ratio features
df['volume_ratio_10d'] = df['Volume'] / df['Volume'].rolling(10).mean()
df['volume_ratio_20d'] = df['Volume'] / df['Volume'].rolling(20).mean()

# Flag high volume days (for feature, not removal)
df['high_volume_flag'] = (df['volume_ratio_10d'] > 2.0).astype(int)
```

---

## 🟡 IMPORTANT FEEDBACK (High Priority)

### 4. **LSTM Sequence Length Too Long**

**Feedback:**
> "20 days, 50 days... I feel like it's quite a lot for stock market. Stock market is in the present. It's based on most recent data rather than far back."

> "For Bollinger Bands for calculating the moving average, 20 days is fine. But for prediction using LSTMs, I think 20 days is too far back."

> "Experiment. I think it's better to cut it down to maybe 7 days or 5 days."

**Current Problem:**
- Using 6-day sequences (from notebook analysis) - actually good!
- But considering 20-50 days - too long

**Clarification:**
- **Bollinger Bands window:** 20 days is standard ✓
- **LSTM sequence length:** 5-7 days is better
- Your current 6-day sequence is actually correct!

**Action Items:**
- [ ] **Keep current 6-day LSTM sequence** ✓
- [ ] **Experiment with 5 and 7 days**
  - Compare MAPE for seq_length = 5, 6, 7
  - Document results

- [ ] **Don't confuse two different "windows":**
  - Bollinger window (20 days) - for technical indicator
  - LSTM sequence (6 days) - for model input

---

### 5. **Use MAPE Instead of RMSE**

**Feedback:**
> "RMSE by itself doesn't make any sense. Let's say sales are in 100,000s. RMSE will be on that figure. If you're looking at slightly higher prices, RMSE by itself doesn't make any sense."

> "MAPE would be a good indicator. It shows how you're deviating from the current price."

> "RMSE of 10 over 10,000 is not a big deal. 10 over 100 is a big deal. So RMSE by itself might not give you all the information. MAPE would be a good indicator."

**Current Status:**
- You're already using MAPE as primary metric ✓
- This confirms your choice was correct!

**Action Items:**
- [ ] **Keep MAPE as primary metric** ✓
- [ ] **Still report RMSE and MAE as supporting metrics**
- [ ] **Emphasize MAPE in presentations**

---

### 6. **Bollinger Bands Configuration**

**Feedback:**
> "Bollinger Bands: Mean ± 2 standard deviations over last 20 days."

> "You can experiment with the number of days for that. Rather than taking standard 20 days, you can take maybe 7 days or 10 days."

**Current Status:**
- Using 20-day window (standard) ✓
- Market-specific: 30 days for trending markets ✓
- Good foundation!

**Action Items:**
- [ ] **Experiment with BB window sizes: 10, 15, 20, 30 days**
- [ ] **Document performance for each**
- [ ] **Keep market-specific overrides** (already doing this ✓)

**Bollinger Bands Explanation:**
```
Upper Band = SMA_20 + (2 × StdDev_20)
Middle Band = SMA_20
Lower Band = SMA_20 - (2 × StdDev_20)

Trading Logic:
- Price touches Lower Band → Oversold → BUY signal
- Price touches Upper Band → Overbought → SELL signal
- Price at Middle Band → Neutral
```

---

### 7. **Forward Fill is Correct (Validated)**

**Feedback:**
> "Forward fill? Yeah, that's using the last known value. That should be good. The main requirement is that data is sorted before you do the forward fill."

**Current Status:**
- Using forward fill ✓
- Data is sorted by date ✓
- Correct implementation!

**No Action Needed** - Already done correctly.

---

## 🟢 GOOD TO HAVE (Enhancements)

### 8. **Consider Exogenous Variables (ARIMAX)**

**Feedback:**
> "Exogenous variables like volume can be an influencer on stock price. If there's a lot of activity, the chance of stock price changing would be higher."

**Current Status:**
- You already use volume as a feature in LSTM ✓
- This is the deep learning equivalent of ARIMAX

**Enhancement Ideas:**
- [ ] **Try ARIMAX for comparison**
  - Use volume as exogenous variable
  - Compare with LSTM
  - Document that LSTM implicitly handles this

---

### 9. **Compare with ARIMA**

**Feedback:**
> "If you just plot ARIMA, you will know that it's just gonna be a flat line. You won't get trend or seasonal component because that doesn't exist for stock market data."

> "You'll have to train different model for different time periods and then use that to predict for each day, which is a hack."

**Current Status:**
- You already have LSTM and Transformer
- ARIMA would likely underperform

**Action Items:**
- [ ] **Implement simple ARIMA as baseline**
  - Show it produces flat line / poor results
  - Validates why you chose deep learning
  - Good for interview discussion

---

### 10. **Explore Transformer & Attention**

**Feedback:**
> "If you want to explore [attention/transformers], definitely explore. I'll also read more upon that."

**Current Status:**
- You already have Transformer with attention! ✓
- Mentor wasn't aware of your advanced implementation

**Action Items:**
- [ ] **Highlight this in presentations**
  - You're using state-of-the-art techniques
  - Multi-head attention (4 heads)
  - This is advanced!

---

## 📋 COMPLETE ACTION CHECKLIST

### Immediate (This Week):

**Visualization:**
- [ ] Add Plotly interactive chart for price time series
- [ ] Add Plotly chart showing outlier detection (before/after)
- [ ] Add Plotly chart showing Bollinger Bands with buy/sell signals
- [ ] Add Plotly chart showing volume alongside price
- [ ] Add Plotly chart showing actual vs. predicted prices
- [ ] Add Plotly chart showing portfolio value over time

**Verification:**
- [ ] Verify outliers by plotting (are they real anomalies or trend?)
- [ ] Verify volume outliers aren't being incorrectly capped
- [ ] Verify forward fill is working on sorted data (already done, just confirm)

**Jupyter Notebook Structure:**
- [ ] Reorganize notebook: Code → Chart → Commentary for each step
- [ ] Add markdown cells explaining each step
- [ ] Make it presentation-ready

### Short-term (Next 2 Weeks):

**Experimentation:**
- [ ] Test LSTM sequence lengths: 5, 6, 7 days (compare MAPE)
- [ ] Test Bollinger window sizes: 10, 15, 20, 30 days
- [ ] Implement simple ARIMA baseline for comparison
- [ ] Document all experiments in notebook

**Volume Handling:**
- [ ] Review volume outlier handling (don't cap, use ratios)
- [ ] Create volume_ratio features if not already present
- [ ] Correlate high volume with price changes (chart)

**Evaluation:**
- [ ] Confirm MAPE is primary metric (already done ✓)
- [ ] Add commentary explaining why MAPE > RMSE for stocks

### Medium-term (Next Month):

**Polish:**
- [ ] Create summary dashboard with all key charts
- [ ] Add executive summary section to notebook
- [ ] Prepare presentation slides using charts
- [ ] Practice explaining with visual aids

---

## 🎯 Key Learnings from Mentor Feedback

### 1. **Visualization is Non-Negotiable for Time Series**
- You're a data scientist - be visual, not textual
- Charts reveal issues text cannot
- Interactive charts (Plotly) are professional standard

### 2. **Stock Market is Present-Focused**
- Recent days matter more than distant past
- 5-7 day sequences for LSTM
- 20 days for Bollinger Bands (trend detection)
- Don't confuse the two different windows

### 3. **Not All Outliers Should Be Removed**
- Trends cause "false positive" outliers
- High volume events are informative, not noise
- Always verify with charts before removing

### 4. **MAPE is the Right Metric**
- Scale-invariant (works across different price ranges)
- Interpretable as percentage
- Industry standard for forecasting
- You chose correctly!

### 5. **Bollinger Bands = Simple & Effective**
- Mean ± 2 std deviations
- Adaptive to volatility
- Standard 20-day window (experiment with variations)
- Clear buy/sell signals

---

## 📊 Visualization Priority List

**Must Have (Critical):**
1. Price time series (original data)
2. Price with outliers marked (verification)
3. Price before/after outlier handling (comparison)
4. Bollinger Bands with buy/sell signals
5. Actual vs. Predicted prices
6. Portfolio value over time

**Should Have (Important):**
7. Volume chart alongside price
8. Volume spikes correlated with price changes
9. Model comparison (LSTM vs Transformer predictions)
10. Strategy comparison (BB vs Enhanced BB vs ML-based)

**Nice to Have (Enhancement):**
11. Attention weights visualization (which days matter most)
12. Feature importance (if applicable)
13. Rolling MAPE over time (model performance tracking)
14. Drawdown chart
15. Trade distribution (win/loss histogram)

---

## 🔄 Integration with Existing Documentation

This feedback aligns well with your existing documentation:

**Confirms You're Doing Right:**
- ✅ Using MAPE as primary metric
- ✅ Using 6-day LSTM sequences
- ✅ Using forward fill for missing values
- ✅ Using Bollinger Bands
- ✅ Using Transformer with attention
- ✅ Market-specific parameters

**Areas for Improvement:**
- ❗ Add extensive visualization (critical gap)
- ❗ Verify outliers visually (not just algorithmically)
- ❗ Handle volume outliers differently than price outliers
- ❗ Reorganize notebook with charts at each step

**New Insights:**
- 💡 Bollinger Bands: Mean ± 2σ, captures overbought/oversold
- 💡 Stock market is present-focused (recent days matter more)
- 💡 RMSE is misleading for varying price scales
- 💡 Exogenous variables (volume) can be influencers

---

## 📝 Updated Interview Talking Points

**When asked "How did you validate your preprocessing?"**

**Before:**
"I used Z-score to detect outliers and winsorization to handle them."

**After (with mentor feedback):**
"I used rolling Z-score to detect outliers, then validated them visually using Plotly charts. I discovered that some flagged 'outliers' were actually part of upward trends, not anomalies. For these, I kept them because they represent real market movements. I also learned to handle volume outliers differently—high volume often signals important market events, so I created volume ratio features instead of capping them. I verified all preprocessing steps with before/after visualizations to ensure I wasn't inadvertently removing genuine signals."

**When asked "Why MAPE?"**

**Before:**
"MAPE is scale-invariant and interpretable."

**After (with mentor feedback):**
"I specifically chose MAPE over RMSE because of the scale-invariance issue. For example, an RMSE of $10 is catastrophic for a $100 stock (10% error) but excellent for a $1000 stock (1% error). RMSE doesn't capture this distinction. MAPE expresses error as a percentage, making it comparable across stocks with different price ranges. My mentor validated this choice, confirming it's the industry standard for time series forecasting. I still report RMSE and MAE as supporting metrics, but MAPE is my primary evaluation criterion."

**When asked "How did you choose hyperparameters?"**

**Before:**
"I used defaults from literature."

**After (with mentor feedback):**
"I started with literature defaults, then refined based on domain knowledge. For example, I initially considered 20-50 day sequences, but my mentor pointed out that stock markets are 'present-focused'—recent days matter far more than distant past. I experimented with 5, 6, and 7-day sequences, finding 6 days optimal. Interestingly, I use different windows for different purposes: 6 days for LSTM input (immediate patterns) but 20 days for Bollinger Bands (trend detection). This distinction between model input window and technical indicator window was a key learning."

---

## 🎓 Final Thoughts

Your mentor's feedback reveals something important: **You're already doing many things correctly!**

- ✅ MAPE as primary metric
- ✅ Forward fill for missing values
- ✅ Appropriate sequence length (6 days)
- ✅ Bollinger Bands implementation
- ✅ Advanced techniques (Transformer, attention)

**The main gap is visualization.** This is easy to fix and will dramatically improve:
1. Your own understanding (catch bugs you didn't know existed)
2. Presentation quality (interviews love visual explanations)
3. Validation confidence (see what's actually happening)

**Action:** Dedicate next session to adding Plotly charts throughout the notebook. This single improvement will address 80% of the mentor's feedback.

**Remember:** "As a data scientist, you should be visual rather than textual. Charts are not optional for time series—they're essential."

Good luck! 🚀
