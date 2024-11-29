# CH03股市資料蒐集_爬蟲與搭建資料庫

## 3-2 資料爬蟲

## 證交所資料爬蟲
- 1.進入證交所網址：https://www.twse.com.tw/zh/index.html
- 2.使用開發者模式取得請求資料網址

```python
### 1️⃣ 匯入套件

import requests
import pandas as pd
import datetime as dt # 時間套件
from dateutil.relativedelta import relativedelta

### 2️⃣ 取得個股日成交資訊"""

# 輸入股票代號
stock_id = '2330'
# 當日時間
date = dt.date.today().strftime("%Y%m%d")
# 取得證交所網站資料
stock_data = requests.get(f'https://www.twse.com.tw/rwd/zh/ \
            afterTrading/STOCK_DAY?date={date}&stockNo={stock_id}')
json_data = stock_data.json()
df = pd.DataFrame(data=json_data['data'],
                  columns=json_data['fields'])
df.tail()

### 3️⃣ 取得連續月份資料(以個股本益比為例)

# 設定抓取幾個月資料
month_num=3
date_now = dt.datetime.now()

# 建立日期串列
date_list = [(date_now - relativedelta(months=i)).replace(day=1).\
             strftime('%Y%m%d') for i in range(month_num)]

date_list.reverse()
all_df = pd.DataFrame()

# 使用迴圈抓取連續月份資料
for date in date_list:
  url = f'https://www.twse.com.tw/rwd/zh/afterTrading/\
      BWIBBU?date={date}&stockNo={stock_id}'
  try:
    json_data = requests.get(url).json()
    df = pd.DataFrame(data=json_data['data'],
                  columns=json_data['fields'])
    all_df = pd.concat([all_df, df], ignore_index=True)
  except Exception as e:
    print(f"無法取得{date}的資料, 可能資料量不足.")

all_df.head()
```
## 用 BeautifulSoup4 取得 Yahoo 股市資料
```python

###4️⃣ 匯入套件
from datetime import datetime
from bs4 import BeautifulSoup
import time

###  5️⃣ 取得當日股價

def yahoo_stock(stock_id):
    url = f'https://tw.stock.yahoo.com/quote/{stock_id}.TW'
    # 使用 requests 取得網頁內容
    response = requests.get(url)
    html = response.content
    # 使用 Beautiful Soup 解析 HTML 內容
    soup = BeautifulSoup(html, 'html.parser')
    # 使用 find 與 find_all 定位元素
    time_element = soup.find('section',\
                {'id': 'qsp-overview-realtime-info'}).find('time')
    table_soups = soup.find('section',\
                {'id': 'qsp-overview-realtime-info'}).find('ul')\
                                   .find_all('li')
    fields = []
    datas = []
    for table_soup in table_soups:
        table_datas = table_soup.find_all('span')
        for num,table_data in enumerate(table_datas):
            if table_data.text =='':
                continue
            if num == 0:
                fields.append(table_data.text)
            else:
                datas.append(table_data.text)
    # 建立 DataFrame
    df = pd.DataFrame([datas], columns=fields)
    # 增加日期和股號欄位
    df.insert(0,'日期',time_element['datatime'])
    df.insert(1,'股號',stock_id)
    # 回傳 DataFrame
    return df

yahoo_stock(stock_id)

### 6️⃣ 取得季報表資訊
# 函式可用於奇摩財報
def url_find(url):
    words = url.split('/')
    k = words[-1]
    # 使用requests取得網頁內容
    response = requests.get(url)
    html = response.content
    # 使用Beautiful Soup解析HTML內容
    soup = BeautifulSoup(html, 'html.parser')
    # 找到表格的表頭
    table_soup = soup.find('section', {'id': 'qsp-{}-table'.format(k)})
    table_fields=table_soup.find('div', class_='table-header')
    table_fields_lines = list(table_fields.stripped_strings)
    # 找到數據
    data_rows = table_soup.find_all('li' ,class_='List(n)')
    # 解析資料行內容
    data = []
    for row in data_rows:
        row_data = list(row.stripped_strings)
        data.append(row_data)
    # 建立 DataFrame
    df = pd.DataFrame(data, columns=table_fields_lines)
    return df

# 抓損益表
url = f'https://tw.stock.yahoo.com/quote/{stock_id}/income-statement'

# 抓資產負債表
# url = f'https://tw.stock.yahoo.com/quote/{stock_id}/balance-sheet'

# 抓現金流量表
# url = f'https://tw.stock.yahoo.com/quote/{stock_id}/cash-flow-statement'

# 抓取季報表資料
df = url_find(url).transpose()

# 資料處理
df.columns = df.iloc[0]
df = df[1:]
df.insert(0,'年度/季別',df.index)
df.columns.name = None
df.reset_index(drop=True, inplace=True)

df.tail()
```
## 使用 selenium 做新聞爬蟲
```python

### 7️⃣ 用 requests 取得股票新聞

# 股票代號
stock_id = "2330"
# 預設表格數據和欄位
field=['股號','日期','標題','內容']
data=[]
# 取得 Json 格式資料
json_data = requests.get(f'https://ess.api.cnyes.com/ess/api/'
                f'v1/news/keyword?q={stock_id}&limit=20&page=1').json()
# 依照格式擷取資料
items=json_data['data']['items']
for item in items:
    # 網址、標題和日期
    news_id = item["newsId"]
    title = item["title"]
    publish_at = item["publishAt"]
    # 使用 UTC 時間格式
    utc_time = datetime.utcfromtimestamp(publish_at)
    formatted_date = utc_time.strftime('%Y-%m-%d')
    # 前往網址擷取內容
    url = requests.get(f'https://news.cnyes.com/'
                       f'news/id/{news_id}').content
    soup = BeautifulSoup(url, 'html.parser')
    p_elements = soup.find_all('p')
    # 提取段落内容
    p=''
    for paragraph in p_elements[4:]:
        p+=paragraph.get_text()
    data.append([stock_id, formatted_date ,title,p])
# 建立表格
df = pd.DataFrame(data,columns=field)
df
```
### 8️⃣  安裝及匯入套件"""
```python
!pip install selenium
from selenium import webdriver
chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument('--headless')  # 不顯示瀏覽器
chrome_options.add_argument('--no-sandbox')# 以最高權限運行

### 9️⃣ 使用 Selenium 取得股票新聞

# 透過 options 設定 driver
driver = webdriver.Chrome(options=chrome_options)
data2=[] # 表格數據

# 目標網址
url = f"https://www.cnyes.com/search/news?keyword={stock_id}"
driver.get(url)

# 模擬滑動滑鼠滾輪的行為，用於加載更多內容
scroll_pause_time = 2  # 等待時間
last_height = driver.execute_script(
                    "return document.body.scrollHeight")
while True:
    driver.execute_script(
        "window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(scroll_pause_time)
    new_height = driver.execute_script(
        "return document.body.scrollHeight")
    if new_height == last_height:
        break
    last_height = new_height

elements = driver.find_elements("xpath",
                        '//*[@id="_SearchAll"]/section/div/a')

# 擷取網址和標題
for element in elements:
    link = element.get_attribute("href")
    title = element.text
    title=title.split('\n')
    data2.append([stock_id, title[1] ,title[0],link])

# 關閉瀏覽器
driver.quit()
for link in data2:
  # 使用 requests 前往網址擷取新聞內容
  link_a = requests.get(link[3]).content
  link_b = BeautifulSoup(link_a,'html.parser')
  p_elements = link_b.find('article')
  # 取得段落内容
  try:
    link[3] = p_elements.get_text()
  except:
    link[3] = '無內容'
# 建立表格
df = pd.DataFrame(data2,columns=field)
df.tail()

# 把資料存成 csv 可以用以下這段
df.to_csv('/content/news_data.csv' )
```
# 3.3 用 Python 套件輕鬆取得股市資料

