# powerpoint檔案存取
- [Python x AI 辦公室作業自動化](https://www.tenlong.com.tw/products/9786267569177?list_name=lv)
  - 第31 章 Python 操作PowerPoint 簡報
  - 31-1 認識與安裝python-pptx 模組

# 安裝套件 ==> py -m pip install python-pptx
```
D:\py202411\ch31>py -m pip install python-pptx
Collecting python-pptx
  Downloading python_pptx-1.0.2-py3-none-any.whl.metadata (2.5 kB)
Requirement already satisfied: Pillow>=3.3.2 in c:\users\ksu\anaconda3\lib\site-packages (from python-pptx) (10.2.0)
Collecting XlsxWriter>=0.5.7 (from python-pptx)
  Downloading XlsxWriter-3.2.0-py3-none-any.whl.metadata (2.6 kB)
Requirement already satisfied: lxml>=3.1.0 in c:\users\ksu\anaconda3\lib\site-packages (from python-pptx) (4.9.3)
Requirement already satisfied: typing-extensions>=4.9.0 in c:\users\ksu\anaconda3\lib\site-packages (from python-pptx) (4.9.0)
Downloading python_pptx-1.0.2-py3-none-any.whl (472 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 472.8/472.8 kB 845.6 kB/s eta 0:00:00
Downloading XlsxWriter-3.2.0-py3-none-any.whl (159 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 159.9/159.9 kB 4.7 MB/s eta 0:00:00
Installing collected packages: XlsxWriter, python-pptx
Successfully installed XlsxWriter-3.2.0 python-pptx-1.0.2
```

## 範例
```python
# ch31_1.py
from pptx import Presentation

# 創建一個新的簡報
prs = Presentation()

# 列出所有可用的投影片主題樣式
for index, layout in enumerate(prs.slide_layouts):
    print(f"Layout {index + 1}: {layout.name}")
```

```
D:\py202411\ch31>py ch31_1.py
Layout 1: Title Slide  ==> 標題投影片
Layout 2: Title and Content
Layout 3: Section Header
Layout 4: Two Content
Layout 5: Comparison
Layout 6: Title Only
Layout 7: Blank
Layout 8: Content with Caption
Layout 9: Picture with Caption
Layout 10: Title and Vertical Text
Layout 11: Vertical Title and Text
```
## 簡報 = 投影片(slide) + 投影片(slide) + 投影片(slide) + 投影片(slide) + 
- slide.shapes
- slide.placeholders
- slide.background

## 範例  建立標題投影片(Title Slide)
```python
# ch31_3.py
from pptx import Presentation

prs = Presentation()                        # 建立簡報物件

# 選取標題投影片的版面配置, 新增一張使用標題版面配置的投影片
title_slide_layout = prs.slide_layouts[0]           
slide = prs.slides.add_slide(title_slide_layout)

title = slide.placeholders[0]               # 取得投影片的標題物件
title.text = "Python PowerPoint存取"         # 設定標題文字

subtitle = slide.placeholders[1]            # 投影片的副標題佔位符
subtitle.text = "A888168 \n  龍大大"  # 設定副標題文字

prs.save('out3.pptx')                       # 簡報儲存 
```


## 範例 31-3 標題及內容投影片
```python
# ch31_5.py
from pptx import Presentation

prs = Presentation()                        # 建立簡報物件

# 增加標題及內容投影片
slide_layout2 = prs.slide_layouts[1]           
slide = prs.slides.add_slide(slide_layout2)

slide_title = slide.shapes.title            # 標題物件
slide_title.text = "崑山科技大學"           # 設定標題文字

slide_body = slide.placeholders[1]          # 投影片內容佔位符
tf = slide_body.text_frame
p1 = tf.add_paragraph()                     # 新增加段落
p1.text = "工學院"

p1_indent = tf.add_paragraph()              # 新增加段落
p1_indent.text = "機械系"
p1_indent.level = 1                         # 內縮等級 1
p1_indent.deeper = tf.add_paragraph()       # 新增加段落
p1_indent.deeper.text = "車輛組"
p1_indent.deeper.level = 2                  # 內縮等級 2


prs.save('out5.pptx')                       # 簡報儲存
```


## 範例
```python

```


## 範例
```python

```


## 範例
```python

```


## 範例
```python

```


## 範例
```python

```


## 範例
```python

```


## 範例
```python

```


## 範例
```python

```

