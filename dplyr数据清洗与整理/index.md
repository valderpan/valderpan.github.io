# [dplyr]数据清洗与整理


![dplyr](/posts_pic/dplyr数据清洗与整理/clipboard1.png)



# dplyr主要函数：

+ mutate() 对已有**列数据**变换并添加新的**列**，类似标准函数transform
+ select() 按照**列名**筛选**列**，可结合starts_with，ends_with，contains，matches，one_of，num_range和everything等使用
+ filter() 按照已定条件对行做过滤，类似标准函数subset
+ arrange() 对数据进行排序，类似标准函数order
+ summarise() 对数据进行汇总操作,可结合**group_by**使用,类似标注函数aggregate



## 1.arrange函数对data排序

```R
data
# group value
# 1     A    13
# 2     B     7
# 3     C     9
# 4     D    21
# 5     E     2
arrange(data,value) 
#按照value列升序排序 或者 data %>% arrange(value)
# group value
# 1     E     2
# 2     B     7
# 3     C     9
# 4     A    13
# 5     D    21
arrange(data,desc(value)) 
#按照value列降序排序 或data %>% arrange(desc(value))
# group value
# 1     D    21
# 2     A    13
# 3     C     9
# 4     B     7
# 5     E     2
```



## 2.mutate函数新增一列
mutate()函数是对已有列数据变换并添加为新的列
```R
# 添加一个新的列new，并且new是Sepal.Length和Sepal.Width的乘积：
df_mutate<- mutate(iris, new = Sepal.Length * Sepal.Width)
head(df_mutate)
```
![pic2](/posts_pic/dplyr数据清洗与整理/clipboard2.png)




## 3.select函数选取列
按照列名筛选列，可结合starts_with，ends_with，contains，matches，one_of，num_range和everything等使用：

```R
# 列名称以Petal开头的列
df_select<- select(iris, starts_with("Petal"))
# 列名称以Width结尾的列
df_select<- select(iris, ends_with("Width"))
# 列名称包含etal的列
df_select<- select(iris, contains("etal"))
# 列名称匹配t的列
df_select<- select(iris, matches(".t."))
# 只筛选Petal.Length,Petal.Width两个列
df_select<- select(iris, Petal.Length, Petal.Width)
# 筛选所有的变量
df_select<- select(iris, everything())
# 把需要的变量赋值给vars，然后用one_of模式筛选
vars<- c("Petal.Length", "Petal.Width")
df_select<- select(iris, one_of(vars))
```




## 4.filter() 函数的使用

按照已定条件对行做过滤，类似标准函数subset,在使用filter之前需要了解一下几个逻辑操作符：
+ ==, >, >=, <, <=, %in%
+ &, |, ！, xor()
+ is.na()
+ between(), near()

使用韦恩图表示不同类型连接方式：
![pic7](/posts_pic/dplyr数据清洗与整理/clipboard3.png)




```R
# 筛选Species是setosa的数据(根据行内容进行选择)
df_filter<- filter(iris, Species == "setosa")

# 筛选Sepal.Length大于5，并且Species是setosa的数据
df_filter<- filter(iris, Sepal.Length > 5 & Species == "setosa")

# 筛选Sepal.Length大于5，并且Species包含在c("setosa","versicolor")中的数据
df_filter<- filter(iris, Sepal.Length > 5 & Species %in%c("setosa","versicolor"))
```



## 5.summarise函数的使用：

对数据进行汇总操作,可结合group_by使用：

```R
# 计算Sepal.Length的平均值和标准差：
df_summarise<- summarise(iris, mean(Sepal.Length), sd(Sepal.Length))
```

![pic4](/posts_pic/dplyr数据清洗与整理/clipboard4.png)




```R
# 按照Species分组，计算Sepal.Length的平均值和标准差
iris_groups<- group_by(iris, Species)
df_summarise<- summarise(iris_groups, mean(Sepal.Length), sd(Sepal.Length))

# dplyr的计算是以tbl格式返回的，我们可以转成dataframe, 当然这步没必要，但是为了方便显示：
df_summarise<- as.data.frame(df_summarise)

#以上代码可以简写为：
iris %>% group_by(Species) %>% summarise(`mean(Sepal.Length)`=mean(Sepal.Length),`sd(Sepal.Length)`=sd(Sepal.Length))
```

![pic5](/posts_pic/dplyr数据清洗与整理/clipboard5.png)




## 6.inner_join、left_join、right_join、full_join

参考网页：https://www.jianshu.com/p/1f4c7bfed3d4

数据类型：

![pic6](/posts_pic/dplyr数据清洗与整理/original-dfs1.png)



