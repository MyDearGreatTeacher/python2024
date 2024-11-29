##
```python

def isLeapYear(year):
    if (year % 4 == 0 and year % 100 != 0) or (year % 400 == 0):
        return True
    else:
        return False

result = 0
index = 0
for i in range(2000, 2051):
    if isLeapYear(i):
        print(i, end = ' ')
        result += i
        index += 1

print(result)
print(index)
```