## 使用 yfinance 下載股市資料
```python

### 🔟  安裝及匯入套件

!pip install yfinance==0.2.38

import yfinance as yf

### 1️⃣1️⃣  設定股票代碼和起止時間

# 指定要下載的股票代碼, 上市為 .TW;上櫃為 .TWO
stock_id = '2330.TW'

# 設定開始與結束時間
end = dt.date.today()
start = end - dt.timedelta(days=360)

### 1️⃣2️⃣ 取得每日股價 (開高低收) 資料
stock_data = yf.download(stock_id, start=start, end=end)
stock_data.tail()

# 依照資料期間下載
stock_data = yf.download(stock_id, period="3mo")

# 下載不同時間頻率的資料 (1分K)
stock_data = yf.download(stock_id, interval="1m")

### 1️⃣3️⃣ 取得多檔股票的資料

stocks = [stock_id, '2303.TW', '2454.TW'] #分別為台積電、聯電和聯發科
stock_data = yf.download(stocks, start=start, end=end)
stock_data.tail()

### 1️⃣4️⃣ 取得公司基本資料


stock = yf.Ticker(stock_id)
stock.info # 為字典型態

### 1️⃣5️⃣  取得損益表"""

financials = stock.financials
financials.tail()

bs = stock.balance_sheet
bs

### 1️⃣6️⃣ 取得法人持股比例
#### 因 yfinance 資料問題，此儲存格暫時無法使用
institutional_holders = stock.institutional_holders
institutional_holders.tail()
```
## 使用 FinMind 下載股市資料
```python
### 1️⃣7️⃣  安裝及匯入套件

!pip install FinMind

from FinMind.data import DataLoader
import getpass

### 1️⃣8️⃣  輸入 FinMind API 和帳號密碼

token = getpass.getpass("請輸入 FinMind 金鑰：")

### 1️⃣9️⃣  建立 FinMind 資料庫物件和登入 FinMind

api = DataLoader()
api.login_by_token(api_token=token)

### 2️⃣0️⃣ 取得股價資料

# 設定股票代號
stock_id = '2330'

# 資料期間
end = dt.date.today()
start = end - dt.timedelta(days=360)

stock_data =  api.taiwan_stock_daily(
    stock_id=stock_id,
    start_date=start,
    end_date=end)

stock_data.tail()

### 2️⃣1️⃣ 取得損益表資料

financial_data = api.taiwan_stock_financial_statement(
    stock_id=stock_id,
    start_date=str(start),)

financial_data.tail()

### 2️⃣2️⃣ 取得法人買賣資料

investors_data = api.taiwan_stock_institutional_investors(
    stock_id=stock_id,
    start_date=str(start),)
investors_data.tail()
```
## 使用 FinLab 下載股市資料
- 注意！FinLab 目前需付費才能取得最新資料
```python

### 2️⃣3️⃣ 安裝及匯入套件
!pip install finlab

import finlab
from finlab import data

### 2️⃣4️⃣ 登入 FinLab

token = getpass.getpass("請輸入FinLab金鑰：")
finlab.login(token)

### 2️⃣5️⃣ 取得收盤價

close = data.get('price:收盤價')
close.tail()

### 2️⃣6️⃣ 選擇產業

data.set_universe(market='TSE', category='半導體')
close = data.get('price:收盤價')
close.tail()

### 2️⃣7️⃣ 取得財報資料

df = data.get('financial_statement:每股盈餘')
df.tail()

### 2️⃣8️⃣  取得法人資料

df = data.get('institutional_investors_trading_summary:投信買賣超股數')
df.tail()
```
# 3.4 SQL資料庫
```python
### 2️⃣9️⃣ 匯入套件及連線資料庫

import sqlite3
conn = sqlite3.connect('stock.db')

### 3️⃣0️⃣設定資料庫結構

cursor = conn.cursor()
cursor.execute('''
CREATE TABLE IF NOT EXISTS 日頻資料 (
    sno INTEGER PRIMARY KEY AUTOINCREMENT,
    Stock_Id TEXT,
    Date DATE,
    Open FLOAT,
    High FLOAT,
    Low FLOAT,
    Close FLOAT,
    Adj_Close FLOAT,
    Volume INTEGER
);
''')
conn.commit()

### 3️⃣1️⃣ 傳入資料到資料庫

df = yf.download('2330.TW',start='2023-08-01')
df = df.reset_index()
df['Date'] = df['Date'].dt.strftime('%Y-%m-%d')
df.rename(columns={"Adj Close": "Adj_Close"}, inplace=True)
df.insert(0,'Stock_id','2330')

df.to_sql('日頻資料',conn,if_exists='append',index=False)

### 3️⃣2️⃣ 查詢表格資料

def table_info(table_name):
    cursor = conn.cursor()
    cursor.execute(f"PRAGMA table_info({table_name})")
    columns = cursor.fetchall()
    column_names = [column[1] for column in columns]
    print(f"資料庫表 '{table_name}' 的欄位名稱：", column_names)
    all_data = conn.execute(f'SELECT * FROM {table_name}')
    for i in all_data.fetchall():
        print(i)

# 查詢表格資料
table_info("日頻資料")

### 3️⃣3️⃣ 新增資料

def insert_data(stock_id, start):
  # 下載資料
  df = yf.download(f'{stock_id}.TW',start=start)
  df = df.reset_index()
  df.rename(columns={"Adj Close": "Adj_Close"}, inplace=True)
  df['Date'] = df['Date'].dt.strftime('%Y-%m-%d')
  df.insert(0,'Stock_Id',stock_id)

  # 新增資料表
  df.to_sql('日頻資料',conn,if_exists='append',index=False)

# 新增 2317 資料
insert_data(stock_id=2317, start='2023-08-01')

### 3️⃣4️⃣ 從資料庫取得資料 ==> 資料課查詢

query = ("SELECT Stock_id, Date, Close "
         "FROM 日頻資料 "
         "WHERE Date < '2023-08-15' AND Stock_id = '2317'")
df = pd.read_sql(query, conn, parse_dates=['Date'])
df.tail()

### 3️⃣5️⃣ 修改並關閉資料庫

conn.commit()
conn.close()
```