### 合并连接
#### 内连接
+ inner_join 函数
内连接的结果是一个新数据框，其中包含键、 x 值和 y 值。我们使用 by 参数告诉 dplyr 哪个变量是键：
```R
x %>%  inner_join(y, by = "key")
# A tibble: 2 x 3
    key val_x val_y
  <dbl> <chr> <chr>
1     1 x1    y1   
2     2 x2    y2
```

![gif1](/posts_pic/dplyr数据清洗与整理/inner-join1.gif)




#### 外连接

内连接保留同时存在于两个表中的观测， 外连接则保留至少存在于一个表中的观测。外连接有 3 种类型。
1. 左连接：保留 x 中的所有观测。
2. 右连接：保留 y 中的所有观测
3. 全连接：保留 x 和 y 中的所有观测。


+ left_join函数
	+ 左连接：保留 x 中的所有观测
```R
> left_join(x, y, by = "key")
# A tibble: 3 x 3
    key val_x val_y
  <dbl> <chr> <chr>
1     1 x1    y1   
2     2 x2    y2   
3     3 x3    NA
```

![gif2](/posts_pic/dplyr数据清洗与整理/left-join1.gif)


另：**如果x中的key变量，在y中有多个同样的key，那么所有的结合可能都会罗列出来**

```R
> left_join(x, y1, by = "key")
# A tibble: 4 x 3
    key val_x val_y
  <dbl> <chr> <chr>
1     1 x1    y1   
2     2 x2    y2   
3     2 x2    y5   
4     3 x3    NA
```

![gif3](/posts_pic/dplyr数据清洗与整理/left-join-extra2.gif)



+ right_join函数
	+ 右连接：保留 y 中的所有观测

```R
> right_join(x, y, by = "key")
# A tibble: 3 x 3
    key val_x val_y
  <dbl> <chr> <chr>
1     1 x1    y1   
2     2 x2    y2   
3     4 NA    y3 
```

![gif4](/posts_pic/dplyr数据清洗与整理/right-join1.gif)



+ full_join函数
	+ 全连接：保留 x 和 y 中的所有观测。

```R
> full_join(x, y, by = "key")
# A tibble: 4 x 3
    key val_x val_y
  <dbl> <chr> <chr>
1     1 x1    y1   
2     2 x2    y2   
3     3 x3    NA   
4     4 NA    y3
```

![gif5](/posts_pic/dplyr数据清洗与整理/full-join1.gif)







dplyr中这些连接函数对应基础函数merge中的功能表：
![pic8](/posts_pic/dplyr数据清洗与整理/clipboard6.png)



dplyr这几种函数的来源SQL中对应的功能：
![pic9](/posts_pic/dplyr数据清洗与整理/clipboard7.png)




### 筛选链接
筛选连接匹配观测的方式与合并连接相同，但前者影响的是观测，而不是变量。筛选连接有两种类型。
1. semi_join(x, y)： 保留 x 表中与 y 表中的观测相匹配的所有观测。
2. anti_join(x, y)： 丢弃 x 表中与 y 表中的观测相匹配的所有观测

+ semi_join函数：
保留 x 表中与 y 表中的观测相匹配的所有观测
```R
> semi_join(x, y, by = "key")
# A tibble: 2 x 2
    key val_x
  <dbl> <chr>
1     1 x1   
2     2 x2
```

![gif6](/posts_pic/dplyr数据清洗与整理/semi-join1.gif)




+ anti_join函数：
丢弃 x 表中与 y 表中的观测相匹配的所有观测
```R
> anti_join(x, y, by = "key")
# A tibble: 1 x 2
    key val_x
  <dbl> <chr>
1     3 x3
```

![gif6](/posts_pic/dplyr数据清洗与整理/anti-join1.gif)




## 7.case_when()

可实现**==多条件判断并可以添加标签的函数==**，这在我们对数据进行分类整理中十分的实用，这个函数中的参数可以这样分：**一部分是判断条件，另一部分是所要做的标签**

可结合mutate函数进行使用

源文件格式：

![pic10](/posts_pic/dplyr数据清洗与整理/clipboard8.png)


```R
tuesdata$movies %>%
  mutate(clean_test = case_when(     #判断筛选到a时，将a替换为b
    clean_test == "ok" ~ "Pass Bechdel",
    clean_test == "dubious" ~ "Dubious",
    clean_test == "men" ~ "About men",
    clean_test == "notalk" ~ "No talk",
    clean_test == "nowomen" ~ "No women",
  ))
```
**判断clean_test这一列，当筛选到前面的字符串时，将其替换为后面的字符串！**

更改后：

![pic11](/posts_pic/dplyr数据清洗与整理/clipboard9.png)


