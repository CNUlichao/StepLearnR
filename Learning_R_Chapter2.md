---
title: "学习R之基础运算"
author: "数分进阶"
date: "3/1/2021"
output: html_document
---



### 1. 运算符和向量
R 中的算术运算符包括：加 (+)、减(-)、乘(*)、除(/)、整除(%/%)、取余(%%)、乘方(^)，除了可以直接作用于两个数字，还可以对向量进行运算。

向量化操作的优点是：**运算符或者函数作用于向量中的每个元素，而无需显式编写循环语句**。

c 函数可以把一系列的值拼接起来创建为向量。


```r
c(1, 3, 5, 7, 9) + 1
>> [1]  2  4  6  8 10

c(2, 4, 6, 8, 10) - 2
>> [1] 0 2 4 6 8

1:10 %/% 3
>>  [1] 0 0 1 1 1 2 2 2 3 3

1:10 %% 3
>>  [1] 1 2 0 1 2 0 1 2 0 1
```

<br />
冒号运算符：创建一个从某个数值开始到另一个数值结束的向量，向量既可以按 1 递增，也可以按 1 递减，取决于首尾哪个值更大。


```r
1:5
>> [1] 1 2 3 4 5

-8:-2
>> [1] -8 -7 -6 -5 -4 -3 -2

16:8
>> [1] 16 15 14 13 12 11 10  9  8
```

<br />
R 支持多种数学函数，如三角函数 (sin、cos、tan，以及相反的 asin、acos 和 atan)、对数和指数 (log 和 exp) 等，所有的函数都作用于向量，而不仅仅是单个值。


```r
cos(c(0, pi / 4, pi / 2, pi))    # pi是内置常数
>> [1]  1.000000e+00  7.071068e-01  6.123234e-17 -1.000000e+00

log(c(10, 100, 1000, 10000), base = 10)
>> [1] 1 2 3 4

exp(1:5)
>> [1]   2.718282   7.389056  20.085537  54.598150 148.413159
```

<br />
R 中的关系运算符包括：大于 (>)、等于(==)、小于(<)、不等于(!=)、大于等于(>=)、小于等于(<=)。


```r
c(3, 4 - 1, 1 + 1 + 1) == 3
>> [1] TRUE TRUE TRUE

(1:5) ^ 2 >= 16
>> [1] FALSE FALSE FALSE  TRUE  TRUE
```

<br />
使用 == 来比较非整型变量可能会有问题，因为浮点数都是都以 32 位存储的，是一个近似值，所以根号 2 的平方并不等于2。


```r
sqrt(2) ^ 2 == 2
>> [1] FALSE

sqrt(2) ^ 2 - 2
>> [1] 4.440892e-16
```

<br />
R 提供了 all.equal 函数用于检查数字是否相等，它提供了一个容忍度 (默认情况下为 1.5e-8)，小于此容忍度的误差将被忽略:


```r
all.equal(sqrt(2) ^ 2, 2)
>> [1] TRUE
```

<br />

### 2. 变量赋值
在 R 中，给变量赋值之前，并不需要事先声明变量，直接使用 <- 或 = 来给变量进行赋值，通常情况下，<- 是首选，赋值之后的变量即可参与运算。

变量名可以包含字母、数字、点和下划线，但不能以数字或下划线开头，系统的保留字也不能使用，如 if、for、else、while 等。


```r
x <- 1:5
y = 6:10

x + 2 * y - 3
>> [1] 10 13 16 19 22
```

<br />

### 3. 特殊数字
R 中有四种特殊值：Inf、-Inf、NaN 和 NA。前两个分别是正无穷和负无穷，NaN 是 not a number 的缩写，从字面意思看表示非数值，NA 是 not avaliable 的缩写，代表缺失值。


```r
c(Inf + 1, Inf - 1, Inf - Inf)
>> [1] Inf Inf NaN

c(1 / Inf, Inf / 1, Inf / Inf)
>> [1]   0 Inf NaN

c(sqrt(Inf), sin(Inf))
>> Warning in sin(Inf): 产生了NaNs
>> [1] Inf NaN

c(NA + 1, NA * 5, NA + Inf)
>> [1] NA NA NA
```

<br />
可使用函数来判断这些特殊值，需要注意的是：NaN 和 NA 既非有限值也非无限值。


```r
x <- c(0, Inf, -Inf, NaN, NA)

is.finite(x)
>> [1]  TRUE FALSE FALSE FALSE FALSE

is.infinite(x)
>> [1] FALSE  TRUE  TRUE FALSE FALSE

is.nan(x)
>> [1] FALSE FALSE FALSE  TRUE FALSE

is.na(x)
>> [1] FALSE FALSE FALSE  TRUE  TRUE
```

<br />

### 4. 逻辑向量
TRUE 和 FALSE 是 R 中的布尔类型值，TRUE 为真，FALSE 为假，有三个运算符可以作用于布尔类型：

-  ! 代表非操作
-  & 代表与操作
-  \| 代表或操作


```r
x <- 1:10
x >= 5
>>  [1] FALSE FALSE FALSE FALSE  TRUE  TRUE  TRUE  TRUE  TRUE  TRUE

!x
>>  [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE

y <- 1:10 
y %% 2 == 0
>>  [1] FALSE  TRUE FALSE  TRUE FALSE  TRUE FALSE  TRUE FALSE  TRUE

x & y 
>>  [1] TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE

x | y
>>  [1] TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE
```

<br />
其他两个比较有用的处理逻辑向量的函数是 any 和 all，向量中至少包含一个 TRUE 值时，any 函数就会返回 TRUE，而向量中的所有值均为 TRUE 时，all 函数才会返回 TRUE：


```r
 none_true <- c(FALSE, FALSE, FALSE)
 some_true <- c(FALSE, TRUE, FALSE)
 all_true <- c(TRUE, TRUE, TRUE)
 
 any(none_true)
>> [1] FALSE
 
 any(some_true)
>> [1] TRUE
 
 any(all_true)
>> [1] TRUE
 
 all(none_true)
>> [1] FALSE
 
 all(some_true)
>> [1] FALSE
 
 all(all_true)
>> [1] TRUE
```
