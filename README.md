
# Algo Trading

This is a Python script for algorithmic trading utilizing the Relative Strength Index (RSI) as a technical indicator. The script uses the yfinance library to download historical stock data for a specified symbol, calculates the daily and weekly RSI, identifies buy and sell signals based on predefined conditions, and visualizes the RSI along with the signals using Plotly.





## Features

- Generate Signal for Buying and selling
- Plots the complete stock chart along with the signals
- Can be customized for any stock prediction



# Documentation

## 1. RSI Calculation 
The script includes a function to calculate the RSI using the closing prices of the stock. It employs a 14-day window by default but can be customized.

```bash
  def calculate_rsi(data, window=14):
    delta = data['Close'].diff()
    up = delta.clip(lower=0)
    down = -1 * delta.clip(upper=0)
    
    ema_up = up.ewm(com=window-1, adjust=False).mean()
    ema_down = down.ewm(com=window-1, adjust=False).mean()

    rs = ema_up / ema_down
    rsi = 100 - (100 / (1 + rs))
    return rsi
```
## 2. Data Retrieval
Daily and weekly stock data are downloaded using the yfinance library for a specified stock symbol. The start date is set to '2022-01-01', and the end date is set to the current date.

```bash
symbol = 'PAYTM.NS'
stock_data_daily = yf.download(symbol, start='2022-01-01', end=datetime.today().strftime('%Y-%m-%d'))
stock_data_weekly = yf.download(symbol, start='2022-01-01', interval='1wk', end=datetime.today().strftime('%Y-%m-%d'))
```
## 3. Signal Generation
Buy and sell signals are generated based on RSI conditions. The script identifies instances where the daily RSI crosses below 35 and the weekly RSI is above 50 as buy signals. Conversely, if the daily RSI crosses above 75 and the weekly RSI is above 50, it indicates a sell signal.

```bash
merged_data['Signal'] = 0

# buy_signal = 0
crossed_below_30 = 0
crossed_above_70 = 0

first_row = merged_data.iloc[0]
if first_row.Daily_RSI < 35 and first_row.Weekly_RSI > 50:
    crossed_below_30 = 1

if first_row.Daily_RSI > 75 and first_row.Weekly_RSI > 50:
    crossed_above_70 = 1
```

### Buy Signal
```bash
for row in merged_data.itertuples():
    if row.Daily_RSI<35 and row.Weekly_RSI>50 and crossed_below_30==0:
        crossed_below_30=1

    if row.Daily_RSI>35 and row.Weekly_RSI>50 and crossed_below_30==1:
        crossed_below_30=0
        merged_data.at[row.Index, 'Signal'] = 1
```

### Sell Signal
```bash
for row in merged_data.itertuples():
    if row.Daily_RSI>75 and row.Weekly_RSI>50 and crossed_above_70==0:
        crossed_above_70=1

    if row.Daily_RSI<75 and row.Weekly_RSI>50 and crossed_above_70==1:
        crossed_above_70=0
        merged_data.at[row.Index, 'Signal'] = 2
```

## 4. Visualization

The script creates interactive plots using Plotly to visualize the daily and weekly RSI along with buy and sell signals. The RSI values are plotted in one subplot, and buy and sell signals are highlighted in another subplot.

![newplot](https://github.com/Ayush21031/Algo-Trading/assets/108355333/0510344f-e099-476c-9b23-2e524e2bd7f7)

```bash
fig = make_subplots(rows=2, cols=1, shared_xaxes=True, vertical_spacing=0.1, subplot_titles=["RSI", "Buy and Sell Signals"])

# Plot RSI
fig.add_trace(go.Scatter(x=merged_data.index, y=merged_data['Daily_RSI'], name='Daily RSI', line=dict(color='blue')), row=1, col=1)
fig.add_trace(go.Scatter(x=merged_data.index, y=merged_data['Weekly_RSI'], name='Weekly RSI', line=dict(color='green')), row=1, col=1)

# Highlight buy and sell signals
buy_signals = merged_data[merged_data['Signal'] == 1]
sell_signals = merged_data[merged_data['Signal'] == 2]

fig.add_trace(go.Scatter(x=buy_signals.index, y=buy_signals['Daily_RSI'], mode='markers', name='Buy Signal', marker=dict(color='green', symbol='triangle-up', size=10)), row=1, col=1)
fig.add_trace(go.Scatter(x=sell_signals.index, y=sell_signals['Daily_RSI'], mode='markers', name='Sell Signal', marker=dict(color='red', symbol='triangle-down', size=10)), row=1, col=1)

# Create a trace for buy signals
fig.add_trace(go.Scatter(x=n_signal_1.index, y=n_signal_1['Daily_RSI'], mode='markers', name='Buy Signals', marker=dict(color='green', symbol='triangle-up', size=10)), row=2, col=1)

# Create a trace for sell signals
fig.add_trace(go.Scatter(x=n_signal_2.index, y=n_signal_2['Daily_RSI'], mode='markers', name='Sell Signals', marker=dict(color='red', symbol='triangle-down', size=10)), row=2, col=1)

# Update layout
fig.update_layout(
    title='RSI with Buy and Sell Signals',
    xaxis=dict(title='Date'),
    yaxis=dict(title='RSI', range=[0, 100]),
    showlegend=False  # Show legend in a separate box
)

# Show the interactive plot
fig.show()
```

## Usage


```bash
1. Install the required libraries using pip install yfinance pandas plotly.
```
```bash
2. Replace the 'PAYTM.NS' symbol with the desired stock ticker symbol.
```
```bash
3. Run the script to execute the algorithmic trading strategy and visualize the results.
```
Feel free to customize the script according to your trading preferences and risk tolerance. Note that algorithmic trading involves risks, and the provided script serves as an educational tool. Users should exercise caution and thoroughly understand the strategy before applying it to real-world trading scenarios.
