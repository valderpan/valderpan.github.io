# [stringr]处理字符串


# 【stringr】处理字符串

> 【数据与平行世界】R数据处理-4:stringr与正则表达式
>


![Untitled](/posts_pics/stringr处理字符串/Untitled.png)

# 1.【str_extract、str_extract_all】 字符串提取

字符提取函数`str_extract`和`str_extract_all`，对字符串进行提取，`str_extract_all`函数返回所有的匹配结果。

```r
# 提取第一个匹配到的字符
str_extract(string = "banana",pattern = "a")

## [1] "a"

# 提取所有匹配到的字符（返回列表）
str_extract_all(string = "banana",pattern = "a")

## [[1]]
## [1] "a" "a" "a"

# 提取所有匹配到的字符（返回矩阵）
str_extract_all(string = "banana",pattern = "a", simplify = T)

##      [,1] [,2] [,3]
## [1,] "a"  "a"  "a"
```


- 需求一：添加一列，此列为原df里面某一列中的几个字符。例如去掉等位的染色体ID

```r
#使用正则表达式匹配
dd %>% mutate(CHR=str_extract(X1,"Chr\\d+"))
#BaseR
dd %>% mutate(CHR=substr(X1,1,5))
```

![Untitled](/posts_pics/stringr处理字符串/Untitled1.png)

![Untitled](/posts_pics/stringr处理字符串/Untitled2.png)

![Untitled](/posts_pics/stringr处理字符串/Untitled3.png)

# 2.【str_c】字符串拼接

- stringr

str_c需要传入两个字符参数，另外,collapse参数是将两个字符参数连接，sep参数是将两个字符参数内部的字符连接

即：**「sep用于指定变量内连接符，collapse用于指定变量间连接符！」**

**所谓变量间：如：c("a","b")；c("c","d") -- 这就是两个变量，指定他们之间的连接符用的就是 collapse；**

**所谓变量内：如：c("a","b") -- a和 b都属于一个向量，所以他们两之间的连接符用 sep来指定！**

```r
# 默认无向量分割符拼接
str_c("a","b")

## [1] "ab"

# 指定向量分隔符
str_c("a","b",sep = "_")

## [1] "a_b"

# 指定向量折叠符
str_c(c("a","b","c"),collapse = "_")

## [1] "a_b_c"

# 混合应用
str_c(c("a","b"),c("c","d"),sep = "/",collapse = "_")

## [1] "a/c_b/d"

str_c(c('a','b'),c('c','d'),collapse = '\\')

## [1] "ac\\bd"
```

- 使用baseR实现上述：

```r
## 顺便再科普一下paste和paste0的用法: 两者的唯一区别在于paste0的sep=""
paste("a","b")  # 默认用空格连接
# [1] "a b"

paste0("a","b")  # 默认无向量分割符拼接
# [1] "ab"

paste("a","b",sep = "_")  # 指定分割符,但是paste0没有sep参数！
# [1] "a_b"

paste(c("a","b","c"),collapse = "_")  # paste元素内部连接
# [1] "a_b_c"

paste0(c("a","b","c"),collapse = "_")  # paste0元素内部连接
# [1] "a_b_c"

# 混合应用
paste(c("a","b"),c("c","d"),sep = "/",collapse = "_")
# [1] "a/c_b/d"

paste0(c("a","b"),c("c","d"), collapse = "_")
# [1] "ac_bd"
```

# 3.【str_split、str_split_fixed】字符串分割

字符分割函数`str_split`和`str_split_fixed`，对字符串进行分割。

```r
# 字符分割，返回列表
str_split(string = "banana",pattern = "")

## [[1]]
## [1] "b" "a" "n" "a" "n" "a"

# 字符分割，返回矩阵
str_split(string = "banana",pattern = "",simplify = T)

##      [,1] [,2] [,3] [,4] [,5] [,6]
## [1,] "b"  "a"  "n"  "a"  "n"  "a"

# 字符分割，指定分割块数
str_split_fixed(string = "banana",pattern = "",n = 3)

##      [,1] [,2] [,3]  
## [1,] "b"  "a"  "nana"
```

# 4.【str_sub、str_subset】 提取字符串

函数`str_sub`和`str_subset`

`str_sub`函数通过指定开始和结束位置，提取出字符串的部分字符串。`str_subset`函数通过匹配模式，过滤出满足模式的字符串。

```r
###### 按位置提取：######
# 字符提取（正向索引）
str_sub(string = "banana",start = 1,end = 3)

## [1] "ban"

# 字符提取（反向索引）
str_sub(string = "banana",start = -2,end = -1)

## [1] "na"

# 字符提取，并赋值
x <- "banana"
str_sub(string = x,start = 1,end = 1) <- "A"
print(x)

## [1] "Aanana"

###### 按匹配模式提取 ######
# 字符串提取（返回字符串）
str_subset(string = c("java","sql","python"),pattern = "^s")

## [1] "sql"

# 字符串提取（返回位置）
str_which(string = c("java","sql","python"),pattern = "^s")

## [1] 2
```

