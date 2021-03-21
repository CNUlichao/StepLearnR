---
title: "学习R之流程控制和循环"
author: "数分进阶"
date: "3/13/2021"
output: html_document
---



### 1. 流程控制
在执行代码时，并不仅仅是逐行执行，而需要更好地控制执行流程，这就涉及到流程控制语句。

#### 1.1 if 和 else
最简单的流程控制语句是 if 语句，if 接受一个逻辑值，该值为 TURE 时才会执行下一条语句。

当然，大部分时候都不会直接传入 TRUE 或 FALSE 值，而是传递一个变量或表达式，满足 if 语句的条件才会继续执行。


```r
# 这条语句会执行
if(TRUE) 
{
    message("It was true!")
}
>> It was true!

# 这条语句不会执行
if(FALSE) 
{
    message("It was false!")
}

# x > 2 时执行 if 语句
x <- 3
if(x > 2)
{
    y <- 2 * x
    z <- 3 * y 
    message("y = ", y, ", z = ", z)
}
>> y = 6, z = 18
```

<br />
与 if 对应的是 else 语句，如果 if 的条件值为 FALSE，则会执行 else 之后的代码：


```r
x <- runif(1)
if(x > 0.5)
{
    message("x = ", x, " larger then 0.5")
} else 
{
    message("x = ", x, " less then 0.5")
}
>> x = 0.432472578017041 less then 0.5
```

<br />
可以反复使用 if 和 else 来定义多个条件：


```r
r <- runif(2)
x < r[1] / r[2]
>> [1] TRUE
if(is.nan(x))
{
    message("x is missing")
} else if(is.infinite(x))
{
    message("x is infinite")
} else if(x > 0)
{
    message("x is positive")
} else if(x < 0)
{
    message("x is negative")
} else
{
    message("x is zero")
}
>> x is positive
```

#### 1.2 矢量化的 if
标准的 if 语句需要一个逻辑值作为参数，如果给它传递一个长度超过 1 的逻辑向量，R 会警告你已给出多个选项，仅第一个将被使用。

这时可以使用 ifelse 函数，它有三个参数：第一个是逻辑条件向量，第二个参数值在第一个向量为 TRUE 时被返回，第三个参数值在第一个向量为 FALSE 时被返回。


```r
# 不要这么使用 if
if(c(TRUE, FALSE)) 
{
    message("two choices")
}
>> Warning in if (c(TRUE, FALSE)) {: 条件的长度大于一，因此只能用其第一元素
>> two choices

# ifelse 语句
ifelse(rbinom(10, 1, 0.5), "Head", "Tail")
>>  [1] "Tail" "Head" "Tail" "Tail" "Tail" "Tail" "Head" "Tail" "Tail" "Tail"
```

#### 1.3 switch 语句
如果包含太多的 else 语句就会迅速降低代码的可读性，在这种情况下，可以用 switch 函数来实现多分支的控制语句。

switch 函数的常见用法是：第一个参数为一个返回字符串的表达式，其后的参数为与第一个参数相匹配时的返回值，如果找不到任何匹配的名字，那么将返回 NULL。


```r
(greek <- switch(
    "gamma",
    alpha = 1,
    beta = sqrt(4),
    gamma =
    {
        a <- sin(pi / 3)
        4 * a ^ 2
    }
))
>> [1] 3

# 没有匹配值时，返回没有命名的值 4
(greek <- switch(
    "delta",
    alpha = 1,
    beta = sqrt(4),
    gamma =
    {
        a <- sin(pi / 3)
        4 * a ^ 2 
    },
    4 
))
>> [1] 4
```

### 2. 循环
R 中有三种循环：repeat、while 和 for，虽然向量化意味着并不像其他语言一样大量需要它们，但在需要重复执行代码时，循环还是很有用的。

#### 2.1 repeat 循环
repeat 循环就是反复地执行代码，直到告诉它停为止，通常使用 break 语句跳出整个循环，使用 next 跳出当前循环。


```r
# 随机抽到字母 C 则退出整个循环
i = 0
repeat {
    letter <- sample(c("A", "B", "C", "D"), 1)
    message("the letter is ", letter)
    if(letter == "C")  
    {
        break
    } else 
    {
        i<- i + 1
    }
}
>> the letter is A
>> the letter is B
>> the letter is C

message(i)
>> 2

# 随机抽到字母 B 则跳出当前循环 j 不累加，抽到 C 则退出整个循环
j <- 0
repeat {
    letter <- sample(c("A", "B", "C", "D"), 1)
    message("the letter is ", letter)
    if(letter == "B")  
    {
        message("B is the lucky letter, j skip plus 1")
        next
    } else if(letter == "C")  
    {
        break
    } else 
    {
        j <- j + 1
    }
}
>> the letter is A
>> the letter is A
>> the letter is C

message(j)
>> 2
```


#### 2.2 while 循环
while 循环就像是延迟了的 repeat 循环，先进行检查，若满足条件则执行代码，否则不执行代码。


```r
letter <- sample(c("A", "B", "C", "D"), 1)
message("the letter is ", letter)
>> the letter is D
# 随机抽到的字母不是 C 则执行 while 循环
while(letter != "C") 
{
    letter <- sample(c("A", "B", "C", "D"), 1)
    message("the letter is ", letter)
}
>> the letter is A
>> the letter is B
>> the letter is A
>> the letter is A
>> the letter is A
>> the letter is A
>> the letter is D
>> the letter is B
>> the letter is D
>> the letter is A
>> the letter is C
```

#### 2.3 for 循环
for 循环适用于已知代码所需执行的循环次数的情形，它将接受一个迭代器变量和一个向量参数，在每个循环中，迭代器变量会从向量中取得一个值。

R 的 for 循环非常灵活，输入并不限于整数或数字，还可以传入字符向量、逻辑向量或列表：


```r
# 整数
for(i in 1:5)
{
    message("i = ", i)
    j <- i ^ 2
    message("j = ", j)
}
>> i = 1
>> j = 1
>> i = 2
>> j = 4
>> i = 3
>> j = 9
>> i = 4
>> j = 16
>> i = 5
>> j = 25

# 字符向量
for(month in month.name)
{
    message("The month of ", month)
}
>> The month of January
>> The month of February
>> The month of March
>> The month of April
>> The month of May
>> The month of June
>> The month of July
>> The month of August
>> The month of September
>> The month of October
>> The month of November
>> The month of December

# 逻辑向量
for(yn in c(TRUE, FALSE, NA))
{
    message("This statement is ", yn)
}
>> This statement is TRUE
>> This statement is FALSE
>> This statement is NA
```

<br />
需要注意的是，R 的 for 循环几乎总是比其对应的向量化运行得要慢，而且往往是一到两个数量级的差别，所以尽可能地使用向量化而不是循环。



