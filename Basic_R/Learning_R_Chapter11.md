---
title: "学习R之日期和时间"
author: "数分进阶"
date: "3/13/2021"
output: html_document
---



### 1. 日期和时间类
#### 1.1 POSIX 日期和时间

R 中的两个标准的日期-时间类是 **POSIXct** 和 **POSIXlt**。

**POSIXct** 类记录了以世界标准时 UTC 时区为准的从 1970 年开始计时的秒数计数，**POSIXlt** 则将日期存储为一个列表，其中包括秒、 分钟、小时和月份等。

**POSIXct** 最适用于存储和计算时间，而 **POSIXlt** 最适用于提取日期中的某个特定部分。


```r
# 以 POSIXct 的形式返回当前的日期和时间
(now_ct <- Sys.time())
>> [1] "2021-03-13 20:47:04 CST"

# POSIXct 的日期是通过数字存储的
unclass(now_ct)
>> [1] 1615639625

# POSIXlt 日期看上和 POSIXct 一样，但底层存储机制非常不同
(now_lt <- as.POSIXlt(now_ct))
>> [1] "2021-03-13 20:47:04 CST"

# POSIXlt 的日期是以列表形式存储的
unclass(now_lt)
>> $sec
>> [1] 4.752203
>> 
>> $min
>> [1] 47
>> 
>> $hour
>> [1] 20
>> 
>> $mday
>> [1] 13
>> 
>> $mon
>> [1] 2
>> 
>> $year
>> [1] 121
>> 
>> $wday
>> [1] 6
>> 
>> $yday
>> [1] 71
>> 
>> $isdst
>> [1] 0
>> 
>> $zone
>> [1] "CST"
>> 
>> $gmtoff
>> [1] 28800
>> 
>> attr(,"tzone")
>> [1] ""    "CST" "CDT"
```

#### 1.2 Date
Date 类存储了从 1970 年开始计算的天数，最适用于只关注日期而不关注具体时间的情况。


```r
(now_date <- as.Date(now_ct))
>> [1] "2021-03-13"
```

#### 1.3 日期和字符串的相互转化
字符串转化为日期，可以使用 strptime 函数，对应的，日期变量转换为字符串，使用 strftime 函数。

在进行转化时，%H 表示 24 小时制，%I 表示 12 小时制，%M 是分钟，%S 是秒，%d 是天数，%A 是星期的全称，%m 是月数，%B 是月份的全名，%Y 是四位数的年份。


```r
moon_landings_str <- c(
    "20:17:40 20/07/1969",
    "06:54:35 19/11/1969",
    "09:18:11 05/02/1971",
    "22:16:29 30/07/1971",
    "02:23:35 21/04/1972",
    "19:54:57 11/12/1972"
)
(moon_landings_lt <- strptime(moon_landings_str, "%H:%M:%S %d/%m/%Y", tz = "UTC"))
>> [1] "1969-07-20 20:17:40 UTC" "1969-11-19 06:54:35 UTC" "1971-02-05 09:18:11 UTC" "1971-07-30 22:16:29 UTC"
>> [5] "1972-04-21 02:23:35 UTC" "1972-12-11 19:54:57 UTC"

strftime(now_ct, "It's %I:%M on %A %d %B, %Y.")
>> [1] "It's 08:47 on 星期六 13 三月, 2021."
```

#### 1.4 时区
很多国家常有好几个时区，而且当一些国家切换到夏令时需要改变边界。

在使用 strptime 解析日期字符串时，可以指定一个时区；使用 strftime 格式化日期时，也可以再次改变它。

在解析的过程中，如果不指定时区，R 会给日期以默认时区，这个值是 Sys.timezone 返回的一个值，取决于你的操作系统和具体的设置。


```r
strftime(now_ct, tz = "America/Los_Angeles")
>> [1] "2021-03-13 04:47:04"

strftime(now_ct, tz = "Asia/Kolkata")
>> [1] "2021-03-13 18:17:04"
```

#### 1.5 日期和时间的运算
R 支持三个日期与时间基类的算术运算：将数字与 POSIX 日期相加，会以秒为单位增加时间，将数字与 Date 相加会以天数为单位。


```r
# POSIXct 时间的加法
now_ct + 86400
>> [1] "2021-03-14 20:47:04 CST"

# POSIXlt 时间的加法
now_lt + 86400
>> [1] "2021-03-14 20:47:04 CST"

# Date 的加法
now_date + 1 
>> [1] "2021-03-14"

# 日期做差值
the_start_of_time <- as.Date("2010-01-01")
the_end_of_time <-as.Date("2021-01-01")
(time_diff <- the_end_of_time - the_start_of_time)
>> Time difference of 4018 days

unclass(time_diff)
>> [1] 4018
>> attr(,"units")
>> [1] "days"
```

