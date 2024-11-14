# 第 7 章 檔案存取
```
7-1　認識檔案路徑
7-2　寫入檔案
7-2-1　建立檔案物件
7-2-2　將資料寫入檔案
7-3　讀取檔案
7-3-1　使用 read() 方法從檔案讀取資料
7-3-2　使用 readline() 方法從檔案讀取資料 
7-3-3　使用 readlines() 方法從檔案讀取資料 
7-4　with 敘述
7-5　管理檔案與資料夾
```
# 讀寫檔案(文字檔)
- 讀取檔案  
  - open(file, mode)
  - readline
- 寫入檔案 write ==>  fileObject.write( [ str ])

# 讀取檔案 
- open(file, mode)  [Python open() 函数](https://www.runoob.com/python/python-func-open.html)
  - "r" - Read - Default value. Opens a file for reading, error if the file does not exist
  - "a" - Append - Opens a file for appending, creates the file if it does not exist
  - "w" - Write - Opens a file for writing, creates the file if it does not exist
  - "x" - Create - Creates the specified file, returns an error if the file exist 
  - "t" - Text - Default value. Text mode
  - "b" - Binary - Binary mode (e.g. images)

### copyfile.py 
```python
import os.path   //os 模組
import sys       //sys 模組

sourcefile = input("請輸入來源檔案名稱 (*.txt)：")
targetfile = input("請輸入目的檔案名稱 (*.txt)：")

if os.path.isfile(targetfile):
    print("目的檔案已經存在，取消複製檔案！")
    sys.exit()
    
//先產生  來源檔案  與  目的檔案  的 物件(object)
fileObject1 = open(sourcefile, "r")
fileObject2 = open(targetfile, "w")

//使用 fileObject1物件的read()方法讀取資料 並寫入到content
content = fileObject1.read()

//使用 fileObject2物件的write()方法寫入content到目的檔案
fileObject2.write(content)

//每一個開啟的物件(fileObject1, fileObject2)使用close()方法關閉 (記憶體釋放)
fileObject1.close()
fileObject2.close()
```

### file1.py
```python
# 使用 "a" 模式開啟檔案以將資料附加到原檔案內容的後面
fileObject = open("./poem2.txt", "a")

# 寫入檔案，\n 字元表示換行
fileObject.write("\n鳳凰臺上鳳凰遊，鳳去臺空江自流。")
fileObject.write("\n吳宮花草埋幽徑，晉代衣冠成古邱。")
fileObject.write("\n三山半落青又外，二水中分白鷺洲。")
fileObject.write("\n總為浮雲能蔽日，長安不見使人愁。")

# 關閉檔案
fileObject.close()
```

### file2.py
```python

def replaceSymbols(string):
    for char in string:
        if char in "~!@#$%^&()[]{},+-*|/?<>'.;:\"":
            string = string.replace(char, ' ')
    return string        

def counts(string):
    wordlist = string.split()
    for word in wordlist:
        if word in result:
            result[word] = result[word] + 1
        else:
            result[word] = 1

fileObject = open("PPAP.txt", "r")
song = fileObject.read()
fileObject.close()

result = {}
tmp = replaceSymbols(song.lower())
counts(tmp)

print(result)
```

## mkdir.py 
```python
import os
dir = "./photo"
if not os.path.exists(dir):
    os.mkdir(dir)
else:
    print("此資料夾已經存在")
```


## rmdir.py 
```python
import os
dir = "./photo"
if os.path.exists(dir):
    os.rmdir(dir)
else:
    print("此資料夾不存在")

```


## remove.py 
```python
import os
file = "./poem.txt"
if os.path.exists(file):
    os.remove(file)
else:
    print("此檔案不存在")
```

## readlines.py
```python
fileObject = open("poem.txt", "r")

content = fileObject.readlines()
for line in content:
    print(line)

fileObject.close()
```

## readline1.py 
```python
fileObject = open("poem2.txt", "r")

line = fileObject.readline()
while line != '':
    print(line)
    line = fileObject.readline()

fileObject.close()
```

## readline2.py 
```python
import os.path

if os.path.isfile("poem2.txt"):
    fileObject = open("poem2.txt", "r")
    for line in fileObject:
        print(line)
    fileObject.close()
else:
    print("此檔案不存在")
```


##
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
# 打開文件
fo = open("runoob.txt", "r")
print("檔案名為: ", fo.name)
 
for line in fo.readlines():                          #依次讀取每行  
    line = line.strip()                             #去掉每行頭尾空白  
    print("讀取的數據為: %s" % (line))
 
# 關閉文件
fo.close()
```


## 作業
```python
1 #!/usr/bin/python
2 # -*- coding: UTF-8 -*-
3  
4 # 打開文件
5 fo = open("runoob.txt", "r")
6 print("檔案名為: ", fo.name)
7 
8 for line in fo.readlines():                          #依次讀取每行  
9     line = line.strip()                             #去掉每行頭尾空白  
10    print("讀取的數據為: %s" % (line))
 
# 關閉文件
fo.close()

```
