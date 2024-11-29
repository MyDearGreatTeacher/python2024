# multiple choice
- 1
```python
Which following is the correct way to create a dictionary in Python?
   (A) students = { 'name' => 'alice' ;  'score' =>60 }
   (B) students = { 'name' = 'alice' ;  'score' =60 }
   (C) students = { 'name' : 'alice' ;  'score' : 60 }
   (D) students = { 'name' => 'alice' ,  'score' =>60 }
   (E) students = { 'name' : 'alice' , 'score' : 60 }
```
- 2
```python
What does the following Python program fragment do?
   s=0
   for i in range (3 , 10) :
        s+=i
   print (s)
   (A) Calculate the sum of 1+2+…+10
   (B) Calculate the sum of 1+4+7+10
   (C) Calculate the sum of 3+4+5+6+…+9
   (D) Calculate the sum of 3+4+5+6+…+10
   (E) Calculate the sum of 0+2+4+6+…+10
```
- 3
```python
What is the output of this code in Python?
      list = [ ' a ' ,  ' b ' ,  ' c ' ,  ' d ' ,  ' e ' ]
      print (list [ 1 : ])
   (A) [ ]
   (B) [ 'b' ]
   (C) [ 'a' ,  'b' ]
   (D) [ 'b' ,  'c' ,  'd' ,  'e' ]
```

- 4
```python
What is the output of this code in Python?
        L1 = [ ]
        L1.append ( [1, [2, 3], 4] )
        L1.extend ( [7, 8, 9] )
        print(L1[0][1][1] + L1[2] )
   (A) 3  (B) 8  (C) 11  (D) 38  (E) None of these
```

- 5

```python
What is the output of this code in python?
        r = lambda q: q * 2
        s = lambda q: q * 3
        x = 2
        x = r (x)
        x = s (x)
        x = r (x)
        print (x)
   (A) 2  (B) 4  (C) 6  (D) 12  (E) 24
```

- 6
```python
Which of the following conditions correctly describes the output of this code in  Python?
         first = [1 , 2 , 3 , 4 , 5]
         second = first
         second.append (6)
         print (first)
         print (second)
   (A) first=[1, 2, 3, 4, 5], second=[1, 2, 3, 4, 5, 6]
   (B) first=[1, 2, 3, 4, 5, 6], second=[1, 2, 3, 4, 5]
   (C) first=[1, 2, 3, 4, 5], second=[1, 2, 3, 4, 5]
   (D) first=[1, 2, 3, 4, 5, 6], second=[1, 2, 3, 4, 5, 6]
   (E) None of these
```
- 7
```python
If the function 
int volume (int x = 1, int y = 1, int z = 1); 
is called by the expression volume (7,8), 
how many default arguments are used?
(A) None  (B) One  (C) Two  (D) Three
(E) It depends on the runtime environment.
```

# reading comprehension:What is the output of this code in Python?
- B1.What is the output of this code in Python? explain it.
```python
# primes.py
primes = []  # this will contain the primes at the end
upto = 100  # the limit, inclusive
for n in range(2, upto + 1):
    is_prime = True  # flag, new at each iteration of outer for
    for divisor in range(2, n):
        if n % divisor == 0:
            is_prime = False
            break
    if is_prime:  # check on flag
        primes.append(n)
print(primes)
```
- B2.What is the output of this code in Python? explain it.
```python
# primes.else.py
primes = []
upto = 100
for n in range(2, upto + 1):
    for divisor in range(2, n):
        if n % divisor == 0:
            break
    else:
        primes.append(n)
print(primes)
```
- B3.What is the output of this code in Python?explain it.
```
cubes = [x**3 for x in range(1,6)]
print(cubes)
```

## Programming Python
```
The Fibonacci numbers may be defined by the recurrence relation
 F_{0}=0, F_{1}=1
and
F_{n} = F_{n-1} + F_{n-2}   for n > 1.

0,1,1,2,3,5,8,......

Under some older definitions, the value  F_{0}=0 is omitted, 
so that the sequence starts with F_{1}=F_{2}=1 and 
the recurrence F_{n}=F_{n-1}+F_{n-2} is valid for n > 2

(1)Write a Python function/Program to Find Fibonacci Numbers using Recursion
(2)use iterative Approach To Find the nth Term Of Fibonacci Sequence(You need to write a function using loop)
```
- [Fibonacci number](https://en.wikipedia.org/wiki/Fibonacci_number)
  - [Python Program to Display Fibonacci Sequence Using Recursion](https://www.programiz.com/python-programming/examples/fibonacci-recursion)
  - [A Python Guide to the Fibonacci Sequence](https://realpython.com/fibonacci-sequence-python/)
  - [Fibonacci Sequence: Iterative Solution in Python](https://pythonistaplanet.com/fibonacci-sequence-iterative/)

```


```
```
