---
title: "学习R之列表和数据框"
author: "数分进阶"
date: "3/2/2021"
output: html_document
---



### 1. 列表
#### 1.1 创建列表
列表由 list 函数创建，且能像 c 函数那样指定内容，只需简单地用逗号分隔每个参数即可指定列表中的内容。列表中元素变量的类型不限, 可以是向量、矩阵，甚至函数。

与向量的命名类似，可以在构造列表时就给元素命名，或在构造之后使用 names 函数命名:


```r
(a_list <- list(
    c(1, 1, 2, 5, 14, 42),
    month.abb,
    matrix(c(3, -8, 1, -3), nrow = 2),
    asin
))
>> [[1]]
>> [1]  1  1  2  5 14 42
>> 
>> [[2]]
>>  [1] "Jan" "Feb" "Mar" "Apr" "May" "Jun" "Jul" "Aug" "Sep" "Oct" "Nov" "Dec"
>> 
>> [[3]]
>>      [,1] [,2]
>> [1,]    3    1
>> [2,]   -8   -3
>> 
>> [[4]]
>> function (x)  .Primitive("asin")

names(a_list) <- c("catalan", "months", "involutary", "arcsin")
a_list
>> $catalan
>> [1]  1  1  2  5 14 42
>> 
>> $months
>>  [1] "Jan" "Feb" "Mar" "Apr" "May" "Jun" "Jul" "Aug" "Sep" "Oct" "Nov" "Dec"
>> 
>> $involutary
>>      [,1] [,2]
>> [1,]    3    1
>> [2,]   -8   -3
>> 
>> $arcsin
>> function (x)  .Primitive("asin")

the_same_list <- list(
    catalan = c(1, 1, 2, 5, 14, 42),
    months = month.abb,
    involutary = matrix(c(3, -8, 1, -3), nrow = 2),
    arcsin = asin
)
```

<br />
列表与向量一样也有长度，其长度是它顶层元素的数目。


```r
length(a_list)
>> [1] 4
```

#### 1.2 列表的索引
与向量类似，可通过方括号 [ ]、正或负的下标数字、元素名称或逻辑索引这四种方法访问列表中的元素，以下四行代码的结果相同:


```r
l <- list(
    first  = 1,
    second = 2,
    third  = list(
        alpha = 3.1,
        beta = 3.2 
    )
)

l[1:2]
>> $first
>> [1] 1
>> 
>> $second
>> [1] 2

l[-3]
>> $first
>> [1] 1
>> 
>> $second
>> [1] 2

l[c("first", "second")]
>> $first
>> [1] 1
>> 
>> $second
>> [1] 2

l[c(TRUE, TRUE, FALSE)]
>> $first
>> [1] 1
>> 
>> $second
>> [1] 2
```

<br />
以上索引操作的结果仍然是一个列表，但有时需要访问列表元素中的内容，可以使用双方括号 [[  ]]，
或指定该元素的名称字符串来获取元素中的内容，对于列表中的命名元素，使用美元符号运算符 $ 与之等价。

is.list 函数可以判断变量是否为列表，如果是列表将返回 TRUE，否则返回 FALSE。


```r
l[[1]]
>> [1] 1

l[["first"]]
>> [1] 1

l$first
>> [1] 1

is.list(l[1])
>> [1] TRUE

is.list(l[[1]])
>> [1] FALSE
```

<br />
也可以通过嵌套方括号来访问嵌套的元素。


```r
l[["third"]]["beta"]
>> $beta
>> [1] 3.2

l[["third"]][["beta"]]
>> [1] 3.2
```

#### 1.3 列表与向量的转换
向量可使用函数 as.list 函数来转换成列表，所创建的可见列表与向量中元素的值一一对应：


```r
busy_beaver <- c(1, 6, 21, 107)
as.list(busy_beaver)
>> [[1]]
>> [1] 1
>> 
>> [[2]]
>> [1] 6
>> 
>> [[3]]
>> [1] 21
>> 
>> [[4]]
>> [1] 107
```

#### 1.4 NULL值
NULL 是个特殊值，它表示一个空的变量，最常用于列表中，不过也会出现在数据框和函数参数中。 

在创建列表时，你可能会想指定一个元素，表明它必须存在但没有赋值，这时可以用 NULL 来表示:


```r
(person <- list(
       name = "Jack",
       age = 28,
       sex = "male",
       profession = NULL
    )
)
>> $name
>> [1] "Jack"
>> 
>> $age
>> [1] 28
>> 
>> $sex
>> [1] "male"
>> 
>> $profession
>> NULL
```

<br />
理解 NULL 和特殊的缺失值 NA 之间的区别非常重要。最大的区别是，NA 是一个标量值，而 NULL 不会占用任何空间，它的长度为零。

可以使用函数 is.null 来判断变量是否为 NULL 值，缺失值不是 NULL。


```r
length(NULL)
>> [1] 0

length(NA)
>> [1] 1

is.null(NULL)
>> [1] TRUE

is.null(NA)
>> [1] FALSE
```

