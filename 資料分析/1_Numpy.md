## numpy 學習主題
```
1.資料型態 ==> N-Dimensional Arrays(ndarray)
ndarray的屬性: 軸(axis)|維度(dimension):ndim/秩rank |形狀(shape):shape|dtype(資料型態:data type)

2.基本運算
基本運算1: 建立各式各樣的ndarray
基本運算2: ndarray的基本運算:切片 (Slicing) , 搜尋(找出滿足條件的資料),排序,.....
基本運算3: ndarray的基本數學運算:四則運算,
基本運算4: ndarray的基本統計運算 ==> 進階功能請參閱python統計書籍
基本運算5: ndarray的(線性代數)數學運算

3.特殊運算 ==> 陣列擴張|廣播 (Broadcasting)
4.A矩陣與B矩陣間的運算
5.Universal function 與向量化運算(Vectorization computation)
```
## 1_numpy資料型態 ndarray及其屬性
- 資料型態 ==> N-Dimensional Arrays(ndarray)
- ndarray的屬性: 
  - 軸(axis)|維度(dimension):{ndim|秩rank} |形狀(shape):shape|dtype(資料型態:data type)| 大小(元素個數):size

#### ndarray的屬性:軸(axis)
- [numpy axis概念整理筆記](http://changtw-blog.logdown.com/posts/895468-python-numpy-axis-concept-organize-notes)

#### ndarray的屬性:維度(dimension):ndim/秩rank
```
import numpy as np

ar2=np.array([[0,3,5],[2,8,7]]) # 產生一個 2D array
ar2.ndim
```

#### ndarray的屬性:形狀(shape):shape
```
import numpy as np

ar2=np.array([[0,3,5],[2,8,7]]) # 產生一個 2D array
ar2.shape
```
#### ndarray的屬性:大小(元素個數):size
```
import numpy as np

ar2=np.array([[0,3,5],[2,8,7]]) # 產生一個 2D array
ar2.size
```
#### ndarray的屬性:[dtype(資料型態:data type)](https://www.runoob.com/numpy/numpy-dtype.html)
```
import numpy as np

ar1=np.array([2,4,6,8]); 
ar1.dtype
```
```
ar2=np.array([2,-1,6,3], dtype='float’ ); 
ar2
ar2.dtype
```

```
import numpy as np

ar3=np.array([2.,4,6,8]); 

ar3.dtype
```

### 作業:[numpy.array的說明](https://numpy.org/doc/stable/reference/generated/numpy.array.html)
#### ndarray的運算:型態轉換|astype()函式
```
f_ar = np.array([13,-3,8.88])
f_ar
```
```
intf_ar=f_ar.astype(int)
intf_ar
```
# 2.基本運算

## 基本運算1: 建立各式各樣的ndarray
- [完成底下函式的說明]
- 建立元素都是 0/1 的陣列 – zeros()/ones()
- 建立「不限定元素值」的陣列 – empty()
- 建立identity matrix
- 建立diagonal array
- 建立指定範圍的等差陣列 – arange()
- 建立指定範圍的等差陣列 – linspace()
- 使用random 模組建立隨機亂數的陣列 

### 建立元素都是 0/1 的陣列 – zeros()/ones()
```
import numpy as np
z1 = np.zeros((2, 3))
z2 = np.ones((2, 3))
z1
```


### create(建立) identity matrix
```
import numpy as np

ar9a = np.array([[ 1.,  0.,  0.],[ 0.,  1.,  0.],[ 0.,  0.,  1.]]);
ar9a
```
```
import numpy as np

ar9b = np.eye(3);
ar9b
```
### Create(建立) diagonal array
```python
import numpy as np

ar10=np.diag((2,1,4,6));
ar10
```
### 使用numpy.linspace產生陣列
```python
import numpy as np

ar12 = np.linspace(1., 4., 6) # start, end, num
ar12
```
### 作業:建立「不限定元素值」的陣列 – empty()

### 建立指定範圍的等差陣列 – arange()
```python
import numpy as np

ar11=np.arange(2, 3, 0.1) # start, end, step
ar11
```

### 作業
```python
import numpy as np
arr = np.array([range(i, i + 3) for i in [2, 4, 6]])
```
```
arr 
(1)array =?
(2)shape =?
(3)dimension =?
(4)dtype
```
- 更多範例 請參閱 [官方說明Array creation routines]([https://numpy.org/doc/stable/reference/ufuncs.html](https://numpy.org/doc/stable/reference/routines.array-creation.html))
# 基本運算2:
- 陣列變形 - reshape()、resize()
- 將陣列展平為 1D 陣列 – flatten()/ravel()
- 增加陣列的軸數 – np.newaxis
- 轉置陣列transpose()
- 陣列排序 – sort() 與 argsort()
- 陣列合併 – vstack()、hstack()
- 在陣列最後面加入元素 – append()
- 條件搜尋
  - 判斷陣列真假值 – all()、any()
  - 找出符合條件的元素 – where()
  - 取出最大值、最小值 – amax()、amin()
  - 取出最大值、最小值的索引位置 – argmax()、argmin()
  - 找出不是 0 的元素 – nonzero()

- 陣列的儲存與讀取 – save() 與 load()
- 以文字格式儲存、讀取陣列內容 – savetxt() 與 loadtxt()

### Array shape manipulation::reshape()

```python
import numpy as np

# x = np.arange(2,10)
x = np.arange(2,10).reshape(2,4)
y = np.arange(2,10).reshape(4,2)
x
y
#x.shape
```
### Array shape manipulation:Flattening(numpy.ravel()) and Transpose(numpy.T())

```python
import numpy as np
ar=np.array([np.arange(1,6),np.arange(10,15)]); 
ar
ar.ravel()
ar.T
ar.T.ravel()
```

### 作業:下列答案為何? numpy.tile()
- [numpy.tile](https://docs.scipy.org/doc/numpy/reference/generated/numpy.tile.html)

```
np.tile(np.array([[1,2],[6,7]]),3)
np.tile(np.array([[1,2],[6,7]]),(2,2))
```
### 使用索引存取陣列 Array Indexing(索引)1: Accessing  Elements
```python

Import numpy as np
x = np.arange(2,10)
x
```
```
x[0]=?
x[-1]=?
x[-2]=?
```
### 使用索引存取陣列 Array Indexing(索引)2: Accessing  Elements
```python
ar = np.array([[2,3,4],[9,8,7],[11,12,13]]); 
ar
```
```python
ar[1,2] =?
ar[2,:] =?
ar[:,1] =?
ar[2,-1] =?
```

### Array slicing陣列的切片運算
```
ar=2*np.arange(6); 
ar
```
```
ar[1:5:2]=?
ar[1:6:2]=?
ar[:4]=?
ar[4:] =?
ar[::3]=?

ar[:3]=1;
ar
ar[2:]=np.ones(4);
ar
```
### 基本運算3:Reduction Operations與四則運算

```python
import numpy as np

ar=np.arange(1,5)
ar.prod()
```
```python
import numpy as np
ar=np.array([np.arange(1,6),np.arange(1,6)]);
ar

## 底下答案為何?
np.prod(ar,axis=0)
np.prod(ar,axis=1)
```
```python
ar=np.array([[2,3,4],[5,6,7],[8,9,10]]); 
ar.sum()
ar.mean()
np.median(ar)
```


### 更多函數 請參閱底下
- [數學函數Mathematical functions](https://numpy.org/doc/stable/reference/routines.math.html) 
- [Logic functions](https://numpy.org/doc/stable/reference/routines.logic.html)

### [基本運算4: ndarray的基本統計運算](https://numpy.org/doc/stable/reference/routines.statistics.html) ==> 進階統計功能請參閱python統計書籍
- 計算元素平均值 – average() 與 mean()
- 計算中位數 – median()
- 計算元素總和 – sum()
- 計算標準差 – std()
- 計算變異數 – var()
- 計算共變異數 – cov() 
- 計算相關係數 – corrcoef()

- [相關係數 – corrcoef()](https://numpy.org/doc/stable/reference/generated/numpy.corrcoef.html)
  - [參考資料](https://blog.csdn.net/small__roc/article/details/123519616?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-123519616-blog-114920517.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-123519616-blog-114920517.pc_relevant_default&utm_relevant_index=1) 
  - 皮爾森相關係數|皮爾森積矩相關係數(Pearson product-moment correlation coefficient) 
  - 是一種線性相關係數，是最常用的一種相關係數 記為r
  - 用來反映兩個變數X和Y的線性相關程度
  - r 值介於-1到1之間，絕對值越大表明相關性越強。
  - 適用連續變數。
  - 相關係數與相關程度一般劃分為
    - 0.8 - 1.0 極強相關
    - 0.6 - 0.8 強相關
    - 0.4 - 0.6 中等程度相關
    - 0.2 - 0.4 弱相關
    - 0.0 - 0.2 極弱相關或無相關



### 基本運算5: ndarray的(線性代數)數學運算(略)
- 點積運算 – dot()
- 計算矩陣的 determinant – linalg.det()
- 計算矩陣的「特徵值」與「特徵向量」 – linalg.eig()
- 計算矩陣的 rank – linalg.matrix_rank()
- 計算矩陣的「反矩陣」 – linalg.inv() 
- 計算張量積 – outer()
- 計算叉積 – cross() 
- 計算卷積 – convolve()
- 將連續值轉換為離散值 – digitize()


## 3.特殊運算 ==> [陣列擴張|廣播 (Broadcasting)](https://numpy.org/doc/stable/user/basics.broadcasting.html)
- https://jakevdp.github.io/PythonDataScienceHandbook/02.05-computation-on-arrays-broadcasting.html
- General Broadcasting Rules

```python
import numpy as np
a = np.array([1.0, 2.0, 3.0])
b = 2.0
a + b
```

## 4_A矩陣與B矩陣間的運算 
- 任何兩個大小相等的陣列之間的運算，都是element-wise(元素對元素)
```python
arr = np.array([[1., 2., 3.], [4., 5., 6.]])
arr

arr+arr
arr-arr
arr*arr(矩陣相乘)
1/arr
arr ** 0.5
```
- Array multiplication(矩陣相乘) vs dot(內積)
```
ar=np.array([[1,1],[1,1]]);
ar2=np.array([[2,2],[2,2]]);

# 標準Array multiplication(矩陣相乘) 
ar*ar2

# 內積運算
ar.dot(ar2)
```
## 5.Universal function(NumPy ufuncs)與向量化運算(Vectorization computation) 
- [NumPy ufuncs](https://www.w3schools.com/python/numpy/numpy_ufunc.asp)
  - ufuncs stands for "Universal Functions" and they are NumPy functions that operates on the ndarray object.
  - ufuncs are used to implement vectorization in NumPy which is way faster than iterating over elements.
  - They also provide broadcasting and additional methods like reduce, accumulate etc. that are very helpful for computation.

- 問題:計算0的三次方到999的三次方
  - 解法大PK:
    - Python ==> For loop
```
ar=range(1000)
%timeit [ar[i]**3 for i in ar]
```

  - Numpy ==>Vectorization 向量化計算
```python
ar=np.arange(1000)
%timeit ar**3
```
- 更多範例
```python
import numpy as np
arr = np.arange(10)
arr

np.sqrt(arr)
np.exp(arr)
```
- 作業[Rounding Decimals](https://www.w3schools.com/python/numpy/numpy_ufunc_rounding_decimals.asp)
  - There are primarily five ways of rounding off decimals in NumPy:
    - truncation
    - fix
    - rounding
    - floor
    - ceil
```python

import numpy as np

arr1 = np.trunc([-3.1666, 3.6667])
arr2 = np.fix([-3.1666, 3.6667])
arr3 = np.around(3.1666, 2)
arr4 = np.floor([-3.1666, 3.6667])
arr5 = np.ceil([-3.1666, 3.6667])

print(arr1)
```
- 更多範例 請參閱 [官方說明Universal functions (ufunc)](https://numpy.org/doc/stable/reference/ufuncs.html)
