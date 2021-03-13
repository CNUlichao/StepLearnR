---
title: "学习R之函数和作用域"
author: "数分进阶"
date: "3/13/2021"
output: html_document
---



### 1. 创建和调用函数
键入一个函数的名称，将显示其运行的代码，以下是 rt 函数，该函数将生成基于 T 分布的随机数：


```r
rt
>> function (n, df, ncp) 
>> {
>>     if (missing(ncp)) 
>>         .Call(C_rt, n, df)
>>     else rnorm(n, ncp)/sqrt(rchisq(n, df)/df)
>> }
>> <bytecode: 0x7f990c751598>
>> <environment: namespace:stats>
```

<br />
rt 函数需要传入三个参数：n 是要产生的随机数的数目，df 是自由度值，ncp 是一个可选的非中心参数。

在 R 中，函数中计算的最后一个值将自动返回，并不需要显式地使用 return 关键字声明应该从函数返回哪个值。

创建自定义函数时，只需像其他任何变量一样为函数赋值，如创建一个函数来计算直角三角形斜边的长度：


```r
# hypotenuse 是创建的函数，x 和 y 是它的参数，在大括号中的内容是函数体
hypotenuse <- function(x, y)
{
    sqrt(x ^ 2 + y ^ 2)
}

hypotenuse(3, 4)
>> [1] 5

hypotenuse(y = 24, x = 7)
>> [1] 25
```

<br />
函数形参的默认值不仅仅是常数值，还可以把任何 R 代码放进去，甚至使用其他形参。


```r
# 参数 m 和 s 是第一个参数的平均值和标准差
normalize <- function(x, m = mean(x), s = sd(x))
{
    (x - m) / s 
}

(normalized <- normalize(c(1, 3, 6, 10, 15)))
>> [1] -1.0690450 -0.7126966 -0.1781742  0.5345225  1.4253933

mean(normalized)
>> [1] -5.572799e-18

sd(normalized)
>> [1] 1

# mean 和 sd 都是 NA
normalize(c(1, 3, 6, 10, NA))
>> [1] NA NA NA NA NA

# mean 和 sd 都有一个参数 na.rm，能删除计算之前的任何缺失值
normalize <- function(x, m = mean(x, na.rm = na.rm), s = sd(x, na.rm = na.rm), na.rm = FALSE)
{
    (x - m) / s
}

(normalize(c(1, 3, 6, 10, NA), na.rm = TRUE))
>> [1] -1.0215078 -0.5107539  0.2553770  1.2768848         NA

# R 中的特殊参数 ...，它包含了所有不能被位置或名称匹配的参数
normalize <- function(x, m = mean(x, ...), s = sd(x, ...), ...)
{
    (x - m) / s 
}

(normalize(c(1, 3, 6, 10, NA), na.rm = TRUE))
>> [1] -1.0215078 -0.5107539  0.2553770  1.2768848         NA
```

### 2. 传递和接收函数
函数可以像其他变量类型一样地使用，可将之作为其他函数的参数，并且从函数中返回。

一个常见的，把其他函数当成参数的函数是 do.call。此函数提供了一种调用其他函数的替代语法，让我们可以像列表一样传递参数，而不是逐次传递。


```r
# 和 hypotenuse(3, 4) 等价
do.call(hypotenuse, list(x = 3, y = 4))
>> [1] 5

# 和 rbind(dfr1, dfr2, dfr3) 等价
dfr1 <- data.frame(x = 1:5, y = rt(5, 1))
dfr2 <- data.frame(x = 6:10, y = rf(5, 1, 1))
dfr3 <- data.frame(x = 11:15, y = rbeta(5, 1, 1))
do.call(rbind, list(dfr1, dfr2, dfr3))
>>     x           y
>> 1   1  1.17915584
>> 2   2 -2.25116142
>> 3   3 -0.57103585
>> 4   4  0.38327699
>> 5   5  1.08917610
>> 6   6  0.08678987
>> 7   7  0.03691078
>> 8   8  3.17112747
>> 9   9  1.71188999
>> 10 10  0.02813865
>> 11 11  0.94765173
>> 12 12  0.53830002
>> 13 13  0.21092560
>> 14 14  0.84087300
>> 15 15  0.43478595
```

### 3. 变量的作用域
变量的作用域是指变量可见的范围，如在函数内部定义一个变量时，该函数中下面的语句将能访问到该变量。


```r
# g 是 f 的子函数，即使没有在 g 里定义 y，也能访问 f 中定义的 y
f <- function(x)
{
    y <- 1
    g <- function(x)
    {
        (x + y) / 2
    }
    g(x) 
}
f(sqrt(5))
>> [1] 1.618034
```

<br />
R 会在当前的环境下寻找变量，如果找不到，则会继续在父环境中搜索，然后再在该环境的父环境中搜索，以此类推，直到达到全局环境，在全局环境 中定义的变量在任何地方都可见。