### 2. 数据框
#### 2.1 创建数据框
数据框用于存储类似电子表格的数据，每列的数据类型可与其他列不同，但在同一列中的元素类型必须相同。

可以通过给 row.names 传入一个向量来为每行命名，如果不指定的话，行号默认是从 1 开始递增的数字。


```r
# 不指定行名称
(a_data_frame <- data.frame(
        x = letters[1:5],
        y = rnorm(5),
        z = runif(5) > 0.5
))

# 指定行名称
data.frame(
       x = letters[1:5],
       y = rnorm(5),
       z = runif(5) > 0.5,
       row.names = c("Jackie", "Tito", "Jermaine", "Marlon", "Michael")
)
```

<br />
像矩阵一样，维度的名称可以使用 dimnames 来获取，同样地，也可以使用 rownames 和 colnames 分别获取或置行和列的名称。

事实上，几乎所有用于矩阵的函数亦可用在数据框上，例如，nrow、ncol 和 dim 函数的使用也与矩阵一样。


```r
dimnames(a_data_frame)
>> [[1]]
>> [1] "1" "2" "3" "4" "5"
>> 
>> [[2]]
>> [1] "x" "y" "z"

rownames(a_data_frame)
>> [1] "1" "2" "3" "4" "5"

colnames(a_data_frame)
>> [1] "x" "y" "z"

dim(a_data_frame)
>> [1] 5 3

nrow(a_data_frame)
>> [1] 5

ncol(a_data_frame)
>> [1] 3
```

#### 2.2 数据框的索引
数据框的索引与矩阵的索引方式类似，可使用四种不同的向量索引 (正整数、负整数、逻辑值和字符)，如选择数据框中前两列的第二个和第三个元素：


```r
a_data_frame[2:3, -3]

a_data_frame[c(FALSE, TRUE, TRUE, FALSE, FALSE), c("x", "y")]
```

<br />
如果只需选择一列，也可以使用列表样式的索引 (带有正整数或名称的双方括号，或者带有名称的美元符号运算符)，如选择第一列中的第二个和第三个元素：


```r
a_data_frame$x[2:3]
>> [1] b c
>> Levels: a b c d e

a_data_frame[[1]][2:3]
>> [1] b c
>> Levels: a b c d e

a_data_frame[['x']][2:3]
>> [1] b c
>> Levels: a b c d e
```

<br />
如果需要给列加上条件来得到一个数据框子集，使用的语法会有点冗长，其中 & 表示多个条件同时满足，| 表示满足其中的一个条件即可。

subset 函数能做同样的事情且更简洁，需传入三个参数：一个数据框，一个行的条件逻辑向量，以及一个需要保留的名字向量，如果最后一个参数省略，则将保留所有列。


```r
# 单个条件
a_data_frame[ a_data_frame$y > 0, c("x", "y")]

# 同时满足两个条件 &
a_data_frame[ a_data_frame$y < 0 & a_data_frame$x %in% c("b", "c"), c("x", "y")]

# 至少满足一个条件 | 
a_data_frame[ a_data_frame$y > 0 | a_data_frame$z == TRUE, c("x", "y", "z")]

# 使用 subset 函数实现
subset(a_data_frame, y > 0, c("x", "y"))

subset(a_data_frame, y < 0 & x %in% c("b", "c"), c("x", "y"))

subset(a_data_frame, y > 0 | z == TRUE, c("x", "y", "z"))
```

#### 2.3 数据框的基本操作
如果两个数据框的大小一致，也可使用 cbind 和 rbind 把它们合并起来，但并不会对列名作重复性检查，所以使用时要注意。


```r
another_data_frame <- data.frame(
    z = rlnorm(5),
    y = sample(5),
    x = letters[3:7]
)

# 纵向合并
rbind(a_data_frame, another_data_frame)

# 横向合并
cbind(a_data_frame, another_data_frame)
```

<br />
当两个数据框有相同的列时，可使用 merge 函数合并，连接时需要指定包含键值的列以作匹配。

merge 函数支持以下四种类型的数据合并:

- inner join: 内连接，仅返回两数据框中匹配的行，参数为：**all = FALSE**
- full outer join: 全连接，返回两数据框中的所有行，参数为：**all = TRUE**
- left outer join: 左连接，返回 x 数据框中所有行以及和 y 数据框中匹配的行，参数为：**all.x = TRUE**
- right outer join: 右连接，返回 y 数据框中所有行以及和 x 数据框匹配的行，参数为：**all.y = TRUE**


```r
# 内连接
merge(a_data_frame, another_data_frame, by = "x")

# 全连接
merge(a_data_frame, another_data_frame, by = "x", all = TRUE)

# 左连接
merge(a_data_frame, another_data_frame, by = "x", all.x = TRUE)

# 右连接
merge(a_data_frame, another_data_frame, by = "x", all.y = TRUE)
```

<br />
操作数据框是一个很大的话题，我们将在之后的章节中继续深入探讨。







