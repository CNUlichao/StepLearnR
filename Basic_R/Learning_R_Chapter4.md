---
title: "学习R之向量、数组和矩阵"
author: "数分进阶"
date: "3/1/2021"
output: html_document
---



### 1. 向量
之前已经介绍过，可以使用冒号运算符来创建从某个数到另一个数的序列，以及 c 函数拼接数值和向量，从而创建更长的向量。


```r
8.5:4.5
>> [1] 8.5 7.5 6.5 5.5 4.5

c(1, 1:3, c(5, 8), 13)
>> [1]  1  1  2  3  5  8 13
```

<br />
vector 函数能创建一个指定类型和长度的矢量，其结果中的值可为零、FALSE、空字符串。为简便起见，用每个类型的包装函数，如 numeric、logical、character 函数来直接创建向量与之是等价的。


```r
vector("numeric", 5)
>> [1] 0 0 0 0 0

numeric(5)
>> [1] 0 0 0 0 0

vector("logical", 5)
>> [1] FALSE FALSE FALSE FALSE FALSE

logical(5)
>> [1] FALSE FALSE FALSE FALSE FALSE

vector("character", 5)
>> [1] "" "" "" "" ""

character(5)
>> [1] "" "" "" "" ""
```

#### 1.1 序列
除了冒号运算符之外，还有几个其他函数能创建更为通用的序列，其中最常见的是 seq 函数。

<br />
seq.int 可创建一个序列，序列的范围由两个数字指定，原理与冒号运算符完全相同，同时还可以指定序列的步长。


```r
seq.int(3, 12)
>>  [1]  3  4  5  6  7  8  9 10 11 12

seq.int(3, 12, 2)
>> [1]  3  5  7  9 11

seq.int(0.1, 0.01, -0.01)
>>  [1] 0.10 0.09 0.08 0.07 0.06 0.05 0.04 0.03 0.02 0.01
```

#### 1.2 长度
所有的向量都有一个长度，即向量中包含多少个元素，缺失值也会被计入长度。


```r
length(1:5)
>> [1] 5

length(c(TRUE, FALSE, NA))
>> [1] 3
```

<br />
需要注意的是字符向量，它们的长度为字符串的个数，如果需要统计每个字符串中字符数的长度，可以使用 nchar 函数：


```r
s <- c("this", "is", "a", "char", "test")
length(s)
>> [1] 5

nchar(s)
>> [1] 4 2 1 4 4
```

#### 1.3 命名
R 语言中向量的一大特性是能给每个元素命名，可以使用 name = value 的形式在创建向量时为其指定名称。命名时可以只针对某些元素而忽略其他元素，向量创建后以后使用 names 函数为元素添加名字也是可行的。


```r
c(apple = 1, banana = 2, "rare fruit" = 3, 4)
>>      apple     banana rare fruit            
>>          1          2          3          4

x <- 1:4
names(x) <- c("apple", "bananas", "rare fruit", "")
```

#### 1.4 索引
通常，我们只要访问向量中的部分或个别元素，这就是所谓的索引，用方括号 [] 来实现，R 语言提供了如下多种索引方法：

- 给向量传入正数，它会返回此位置上的向量元素切片，**第一个位置是 1** (而不像其他某些语言一样是 0)
- 给向量传入负数，它会返回一个向量切片，它将包含 **除了这些位置以外** 的所有元素
- 给向量传入一个逻辑向量，它会返回一个向量切片，里面只包含 **索引为 TRUE** 的元素
- 对于已命名的向量，给向量传入命名的字符向量，将会返回向量中 **包含这些名字** 的元素切片


```r
x <- (1:5) ^ 2

x[c(1, 3, 5)]
>> [1]  1  9 25

x[c(-2, -4)]
>> [1]  1  9 25

x[c(TRUE, FALSE, TRUE, FALSE, TRUE)]
>> [1]  1  9 25

names(x) <- c("one", "four", "nine", "sixteen", "twenty five")
x[c("one", "nine", "twenty five")]
>>         one        nine twenty five 
>>           1           9          25
```

