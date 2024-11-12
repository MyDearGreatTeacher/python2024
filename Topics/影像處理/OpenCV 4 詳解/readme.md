# [OpenCV 4 詳解：基於 Python|馮振，陳亞萌](https://www.tenlong.com.tw/products/9787115566034)

## 2-1 圖像的表示


```python
# -*- coding:utf-8 -*-
# 
import cv2 as cv
import sys


if __name__ == '__main__':
    # 讀取圖像並判斷是否讀取成功
    img = cv.imread('./images/flower.jpg')
    if img is None:
        print('Failed to read flower.jpg.')
        sys.exit()
    else:
        print('圖像的形狀為：{}\n元素資料類型為：{}\n圖像通道數為：{}\n圖元總數為：{}'
              .format(img.shape, img.dtype, img.ndim, img.size))
```
```
D:\CV_2024\chapter2>py Numpy_Attributes.py
图像的形状为：(442, 442, 3)
元素数据类型为：uint8
图像通道数为：3
像素总数为：586092

D:\CV_2024\chapter2>py 2_1.py
圖像的形狀為：(442, 442, 3)
元素資料類型為：uint8
圖像通道數為：3
圖元總數為：586092
```
## 2-2 圖片的讀取與顯示
```python
# 2-2.py
# -*- coding:utf-8 -*-
import cv2 as cv
import numpy as np
import datetime
import sys


if __name__ == '__main__':
    # 創建ndarray對象
    # 使用np.array()創建一個5*5，資料類型為float32的物件
    a = np.array([[1, 2, 3, 4, 5],
                  [6, 7, 8, 9, 10],
                  [11, 12, 13, 14, 15],
                  [16, 17, 18, 19, 20],
                  [21, 22, 23, 24, 25]], dtype='float32')
    # 使用np.ones()創建一個5*5，資料類型為uint8的全1對象
    b = np.ones((5, 5), dtype='uint8')
    # 使用np.zeros()創建一個5*5，資料類型為float32的全0對象
    c = np.zeros((5, 5), dtype='float32')
    print('創建對象（np.array）：\n{}'.format(a))
    print('創建對象（np.ones）：\n{}'.format(b))
    print('創建對象（np.zeros）：\n{}'.format(c))

    # ndarray物件切片和索引
    image = cv.imread('./images/flower.jpg')
    # 判斷圖片是否讀取成功
    if image is None:
        print('Failed to read flower.jpg.')
        sys.exit()
    gray = cv.cvtColor(image, cv.COLOR_BGR2GRAY)
    # 讀取圖像位於（45,45）位置的圖元點
    print('位於（45,45）位置的圖元點為：{}'.format(gray[45, 45]))
    # 裁剪部分圖像（灰度圖像和RGB圖像）
    res_gray = gray[40:280, 60:340]
    res_color1 = image[40:280, 60:340, :]
    res_color2 = image[100:220, 80:220, :]
    # 通道分離
    b = image[:, :, 0]
    g = image[:, :, 1]
    r = image[:, :, 2]
    # 展示裁剪和分離通道結果
    cv.imshow('Result crop gray', res_gray)
    cv.imshow('Result crop color1', res_color1)
    cv.imshow('Result crop color2', res_color2)
    cv.imshow('Result split b', b)
    cv.imshow('Result split g', g)
    cv.imshow('Result split r', r)

    # 生成亂數
    # 生成一個5*5，取值範圍在0-100的陣列
    values1 = np.random.randint(0, 100, (5, 5), dtype='uint8')
    # 生成一個2*3，元素服從平均值為0、標準差為1正態分佈的陣列
    values2 = np.random.randn(2, 3)
    print('生成亂數（np.random.randint）：\n{}'.format(values1))
    print('生成亂數（np.random.randn）：\n{}'.format(values2))

    cv.waitKey(0)
    cv.destroyAllWindows()

```
