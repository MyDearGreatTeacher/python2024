# CH-05 AI 技術指標回測系統

## 5-2 強大的回測工具：backtesting.py
- https://kernc.github.io/backtesting.py/#google_vignette


```python
### 1️⃣ 安裝及匯入套件
!pip install openai
!pip install yfinance==0.2.38
!pip install backtesting
!pip install bokeh==2.4.3 # 繪圖套件

from  openai import OpenAI, OpenAIError # 串接 OpenAI API
import yfinance as yf
import pandas as pd # 資料處理套件
import datetime as dt # 時間套件
from backtesting import Backtest, Strategy
from backtesting.lib import crossover

### 2️⃣ 取得股價資料

# 輸入股票代號
stock_id = "2330.tw"

# 抓取 5 年資料
df = yf.download(stock_id, period="5y")

# 計算指標
df['ma1'] = df['Close'].rolling(window=5).mean()
df['ma2'] = df['Close'].rolling(window=10).mean()
df.head()

### 3️⃣ 定義回測策略

class CrossStrategy(Strategy):
  def init(self):
    super().init()

  def next(self):
    if crossover(self.data.ma1, self.data.ma2):
      self.buy(size=1)
    elif crossover(self.data.ma2, self.data.ma1):
      self.sell(size=1)

### 4️⃣ 回測結果

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

# 印出回測績效
print(stats)

# 查看詳細的交易紀錄
stats["_trades"].head()

### 5️⃣ 回測繪圖

backtest.plot(plot_equity=True,
       plot_return=False,
       plot_pl=True,
       plot_volume=True,
       plot_drawdown=False,
       superimpose=True)

### 6️⃣ 設定停利、停損點

class CrossStrategy(Strategy):
  def init(self):
    super().init()

  def next(self):
    if crossover(self.data.ma1, self.data.ma2):
        # 買入時設置停損與停利價格
        self.buy(size=1,
            sl=self.data.Close[-1] * 0.90,
            tp=self.data.Close[-1] * 1.10)
    elif crossover(self.data.ma2, self.data.ma1):
        # 賣出時時設置停損與停利價格
        self.sell(size=1,
             sl=self.data.Close[-1] * 1.10,
             tp=self.data.Close[-1] * 0.90)

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
## 5-3 讓 AI 產生回測策略
```python
### 7️⃣ 輸入 OpenAI API KEY

import getpass
api_key = getpass.getpass("請輸入金鑰：")
client = OpenAI(api_key=api_key)

### 8️⃣ 創建 GPT 3.5 模型函式

# GPT 3.5 模型
def get_reply(messages):
  try:
    response = client.chat.completions.create(model="gpt-3.5-turbo",
                         messages=messages)
    reply = response.choices[0].message.content
  except OpenAIError as err:
    reply = f"發生 {err.type} 錯誤\n{err.message}"
  return reply

# 設定 AI 角色, 使其依據使用者需求進行 df 處理
def ai_helper(df, user_msg):
  msg = [{
    "role":
    "system",
    "content":
    f"As a professional code generation robot, \
      I require your assistance in generating Python code \
      based on specific user requirements. To proceed, \
      I will provide you with a dataframe (df) that follows the \
      format {df.columns}. Your task is to carefully analyze the \
      user's requirements and generate the Python code \
      accordingly.Please note that your response should solely \
      consist of the code itself, \
      and no additional information should be included."
  }, {
    "role":
    "user",
    "content":
    f"The user requirement:{user_msg} \n\
      Your task is to develop a Python function named \
      'calculate(df)'. This function should accept a dataframe as \
      its parameter. Ensure that you only utilize the columns \
      present in the dataset, specifically {df.columns}.\
      After processing, the function should return the processed \
      dataframe. Your response should strictly contain the Python \
      code for the 'calculate(df)' function \
      and exclude any unrelated content."
  }]

  reply_data = get_reply(msg)
  return reply_data

