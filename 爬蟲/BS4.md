# Beautiful Soup
- Beautiful Soup 是一個 Python 的函式庫模組
- 可以讓開發者僅須撰寫非常少量的程式碼，就可以快速解析網頁 HTML 碼，從中翠取出使用者有興趣的資料、去蕪存菁，降低網路爬蟲程式的開發門檻、加快程式撰寫速度。
- Beautiful Soup 這套模組的網頁結構搜尋與萃取功能相當完整
- 我們只介紹比較常用的幾種功能，更詳細的用法請參考
- Beautiful Soup 官方的說明文件
  - https://www.crummy.com/software/BeautifulSoup/bs4/doc/
  - https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/ 
- Beautiful Soup 的運作方式
  - 讀取 HTML 原始碼，自動進行解析並產生一個 BeautifulSoup 物件
  - 此物件中包含了整個 HTML 文件的結構樹
  - 有了這個結構樹之後，就可以輕鬆找出任何有興趣的資料了。

# 參考資料
- [Python 使用 Beautiful Soup 抓取與解析網頁資料，開發網路爬蟲教學](https://blog.gtwang.org/programming/python-beautiful-soup-module-scrape-web-pages-tutorial/)

```python
# 引入 Beautiful Soup 模組
from bs4 import BeautifulSoup

# 原始 HTML 程式碼
html_doc = """
<html><head><title>Hello World</title></head>
<body><h2>Test Header</h2>
<p>This is a test.</p>
<a id="link1" href="/my_link1">Link 1</a>
<a id="link2" href="/my_link2">Link 2</a>
<p>Hello, <b class="boldtext">Bold Text</b></p>
</body></html>
"""

# 以 Beautiful Soup 解析 HTML 程式碼
soup = BeautifulSoup(html_doc, 'html.parser')

# 輸出排版後的 HTML 程式碼
print(soup.prettify())

# 取得節點文字內容
"""
若要輸出網頁標題的 HTML 標籤，可以直接指定網頁標題標籤的名稱（title），即可將該標籤的節點抓出來：

# 網頁標題 HTML 標籤
"""
title_tag = soup.title
print(title_tag)

# 取得網頁的標題文字
### HTML 標籤節點的文字內容，可以透過 string 屬性存取：

print(title_tag.string)

# 搜尋節點
### 可以使用 find_all 找出所有特定的 HTML 標籤節點
### 再以 Python 的迴圈來依序輸出每個超連結的文字：

# 所有的超連結
a_tags = soup.find_all('a')
for tag in a_tags:
  # 輸出超連結的文字
  print(tag.string)

# 取出節點屬性
### 若要取出 HTML 節點的各種屬性，可以使用 get，例如輸出每個超連結的網址（href 屬性）：

for tag in a_tags:
  # 輸出超連結網址
  print(tag.get('href'))

# 同時搜尋多種標籤
## 若要同時搜尋多種 HTML 標籤，可以使用 list 來指定所有的要列出的 HTML 標籤名稱：
# 搜尋所有超連結與粗體字

tags = soup.find_all(["a", "b"])
print(tags)

# 限制搜尋節點數量
## find_all 預設會輸出所有符合條件的節點
## 但若是遇到節點數量很多的時候，就會需要比較久的計算時間
## 如果我們不需要所有符合條件的節點，可以用 limit 參數指定搜尋節點數量的上限值
## 這樣它就只會找出前幾個符合條件的節點：
# 限制搜尋結果數量
tags = soup.find_all(["a", "b"], limit=2)
print(tags)


```
