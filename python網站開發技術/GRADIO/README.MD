# 參考書籍:OpenAI API 基礎必修課 
- Chapter 4 Gradio 互動式網頁
  - 4.1 認識 Gradio
　- 4.2 Gradio 基本語法介紹
　- 4.3 Gradio 常用的輸出入元件
　- 4.4 運用實例
# 可直接在Google Colab跑

!pip install gradio

import gradio as gr

# OPENAI

!pip install openai

```python
import openai

from google.colab import userdata
OPENAI_API_KEY= userdata.get('GenAI20240912')

client = openai.OpenAI(api_key=OPENAI_API_KEY)
```