# 產生技術指標策略
def ai_strategy(df, user_msg, add_msg="無"):
  code_example ='''
class AiStrategy(Strategy):
  def init(self):
    super().init()

  def next(self):
    if crossover(self.data.short_ma, self.data.long_ma):
        self.buy(size=1,
            sl=self.data.Close[-1] * 0.90,
            tp=self.data.Close[-1] * 1.10)
    elif crossover(self.data.long_ma, self.data.short_ma):
        self.sell(size=1,
             sl=self.data.Close[-1] * 1.10,
             tp=self.data.Close[-1] * 0.90)
        '''

  msg = [{
    "role":
    "system",
    "content":
     "As a Python code generation bot, your task is to generate \
     code for a stock strategy based on user requirements and df. \
     Please note that your response should solely \
     consist of the code itself, \
     and no additional information should be included."
  }, {
    "role":
    "user",
    "content":
     "The user requirement:計算 ma,\n\
     The additional requirement: 請設置 10% 的停利與停損點\n\
     The df.columns =['Open',	'High', 'Low',	'Close',	'Adj Close',	'Volume', 'short_ma',	'long_ma']\n\
     Please using the crossover() function in next(self)\
     Your response should strictly contain the Python \
     code for the 'AiStrategy(Strategy)' class \
     and exclude any unrelated content."
  }, {
    "role":
    "assistant",
    "content":f"{code_example}"
  }, {
    "role":
    "user",
    "content":
    f"The user requirement:{user_msg}\n\
     The additional requirement:{add_msg}\n\
     The df.columns ={df.columns}\n\
     Your task is to develop a Python class named \
     'AiStrategy(Strategy)'\
     Please using the crossover() function in next(self)."

  }]

  reply_data = get_reply(msg)
  return reply_data

### 9️⃣ 計算技術指標

# 輸入股票代號
stock_id = "2330.tw"

# 抓取 5 年資料
df = yf.download(stock_id, period="5y")

# 計算指標
user_msg = ["MACD", "請設置10%的停損點與20%的停利點"]
code_str = ai_helper(df, user_msg[0])
print(code_str)
exec(code_str)
new_df = calculate(df)
new_df.tail()

### 🔟 策略生成

strategy_str = ai_strategy(new_df, user_msg[0], user_msg[1])
print(strategy_str)
print("-----------------------")
exec(strategy_str)
backtest = Backtest(df,
        AiStrategy,
        cash=100000,
        commission=0.004,
        trade_on_close=True,
        exclusive_orders=True,
        )
stats = backtest.run()
print(stats)

### 1️⃣1️⃣ 寫成函式

def ai_backtest(stock_id, period, user_msg, add_msg):

  # 下載資料
  df = yf.download(stock_id, period=period)

  # 獲取和執行指標計算程式碼
  code_str = ai_helper(df, user_msg)
  local_namespace = {}
  exec(code_str, globals(), local_namespace)
  calculate = local_namespace['calculate']
  new_df = calculate(df)

  # 獲取和執行策略程式碼
  strategy_str = ai_strategy(new_df, user_msg, add_msg)
  print(strategy_str)
  print("-----------------------")
  exec(strategy_str, globals(), local_namespace)
  AiStrategy = local_namespace['AiStrategy']

  backtest = Backtest(df,
          AiStrategy,
          cash=100000,
          commission=0.004,
          trade_on_close=True,
          exclusive_orders=True,
          )
  stats = backtest.run()
  print(stats)
  return str(stats)
```
## 5-4 讓 AI 解析回測報告
```python
### 1️⃣2️⃣ 設定 AI 回復內容

def backtest_analysis(*args):

  content_list = [f"策略{i+1}：{report}"
                  for i, report in enumerate(args)]
  content = "\n".join(content_list)
  content += "\n\n請依資料給我一份約200字的分析報告。若有多個策略, \
                  請選出最好的策略及原因, reply in 繁體中文."

  msg = [{
      "role": "system",
      "content": "你是一位專業的證券分析師, 我會給你交易策略的回測績效,\
                  請幫我進行績效分析.不用詳細講解每個欄位, \
                  重點說明即可, 並回答交易策略的好壞"
  }, {
      "role": "user",
      "content": content
  }]

  reply_data = get_reply(msg)
  return reply_data

### 1️⃣3️⃣ 回測結果分析

stats = ai_backtest(stock_id="2330.TW",
           period="5y",
           user_msg="MACD",
           add_msg="請設置10%的停損點與20%的停利點")
reply = backtest_analysis(stats)
print(reply)

### 1️⃣4️⃣ 比較多個策略

# 策略1:MACD+停利停損
stats1 = ai_backtest(stock_id="2330.TW", period="5y",
            user_msg="MACD",
            add_msg="請設置10%的停損點與20%的停利點")

# 策略2:SMA
stats2 = ai_backtest(stock_id="2330.TW", period="5y",
            user_msg="SMA",
            add_msg="無")

# 策略3:RSI+停利停損
stats3 = ai_backtest(stock_id="2330.TW", period="5y",
            user_msg="RSI",
            add_msg="請設置10%的停損點與20%的停利點")

reply = backtest_analysis(stats1, stats2, stats3)
print(reply)


```