<br />
混合使用正负值是不允许的，会抛出一个错误，超出范围的下标值不会导致错误，而是返回缺失值 NA，但实际使用索引时，最好确保下标值都在使用范围内。

<br / >
which 函数将返回逻辑向量中为 TRUE 的位置，如果要将逻辑索引切换到整数索引中，这个函数很有用，which.min 和 which.max 函数 则可以找出向量中值最小、值最大的元素索引。


```r
names(x) <- character(5)

# 元素值超过 10 的索引
which(x > 10)
>>     
>> 4 5

# 值最小的索引
which.min(x)
>>   
>> 1

# 值最大的索引
which.max(x)
>>   
>> 5
```


#### 1.5 循环

- 一个单独的数字与向量相加，则向量的每个元素都会与该数字相加；
- 将两个向量相加时，R 将会循环较短向量中的元素以配合较长的那个；
- 如果长向量的长度不是短向量长度的倍数，将出现一个警告。


```r
1:5 + 1
>> [1] 2 3 4 5 6

1:5 + 1:15
>>  [1]  2  4  6  8 10  7  9 11 13 15 12 14 16 18 20

1:5 + 1:7
>> Warning in 1:5 + 1:7: 长的对象长度不是短的对象长度的整倍数
>> [1]  2  4  6  8 10  7  9
```

<br />
rep 函数允许重复使用元素来创建向量:


```r
rep(1:5, 3)
>>  [1] 1 2 3 4 5 1 2 3 4 5 1 2 3 4 5

rep(1:5, each = 3)
>>  [1] 1 1 1 2 2 2 3 3 3 4 4 4 5 5 5

rep(1:5, times = 1:5)
>>  [1] 1 2 2 3 3 3 4 4 4 4 5 5 5 5 5

rep(1:5, length.out = 7)
>> [1] 1 2 3 4 5 1 2
```

<br />

### 2. 数组
可以使用 array 函数创建一个数组，为它们传入两个向量 (值和维度) 作为参数，同时 dimnames 参数可以为每个维度命名。

<br />
dim 函数可以获取到数组各个维度的整数值向量，dimnames 则用来获取每个维度的名称。


```r
(three_d_array <- array(
    1:24,
    dim = c(4, 3, 2),
    dimnames =  list(
        c("A1", "A2", "A3", "A4"),
        c("B1", "B2", "B3"),
        c("C1", "C2")
  )
))
>> , , C1
>> 
>>    B1 B2 B3
>> A1  1  5  9
>> A2  2  6 10
>> A3  3  7 11
>> A4  4  8 12
>> 
>> , , C2
>> 
>>    B1 B2 B3
>> A1 13 17 21
>> A2 14 18 22
>> A3 15 19 23
>> A4 16 20 24

dim(three_d_array)
>> [1] 4 3 2

dimnames(three_d_array)
>> [[1]]
>> [1] "A1" "A2" "A3" "A4"
>> 
>> [[2]]
>> [1] "B1" "B2" "B3"
>> 
>> [[3]]
>> [1] "C1" "C2"
```

<br />

### 3. 矩阵
#### 3.1 矩阵的创建
创建矩阵的语法与数组非常类似，但无需传递维度 dim 参数，只要指定行数或列数即可。
创建矩阵时，传入的值会按列填充矩阵，也可指定参数 byrow = TRUE 来按行填充矩阵。

<br />
同样，dim 函数将返回矩阵各个维度的整数值向量，而函数 nrow 和 ncol 将分别返回其行数和列数。


```r
# 按列创建矩阵
(a_matrix <- matrix(
    1:12,
    nrow = 4,
    dimnames = list(
        c("one", "two", "three", "four"),
        c("col1", "col2", "col3")
    )
))
>>       col1 col2 col3
>> one      1    5    9
>> two      2    6   10
>> three    3    7   11
>> four     4    8   12

# 按行创建矩阵
matrix(
    1:12,
    nrow = 4,
    byrow = TRUE,
    dimnames = list(
        c("one", "two", "three", "four"),
        c("col1", "col2", "col3")
    )
)
>>       col1 col2 col3
>> one      1    2    3
>> two      4    5    6
>> three    7    8    9
>> four    10   11   12

# 矩阵的维度
dim(a_matrix)
>> [1] 4 3

# 矩阵的行数
nrow(a_matrix)
>> [1] 4

# 矩阵的列数
ncol(a_matrix)
>> [1] 3

# 矩阵的行名
rownames(a_matrix)
>> [1] "one"   "two"   "three" "four"

# 矩阵的列名
colnames(a_matrix)
>> [1] "col1" "col2" "col3"
```

