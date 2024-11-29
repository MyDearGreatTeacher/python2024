# CH02_從零開始的OpenAI_API

## 2-3 建構自己的 AI 機器人

### 1️⃣ 使用 OpenAI API 官方套件
- OpenAI 官方提供 openai 套件, 可以簡化使用上的複雜度。
- !pip install openai

### 2️⃣ 輸入 API KEY
- getpass 套件可以隱藏輸入值
```python
from openai import OpenAI, OpenAIError # OpenAI 官方套件
import getpass # 保密輸入套件
api_key = getpass.getpass("請輸入金鑰：")
client = OpenAI(api_key = api_key) # 建立 OpenAI 物件
```
### 3️⃣ 建構模型並交談
```python
reply = client.chat.completions.create(
    model = "gpt-3.5-turbo",
    # model = "gpt-4",
    messages = [
        {"role":"user", "content": "你住的地方很亮嗎？"}
    ]
)
```
### 4️⃣ 檢視傳回物件

print(reply)

### 5️⃣ 檢視訊息內容

print(reply.choices[0].message.content)

### 6️⃣ 設定 AI 角色
```python
reply = client.chat.completions.create(
    model = "gpt-3.5-turbo",
    messages = [
        {"role":"system", "content":"你是隻住在外太空的猴子"},
        {"role":"user", "content": "你住的地方很亮嗎？ reply in 繁體中文"}
    ]
)

print(reply.choices[0].message.content)
```
### 7️⃣ 寫成函式
```python
def get_reply(messages):
    try:
        response = client.chat.completions.create(
            model = "gpt-3.5-turbo",
            messages = messages
        )
        reply = response.choices[0].message.content
    except OpenAIError as err:
        reply = f"發生 {err.error.type} 錯誤\n{err.error.message}"
    return reply
```

### 8️⃣ 簡易的對談程式
```python
while True:
    msg = input("你說：")
    if not msg.strip(): break
    messages = [{"role":"user", "content":msg}]
    reply = get_reply(messages)
    print(f"ㄟ唉：{reply}\n")

```
### 9️⃣ 記憶對話紀錄的函式
```python
hist = []       # 歷史對話紀錄
backtrace = 2   # 記錄幾組對話

def chat(sys_msg, user_msg):
    hist.append({"role":"user", "content":user_msg})
    reply = get_reply(hist
                      + [{"role":"system", "content":sys_msg}])
    while len(hist) >= 2 * backtrace: # 超過記錄限制
        hist.pop(0)                   # 移除最舊紀錄
    hist.append({"role":"assistant", "content":reply})
    return reply
```
### 🔟 能接續對話的 AI 程式
```python
sys_msg = input("你希望ㄟ唉扮演：")
if not sys_msg.strip(): sys_msg = '小助理'
print()
while True:
    msg = input("你說：")
    if not msg.strip(): break
    reply = chat(sys_msg, msg)
    print(f"{sys_msg}:{reply}\n")
hist = []
```
### 1️⃣1️⃣ 安裝與匯入 google 搜尋套件
```python
!pip install googlesearch-python
from googlesearch import search
```
### 1️⃣2️⃣ 使用 google 搜尋套件
```
for item in search(
    "NBA 2023 冠軍隊", advanced=True, num_results=3):
    print(item.title)
    print(item.description)
    print(item.url)
    print()
```
### 1️⃣3️⃣ 將搜尋結果加入到 content 中
```python
hist = []       # 歷史對話紀錄
backtrace = 2   # 記錄幾組對話

def chat_w(sys_msg, user_msg, search_g = True):
    web_res = []
    if search_g == True: # 代表要搜尋網路
        content = "以下為已發生的事實：\n"
        for res in search(user_msg, advanced=True,
                          num_results=3, lang='zh-TW'):
            content += f"標題：{res.title}\n" \
                       f"摘要：{res.description}\n\n"
        content += "請依照上述事實回答問題 \n"
        web_res = [{"role": "user", "content": content}]
    web_res.append({"role": "user", "content": user_msg})
    while len(hist) >= 2 * backtrace: # 超過記錄限制
        hist.pop(0)  # 移除最舊的紀錄
    reply_full = ""
    for reply in get_reply(
        hist                          # 先提供歷史紀錄
        + web_res                     # 再提供搜尋結果及目前訊息
        + [{"role": "system", "content": sys_msg}]):
        reply_full += reply           # 記錄到目前為止收到的訊息
        yield reply                   # 傳回本次收到的片段訊息
    hist.append({"role": "user", "content": user_msg})
    while len(hist) >= 2 * backtrace: # 超過記錄限制
        hist.pop(0)                   # 移除最舊紀錄
    hist.append({"role":"assistant", "content":reply_full})
```
### 1️⃣4️⃣ 突破搜尋限制的聊天機器人
```python
sys_msg = '小助理'

while True:
    msg = input("你說：")
    if not msg.strip(): break
    print(f"{sys_msg}：", end = "")
    for reply in chat_w(sys_msg, msg, search_g = True):
        print(reply, end = "")
    print('\n')
hist = []
```
