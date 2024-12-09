# yfinance模組技巧
- [yfinance GitHub原始碼](https://github.com/ranaroussi/yfinance)
- [yfinance 攻略！Python 下載股票價格數據無難度](https://pythonviz.com/finance/yfinance-download-stock-data/)

## 實作
- [元大台灣50 (0050.TW)](https://hk.finance.yahoo.com/quote/0050.TW?p=0050.TW&.tsrc=fin-srch)
- GOOGLE COLAB
  - !pip install yfinance
  - 範例程式
```python
import yfinance as yf
a=yf.download('0050.tw',start='2022-12-20',end='2022-12-26')
a
```
- type(a)

## [元大台灣50反1 (00632R.TW)](https://hk.finance.yahoo.com/quote/00632R.TW?p=00632R.TW)
```python
import yfinance as yf

a=yf.download('00632R.tw',period='7d',interval='1m')
a
```
- period == > 7天
- interval  == >每一分鐘

## [yfinance 攻略！Python 下載股票價格數據無難度](https://pythonviz.com/finance/yfinance-download-stock-data/)
- 單一股票
```python
import yfinance as yf

tsm = yf.Ticker(‘TSM’)
tsla = yf.Ticker(‘TSLA’)
tsm.info	
tsm.financials	
tsm.earnings	
tsm.actions	
tsm.history()

```
- 多隻股票下載
```python
b = yf.download('TSM TSLA',start='2016-01-01',end='2021-01-01')
b
```