<br />
c 函数能在拼接矩阵之前把它们转换成向量：


```r
(another_matrix <- matrix(
    seq.int(2, 24, 2),
    nrow = 4,
    dimnames = list(
        c("five", "six", "seven", "eight"),
        c("col1", "col2", "col3")
    )
))
>>       col1 col2 col3
>> five     2   10   18
>> six      4   12   20
>> seven    6   14   22
>> eight    8   16   24

c(a_matrix, another_matrix)
>>  [1]  1  2  3  4  5  6  7  8  9 10 11 12  2  4  6  8 10 12 14 16 18 20 22 24
```

#### 3.2 矩阵的索引
矩阵的索引与向量的索引类似，只是要索引的维度多一个。和之前一样，我们用方括号 [] 来表示索引，
且仍有四种指定索引的方法，包括正整数、负整数、逻辑值和元素的名称。

<br />
在不同的维度上用不同的方式指定索引下标完全没问题，每个维度的下标用逗号分隔。


```r
# 第一行，第二列和第三列的元素
a_matrix[1, c("col1", "col3")]
>> col1 col3 
>>    1    9

# 第三行的所有元素
a_matrix[3, ]
>> col1 col2 col3 
>>    3    7   11

# 第二列和第三列的所有元素
a_matrix[, c("col2", "col3")]
>>       col2 col3
>> one      5    9
>> two      6   10
>> three    7   11
>> four     8   12
```

#### 3.3 矩阵的合并
可以使用 cbind 和 rbind 两个函数按行和列合并两个矩阵：


```r
# 横向合并
cbind(a_matrix, another_matrix)
>>       col1 col2 col3 col1 col2 col3
>> one      1    5    9    2   10   18
>> two      2    6   10    4   12   20
>> three    3    7   11    6   14   22
>> four     4    8   12    8   16   24

# 纵向合并
rbind(a_matrix, another_matrix)
>>       col1 col2 col3
>> one      1    5    9
>> two      2    6   10
>> three    3    7   11
>> four     4    8   12
>> five     2   10   18
>> six      4   12   20
>> seven    6   14   22
>> eight    8   16   24
```

#### 3.4 矩阵的运算
和向量中的运算一样，标准算术运算符(+、- 、\*、/) 将以同样的方式按元素来处理矩阵和数组，当对两个数组执行算术运算时，必须确保它们的行和列是一致的。


```r
a_matrix + another_matrix
>>       col1 col2 col3
>> one      3   15   27
>> two      6   18   30
>> three    9   21   33
>> four    12   24   36

a_matrix * another_matrix
>>       col1 col2 col3
>> one      2   50  162
>> two      8   72  200
>> three   18   98  242
>> four    32  128  288
```

也可以进行矩阵的乘法、转置和取逆运算：


```r
# 矩阵的转置
t(a_matrix)
>>      one two three four
>> col1   1   2     3    4
>> col2   5   6     7    8
>> col3   9  10    11   12

# 矩阵的乘法
a_matrix %*% t(a_matrix)
>>       one two three four
>> one   107 122   137  152
>> two   122 140   158  176
>> three 137 158   179  200
>> four  152 176   200  224

square_matrix <- matrix(c(1, 0, 1, 5, -3, 1, 2, 4, 7), nrow = 3)

# 矩阵的取逆
inverse_square_matrix <- solve(square_matrix)

square_matrix %*% inverse_square_matrix
>>      [,1] [,2] [,3]
>> [1,]    1    0    0
>> [2,]    0    1    0
>> [3,]    0    0    1
```



