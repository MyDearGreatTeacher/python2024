# 資料來源
- https://github.com/PacktPublishing/Learning-Pandas-Second-Edition/blob/master/Chapter04/04_Table_And_MultiVariate_Data_with_The_DataFrame.ipynb

## 測驗內容
- 下載資料集
  - !wget https://raw.githubusercontent.com/PacktPublishing/Learning-Pandas-Second-Edition/master/data/sp500.csv
- sp500.head()
- len(sp500)
- sp500.shape
- sp500.size
- sp500.index
- sp500.columns
- sp500['Sector'].head()
- type(sp500['Sector'])
- sp500[['Price', 'Book Value']].head()
- type(sp500[['Price', 'Book Value']])
- sp500.Price
- sp500.loc['MMM']
- sp500.loc[['MMM', 'MSFT']]
- sp500.iloc[[0, 2]]
```
i1 = sp500.index.get_loc('MMM')
i2 = sp500.index.get_loc('A')
(i1, i2)
```
- sp500.iloc[[i1, i2]]
- sp500.at['MMM', 'Price']
- sp500.iat[0, 1]
- sp500[:5]
- sp500['ABT':'ACN']
- sp500.Price < 100
- sp500[sp500.Price < 100]
```
r = sp500[(sp500.Price < 10) & 
          (sp500.Price > 6)] ['Price']
r
```
```
r = sp500[(sp500.Sector == 'Health Care') & 
          (sp500.Price > 100.00)] [['Price', 'Sector']]
r
```
- sp500.loc[['ABT', 'ZTS']][['Sector', 'Price']]