# 5.【str_count】 统计字符串出现的次数

字符计数函数`str_count`，计算字符串中指定字符的个数。

```r
# 单个目标字符计数
str_count(string = c("sql","json","java"),pattern = "s")

## [1] 1 1 0

# 多个目标字符计数
str_count(string = c("sql","json","java"),pattern = c("s","j","a"))

## [1] 1 1 2

# 元字符查找计数（fixed包裹元字符）
str_count(string = "a..b",pattern = fixed("."))

## [1] 2
```

# 6.【str_detect】字符检查

```r
# 字符检查函数`str_detect`，检查字符串中是否包含指定字符，返回逻辑向量。
str_detect(string = c("sql","json","java"),pattern = "s")

## [1]  TRUE  TRUE FALSE
```

# 7.【str_dup】字符复制

字符复制函数`str_dup`，将字符向量重复若干次，返回重复后的字符向量。

```r
str_dup(string = c("sql","json","java"),times = 2)

## [1] "sqlsql"   "jsonjson" "javajava"
```

# 8.【str_glue】字符格式化

字符串格式化函数`str_glue`，用花括号`{}`表示占位符，括号内的变量被替换成全局变量值。

```r
# 定义全局变量
name <- "jack"
age <- 12

# 字符串格式化
str_glue("My name is {name},","\nmy age is {age}.")

## My name is jack,
## my age is 12.
```

# 9.【str_length】**字符串长度**

字符串长度函数`str_length`，计算字符串长度。

```r
str_length(string = "banana")

## [1] 6

# 功能等同于R自带的函数：
nchar("banana")
```

# 10.【str_locate、str_locate_all】字符位置提取

字符位置提取函数`str_locate`和`str_locate_all`,返回匹配到的字符的位置。

```r
# 返回第一个匹配到的字符的位置
str_locate(string = "banana",pattern = "a")

##      start end
## [1,]     2   2

# 返回所有匹配到的字符的位置
str_locate_all(string = "banana",pattern = "a")

## [[1]]
##      start end
## [1,]     2   2
## [2,]     4   4
## [3,]     6   6
```

# 11.【str_pad】字符补齐

字符补齐函数`str_pad`，用于在字符串中添加单个字符，可选择添加的位置，在参数`side`中进行设置。

```r
# 默认字符串左边补齐
str_pad(string = "jack",width = 6,pad = "S")

## [1] "SSjack"

# 字符串右边补齐
str_pad(string = "jack",width = 6,side = "right",pad = "S")

## [1] "jackSS"

# 字符串两边补齐
str_pad(string = "jack",width = 6,side = "both",pad = "S")

## [1] "SjackS"
```

# 12.【str_remove、str_remove_all】字符删除

字符删除函数`str_remove`和`str_remove_all`，用于删除字符串中的部分字符。

```r
# 删除第一个匹配到的字符
str_remove(string = "banana",pattern = "a")

## [1] "bnana"

# 删除所有匹配到的字符
str_remove_all(string = "banana",pattern = "a")

## [1] "bnn"
```

# 13.【str_replace、str_replace_all、str_replace_na】**字符替换**

字符替换函数`str_replace`、`str_replace_all`和`str_replace_na`，用于替换字符串中的部分字符。

```r
# 替换第一个匹配到的字符
str_replace(string = "banana",pattern = "a",replacement = "A")

## [1] "bAnana"

# 替换所有匹配到的字符
str_replace_all(string = "banana",pattern = "a",replacement = "A")

## [1] "bAnAnA"

# 将NA替换成NA字符
str_replace_na(string = c("banana",NA))

## [1] "banana" "NA"
```

# 14.【sre_sort、str_order】字符排序

字符排序函数`str_sort`和`str_order`，对字符向量进行排序。

```r
# 字符向量升序排序，返回字符向量
str_sort(c("sql","json","python"))

## [1] "json"   "python" "sql"

# 字符向量降序排序，返回字符向量
str_sort(c("sql","json","python"),decreasing = TRUE)

## [1] "sql"    "python" "json"

# 字符向量升序排序，返回索引向量
str_order(c("sql","json","pythn"))

## [1] 2 3 1
```

# 15.其他函数

`stringr`包中其他的有用函数，用于常见的字符处理

```r
# 删除字符串两边的空格
str_trim(string = "  you are beautiful!  ")

## [1] "you are beautiful!"

# 删除字符串中多余的空格
str_squish(string = "  you are  beautiful!  ")

## [1] "you are beautiful!"

# 字符转为小写
dog <- "The quick brown dog"
str_to_lower(dog)

## [1] "the quick brown dog"

# 字符转为大写
str_to_upper(dog)

## [1] "THE QUICK BROWN DOG"

# 字符转为标题
str_to_title(dog)

## [1] "The Quick Brown Dog"

# 字符转为语句
str_to_sentence(dog)

## [1] "The quick brown dog"
```
