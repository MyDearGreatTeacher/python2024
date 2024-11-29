# CH-09 讓 AI 評估投資組合風險

## 9-1 資金管理

```python
### 1️⃣ 單次賭局的期望資產
"""
以下為賠率1的賭局
"""

bet = 500 # 下注金額
win_rate = 0.8 # 勝率
wealth = 1000 # 資產

# 期望獲利
gain = win_rate * bet + (1-win_rate) * -bet
wealth += gain
print("這次賭局期望獲利為:",gain)
print("期望總資產為:",wealth)

### 2️⃣ 單一賭局的隨機結果


import random

def single_bet(bet, win_rate, wealth, odds=1, verbose=True):
  # 單一賭局獲利
  if random.uniform(0,1) <= win_rate:
    gain = bet * odds
  else:
    gain = -bet
  wealth += gain

  if verbose:
    print("這次賭局的獲利為:", gain)
    print("總資產為:", wealth)

  return wealth

single_bet(bet=1000, win_rate=0.8, wealth=1000)

### 3️⃣ 重複賭局的資產變化

def simulate_bets(initial_wealth, bet_ratio,
                  win_rate, num_bets=100, odds=1, verbose=True):
  wealths = [initial_wealth]
  wealth = initial_wealth
  for i in range(num_bets):
    bet = wealth * bet_ratio
    wealth = single_bet(bet=bet, win_rate=win_rate,
                        wealth=wealth, odds=odds, verbose=verbose)
    wealths.append(wealth)
    # 輸光就跳出迴圈
    if wealth <= 0:
        break
  return wealths

simulate_bets(initial_wealth=1000, bet_ratio=1 ,win_rate=0.8)

### 4️⃣ 不同下注量的資產成長幅度

import matplotlib.pyplot as plt
import pandas as pd

# 設定變數
initial_wealth = 1000
bet_ratios = [i/10 for i in range(1, 11)]  # 從10%到全押的下注比例
num_bets = 100
win_rate =0.8

df = pd.DataFrame()
# 模擬各比例下注
for bet_ratio in bet_ratios:
  wealths = simulate_bets(initial_wealth, bet_ratio, win_rate, num_bets, verbose=False)
  df[f'Ratio {bet_ratio}'] = pd.Series(wealths)

final_wealths = df.iloc[-1]
max_ratio = final_wealths.idxmax() # 找到最好的下注比例
max_value = final_wealths.max()  # 最高資產

print(f"最好的下注比例為: {max_ratio}, 最終資產：{max_value}")

# 繪製圖表
ax = df.plot(figsize=(10,6), legend=True, title='Wealth for Different Bet Ratios')
ax.set_xlabel('Number of Bets')
ax.set_ylabel('Wealth')
plt.show()

### 5️⃣ 倍倍下注法

def double_bet(initial_wealth, win_rate, num_bets=100, odds=1):
  wealths = [initial_wealth]
  wealth = initial_wealth
  initial_bet = 1
  bet = initial_bet
  for i in range(num_bets):
    if random.uniform(0, 1) <= win_rate:
      # 若贏了，則下注初始金額
      wealth += bet * odds
      bet = initial_bet
    else:
      # 若輸了，則加倍下注金額
      wealth -= bet
      bet *= 2
    wealths.append(wealth)

    # 輸光就跳出迴圈
    if wealth <= 0:
      break

  return wealths

# 設定變數
initial_wealth = 1000
bet_ratios = [i/10 for i in range(1, 11)]  # 從10%到全押的下注比例
num_bets = 100
win_rate =0.8

df = pd.DataFrame()
# 模擬各比例下注
for bet_ratio in bet_ratios:
  wealths = simulate_bets(initial_wealth, bet_ratio, win_rate, num_bets, verbose=False)
  df[f'Ratio {bet_ratio}'] = pd.Series(wealths)

# 倍倍下注法模擬結果
wealths_double = double_bet(initial_wealth, win_rate, num_bets)
df['double_bet'] = pd.Series(wealths_double)

# 下注法排名
final_wealths = df.iloc[-1]
sorted_wealths = final_wealths.sort_values(ascending=False)
print("下注方法排名：")
i = 1
for index, value in zip(sorted_wealths.index, sorted_wealths.values):
    print(f"第{i}名:{index}, 最終資產：{value}")
    i += 1

# 繪製圖表
ax = df.plot(figsize=(10,6), legend=True, title='Wealth for Different Bet Ratios')
ax.set_xlabel('Number of Bets')
ax.set_ylabel('Wealth')
plt.show()

### 6️⃣ 凱利公式 Kelly formula

def kelly_formula(p,b):
  # 最佳下注比例
  best_bet = (b * p - (1 - p)) / b
  # 如果下注比例小於等於 0，則設為 0
  if best_bet <= 0:
      return 0
  # 取到小數點後兩位
  best_bet = round(best_bet,2)
  return best_bet

kelly_formula(p=0.8,b=1)

best_bet = kelly_formula(p=0.8, b=1)
print("最佳下注比例為:", best_bet)

### 7️⃣ 安裝及匯入套件

!pip install yfinance==0.2.38
!pip install backtesting
!pip install bokeh==2.4.3 # 繪圖套件
import yfinance as yf
import numpy as np
import pandas as pd # 資料處理套件
from scipy.stats import norm
import datetime as dt # 時間套件
from backtesting import Backtest, Strategy
from backtesting.lib import crossover

### 8️⃣ 取得回測結果

# 取得股價資料
stock_id = "2330.tw"
df = yf.download(stock_id, period="5y")
df['ma1'] = df['Close'].rolling(window=5).mean()
df['ma2'] = df['Close'].rolling(window=10).mean()


# 定義回測策略
class CrossStrategy(Strategy):
  def init(self):
    super().init()

  def next(self):
    if crossover(self.data.ma1, self.data.ma2):
      self.buy(size=1)
    elif crossover(self.data.ma2, self.data.ma1):
      self.sell(size=1)


# 回測結果
backtest = Backtest(df,
        CrossStrategy,
        cash=100000,
        commission=0.004,
        margin=1,
        hedging=False,
        trade_on_close=False,
        exclusive_orders=False,
        )
stats = backtest.run()
print(stats)

### 9️⃣ 計算賠率、取得勝率及最佳下注比例

# 先計算出獲利及虧損時的平均報酬
def trades_returns(returns):
    profits = returns[returns > 0].tolist()
    losses = returns[returns < 0].tolist()

    # 確保分母不為零
    avg_profit = sum(profits) / len(profits) if profits else 0
    avg_loss = sum(losses) / len(losses) if losses else 0

    return avg_profit, avg_loss

avg_profit, avg_loss = trades_returns(stats['_trades']['ReturnPct'])
print(f"獲利時的平均報酬:{avg_profit*100:.2f}%")
print(f"虧損時的平均報酬:{avg_loss*100:.2f}%")
print("--------------------------")

# 用平均獲利除以平均虧損來推估賠率
b = -avg_profit/avg_loss
p = stats['Win Rate [%]']/100
print(f"賠率為:{b:.2f}")
print(f"勝率為:{p*100:.2f}%")
print("--------------------------")

# 代入凱利公式
best_bet = kelly_formula(p=p, b=b)
print("最佳下注比例為:", best_bet)

### 🔟 用凱利公式來更改策略

# 定義回測策略
class CrossStrategy(Strategy):
  kelly_ratio = 0.3  # 凱利公式的下注比率

  def init(self):
    super().init()

  def next(self):

    size = (self.equity * self.kelly_ratio) / self.data.Close[-1]
    size = max(round(size), 1) # 確保交易股數為整數

    if crossover(self.data.ma1, self.data.ma2):
        self.buy(size=size)
    elif crossover(self.data.ma2, self.data.ma1):
        self.sell(size=size)


# 回測結果
backtest = Backtest(df,
        CrossStrategy,
        cash=100000,
        commission=0.004,
        margin=1,
        hedging=False,
        trade_on_close=False,
        exclusive_orders=False,
        )
stats = backtest.run()
print(stats)
```
## 9-2 投資組合資金分配與風險管理
```python
### 1️⃣1️⃣ 掛載雲端硬碟 & 安裝套件

# Commented out IPython magic to ensure Python compatibility.
!pip install gdown
import gdown
import os
!git clone https://github.com/FlagTech/F3933.git
# %cd F3933
from Stock_DB import StockDB
# %cd ..
from google.colab import drive
drive.mount('/content/drive')

### 1️⃣2️⃣ 下載資料庫

# 指定下載路徑
!mkdir -p "/content/drive/MyDrive/StockGPT/"
output_path = '/content/drive/MyDrive/StockGPT/'

# 檢查資料庫是否存在
stock_db_path = output_path + 'stock.db'
if not os.path.exists(stock_db_path):
    print("下載資料庫中...")
    id = '1S5JE9ZF2hohRpvO8FikgLhQmN2DJrVgW'

    gdown.download(id=id, output=stock_db_path)
    print("下載完成")
else:
    print("無需下載")

"""若需更新資料庫, 可執行以下程式碼"""

stock_db = StockDB()
stock_db.renew()
stock_db.close()

### 1️⃣3️⃣ 設定投資組合

# 以10檔股票為例
stock_list = [1101, 1203, 1216, 1402, 1722,
               1762, 2330, 2608, 2884, 6405]

condition = f"股號 IN ({','.join(map(str, stock_list))})"

# 從資料庫取出資料
stock_db = StockDB()
df = stock_db.get(table="日頻", where=condition)
df = df.dropna()
df.tail()

###1️⃣4️⃣ 計算每月的漲幅或跌幅

# 設定日期為索引
df['日期'] = pd.to_datetime(df['日期'])
df.set_index('日期', inplace=True)
df = df[df.index > '2017-01-01']

# 訓練資料與測試資料
start = "2021-01-01"
end = "2023-10-10"
train_df = df[df.index <= start]
test_df = df[(df.index > start) & (df.index <= end)]

# 取出每月最後一個交易日的收盤價
monthly_closing = train_df.groupby('股號')\
                          .resample('M')['收盤價'].last()

# 計算每月的漲幅或跌幅
monthly_return = monthly_closing.groupby(level=0)\
                                .pct_change().fillna(0)

print(monthly_return)

###1️⃣5️⃣ 計算每檔股票的最佳下注比例

results = []

# 計算每檔股票的最佳下注比例
for stock in stock_list:
    str_stock = str(stock)
    avg_profit, avg_loss = trades_returns(monthly_return[str_stock])
    b = -avg_profit/avg_loss # 賠率
    p = len(monthly_return[str_stock][monthly_return[str_stock] > 0]
            ) / len(monthly_return[str_stock])  # 勝率
    best_bet = kelly_formula(p=p, b=b) # 下注比例

    results.append([stock, avg_profit, avg_loss, p, b, best_bet])

# 合併為 DataFrame
df_results = pd.DataFrame(results,
              columns=['股號', '平均漲幅', '平均跌幅',
                  '勝率', '賠率', '下注比例'])

total_bet = df_results['下注比例'].sum()
df_results['資金分配'] = df_results['下注比例'] / total_bet

df_results

###1️⃣6️⃣ 比較平均分配與使用下注比例的報酬

# 計算測試資料的每月漲幅或跌幅
monthly_closing_test = test_df.groupby('股號')\
                .resample('M')['收盤價'].last()
monthly_return_test = monthly_closing_test.groupby(level=0)\
                                .pct_change().fillna(0)
first_price = monthly_closing_test.groupby('股號').first()
last_price = monthly_closing_test.groupby('股號').last()


# 計算報酬率
returns = (last_price /first_price)
df_results['股號'] = df_results['股號'].astype(str)
df_results_test = df_results.merge(
    returns.rename('報酬率'), left_on='股號', right_index=True)
display(df_results_test)

# 設定初始資金
initial_capital = 100000

# 平均分配策略的結果
avg = initial_capital / len(stock_list)
avg_strategy = sum(df_results_test['報酬率'] * avg)

# 使用下注比例的策略結果
bet_strategy = sum(df_results_test['報酬率'] *(
    df_results_test['資金分配'] * initial_capital))

print(f"平均分配策略的最終資金: {avg_strategy}")
print(f"下注比例策略的最終資金: {bet_strategy}")

###1️⃣7️⃣ 與大盤績效進行比較

# 投組每月報酬
returns_df = pd.concat([monthly_return_test[str(stock)]
                         for stock in stock_list], axis=1)
returns_df.columns = stock_list
weights = df_results['資金分配'].values
returns_df['投組報酬'] = returns_df[stock_list].dot(weights)

# 計算大盤報酬
market_index = yf.download("^TWII",start=start,end=end)
market_closing = market_index.resample('M')['Close'].last()
market_return = market_closing.pct_change().fillna(0)
returns_df['大盤報酬'] = market_return

# 累積報酬
returns_df['投組累積報酬'] = (1 + returns_df['投組報酬']).cumprod() - 1
returns_df['大盤累積報酬'] = (1 + returns_df['大盤報酬']).cumprod() - 1

# 繪製大盤與投組績效
returns_df = returns_df.drop(returns_df.index[0])
fig, (ax1,ax2) = plt.subplots(1,2, figsize=(15, 4))

ax1.plot(returns_df['大盤報酬'], label="market")
ax1.plot(returns_df['投組報酬'], label="portfolio")
ax1.set_title("Monthly Returns: Portfolio vs Market")
ax1.legend()

ax2.plot(returns_df['大盤累積報酬'], label="market")
ax2.plot(returns_df['投組累積報酬'], label="portfolio")
ax2.set_title("Cumulative Returns: Portfolio vs Market")
ax2.legend()

plt.show()

###1️⃣8️⃣ 投資組合標準差 (σ)

# 計算共變異數矩陣
stk_returns_df = returns_df.iloc[:, :-4]
cov_matrix = stk_returns_df.cov()
display(cov_matrix)

# 計算投資組合的報酬與標準差
portfolio_return = np.dot(weights, stk_returns_df.mean().values)
portfolio_std = np.sqrt(np.dot(weights.T, np.dot(cov_matrix.values, weights)))

# 轉換成年化
annualized_portfolio_return = (1 + portfolio_return)**12 - 1
annualized_portfolio_std = portfolio_std * (12**0.5)
annualized_market_return = (1 + market_return.mean())**12 - 1
annualized_market_std = market_return.std() * (12**0.5)

print(f"投資組合的年化報酬率:{annualized_portfolio_return*100:.2f}%")
print(f"投資組合的年化標準差:{annualized_portfolio_std*100:.2f}%")

print(f"大盤指數的年化報酬率:{annualized_market_return*100:.2f}%")
print(f"大盤指數的年化標準差:{annualized_market_std*100:.2f}%")

###1️⃣9️⃣ 風險值 (Value at Risk, VaR)

confidence_level = 0.95

# 月頻 VaR
VaR = portfolio_return - portfolio_std * norm.ppf(confidence_level)
# 年頻 VaR
VaR_annualized = VaR * (12**0.5)

print(f"在 {confidence_level*100:.0f}% 的信心水準下,\
 下個月的最大可能損失為：{VaR*100:.2f}%")
print(f"在 {confidence_level*100:.0f}% 的信心水準下,\
 明年最大可能損失為：{VaR_annualized*100:.2f}%")

###2️⃣0️⃣ beta 係數 (β)

# 計算β係數
portfolio_market_cov = returns_df[['投組報酬', '大盤報酬']].cov()\
                               .iloc[0, 1]
market_var = returns_df['大盤報酬'].var()
portfolio_beta = portfolio_market_cov / market_var

print(f"投組與大盤的共變異數為：{portfolio_market_cov:.5f},\
    大盤變異數為：{market_var:.5f}")
print(f"投資組合的β係數：{portfolio_beta:.2f}")

###2️⃣1️⃣ 夏普比率 (Sharpe Ratio)

# 設定無風險利率
risk_free_rate = 0.015/12  # 轉換成月頻 (算術平均)

average_return = returns_df['投組報酬'].mean()
portfolio_std = returns_df['投組報酬'].std()

sharpe_ratio = (average_return - risk_free_rate) / portfolio_std
sharpe_ratio_annualized = sharpe_ratio * (12**0.5)

print(f"投資組合的夏普比率：{sharpe_ratio:.2f}")
print(f"年化的夏普比率：{sharpe_ratio_annualized:.2f}")
```