<br />
使用 difftime 函数来计算出日期和时间之间的差值，默认以数字的形式存储并以天为单位，可以通过 units 参数指定单位。


```r
difftime(the_end_of_time, the_start_of_time, units = "secs")
>> Time difference of 347155200 secs

difftime(the_end_of_time, the_start_of_time, units = "weeks")
>> Time difference of 574 weeks
```

<br />
生成序列的 seq 函数也适用于日期。


```r
seq(the_start_of_time, the_end_of_time, by = "1 year")
>>  [1] "2010-01-01" "2011-01-01" "2012-01-01" "2013-01-01" "2014-01-01" "2015-01-01" "2016-01-01" "2017-01-01"
>>  [9] "2018-01-01" "2019-01-01" "2020-01-01" "2021-01-01"

seq(the_start_of_time, the_end_of_time, by = "6 months")
>>  [1] "2010-01-01" "2010-07-01" "2011-01-01" "2011-07-01" "2012-01-01" "2012-07-01" "2013-01-01" "2013-07-01"
>>  [9] "2014-01-01" "2014-07-01" "2015-01-01" "2015-07-01" "2016-01-01" "2016-07-01" "2017-01-01" "2017-07-01"
>> [17] "2018-01-01" "2018-07-01" "2019-01-01" "2019-07-01" "2020-01-01" "2020-07-01" "2021-01-01"
```

### 2. lubridate
lubridate 是 R 中专门用来处理日期和的时间包，使用之前，需要通过 **install.packages("lubridate")** 命令先安装一下。

lubridate 有多种使用了预设格式的解析函数，如 ymd 接受年、月、日的 日期形式，它最大的优点在于：相同向量中的不同元素可用不同的格式，只要年月日的先后顺序不变即可。


```r
# 不同格式的日期字符串，处理结果完全一致
library(lubridate)
john_harrison_birth_date <- c ("1693-03 24", "1693/03\\24", "Tuesday+1693.03*24")
ymd(john_harrison_birth_date) 
>> [1] "1693-03-24" "1693-03-24" "1693-03-24"
```

<br />
如果你的日期数据的形式有所不同，可以使用 lubridate 提供的其他函数 (ydm、mdy、myd、dmy 和 dym)，这些函数也都有相关的函数用于指定特定的时间格式，如 ymd_h、ymd_hm 和 ymd_hms 等。

lubridate 主要有持续时间 Duartion、周期 Period 两种用来处理时间范围的类型。
持续时间 Duration 指定的时间跨度为秒的倍数，所以一天的总时间是 86400 秒，其他用于创建持续时间的函数为 dseconds、dminutes 等。


```r
(duration_one_to_ten_years <- dyears(1:10))
>>  [1] "31557600s (~1 years)"   "63115200s (~2 years)"   "94672800s (~3 years)"   "126230400s (~4 years)" 
>>  [5] "157788000s (~5 years)"  "189345600s (~6 years)"  "220903200s (~7 years)"  "252460800s (~8 years)" 
>>  [9] "284018400s (~9 years)"  "315576000s (~10 years)"
today() + duration_one_to_ten_years
>>  [1] "2022-03-13 06:00:00 UTC" "2023-03-13 12:00:00 UTC" "2024-03-12 18:00:00 UTC" "2025-03-13 00:00:00 UTC"
>>  [5] "2026-03-13 06:00:00 UTC" "2027-03-13 12:00:00 UTC" "2028-03-12 18:00:00 UTC" "2029-03-13 00:00:00 UTC"
>>  [9] "2030-03-13 06:00:00 UTC" "2031-03-13 12:00:00 UTC"
```

<br />
周期 Period 根据时钟上的时间来指定时间跨度，但确切的时间跨度实际上是不确定的，如一年的周期可以是 365 或 366 天，这取决于它是否是闰年。


```r
(period_one_to_ten_years <- years(1:10))
>>  [1] "1y 0m 0d 0H 0M 0S"  "2y 0m 0d 0H 0M 0S"  "3y 0m 0d 0H 0M 0S"  "4y 0m 0d 0H 0M 0S"  "5y 0m 0d 0H 0M 0S" 
>>  [6] "6y 0m 0d 0H 0M 0S"  "7y 0m 0d 0H 0M 0S"  "8y 0m 0d 0H 0M 0S"  "9y 0m 0d 0H 0M 0S"  "10y 0m 0d 0H 0M 0S"
today() + period_one_to_ten_years
>>  [1] "2022-03-13" "2023-03-13" "2024-03-13" "2025-03-13" "2026-03-13" "2027-03-13" "2028-03-13" "2029-03-13"
>>  [9] "2030-03-13" "2031-03-13"
```

