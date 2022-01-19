# [forcats]关于因子的操作


![data](/posts_pics/forcats关于因子的操作/clipboard1.png)

所有关于因子factor的操作，均可使用R中的forcats包

官方网站:https://github.com/tidyverse/forcats


R包安装与加载：
```R
install.packages('forcats')
library('forcats')
```

使用数据的格式如下图：
![data](/posts_pics/forcats关于因子的操作/clipboard2.png)

## 1.常规作图：

```R
ggplot(dd,aes(y=Number,x=Description))+ 
    geom_bar(stat='identity')+coord_flip()
```
![pic3](/posts_pics/forcats关于因子的操作/clipboard3.png)


## 2.利用fct_reorder函数排序：
这个函数让排序按照一个数值变量来！！

```R
ggplot(dd,aes(y=Number,x=fct_reorder(Description,Number)))+ 
    geom_bar(stat='identity')+coord_flip()
```
即将Description一列按照Number列的数量进行排序，默认为从大到小。

![pic4](/posts_pics/forcats关于因子的操作/clipboard4.png)

这个操作也可用于分面：

数据预处理：
```R
tt <- gapminder %>%filter(country %in% c("Norway", "Portugal", "Spain", "Austria"))
```
数据展示：

![pic5](/posts_pics/forcats关于因子的操作/clipboard5.png)

常规作图：

```R
ggplot(tt,aes(year, lifeExp)) + geom_line(size=0.8) + 
    facet_wrap(~country, nrow = 1)
#or
#ggplot(tt,aes(year, lifeExp)) + geom_line(size=0.8) + 
#    facet_wrap(vars(country), nrow = 1)
```

![pic6](/posts_pics/forcats关于因子的操作/clipboard6.png)

默认结果是按照**分面列的字母排序**来的。

使用fct_reorder进行分面作图：
```R
ggplot(tt,aes(x=year, y=lifeExp)) + 
    geom_line(size=0.8) + 
    facet_wrap(~fct_reorder(country,lifeExp), nrow = 1)
#or
tt %>% mutate(country = fct_reorder(country, lifeExp)) %>% # default: order by median
  ggplot(aes(year, lifeExp)) + geom_line() +
  facet_wrap(vars(country), nrow = 1)   
```

![pic7](/posts_pics/forcats关于因子的操作/clipboard7.png)

因为**country列和lifeExp列是一对多的关系**，默认是按照中位数去排
中位数这个默认操作，是可以换的，比如换成**最低值**,只需在**fct_reorder**中添加第三个参数即可。

```R
ggplot(tt,aes(x=year, y=lifeExp)) + geom_line(size=0.8) + 
    facet_wrap(~fct_reorder(country,lifeExp,min), nrow = 1)
```

![pic8](/posts_pics/forcats关于因子的操作/clipboard8.png)

也可以换成max去试试。你还可以自定义函数，比如说**最大值与最小值之差**，也就是按照差值大小来排:
```R
ggplot(tt,aes(x=year, y=lifeExp)) + 
    geom_line(size=0.8) + 
    facet_wrap(~fct_reorder(country,lifeExp,function(x) { max(x) - min(x) }), nrow = 1)
```

![pic9](/posts_pics/forcats关于因子的操作/clipboard9.png)

反过来排序，只需把函数定义为最小值减最大值，就和上图反过来了。或者是加上fct_rev()函数。


## 3.利用fct_relevel函数对因子进行手动排序

```R
library(palmerpenguins)
````

数据预览：
![pic10](/posts_pics/forcats关于因子的操作/clipboard10.png)

常规作图：
```R
ggplot(penguins,aes(y=species))+geom_bar(width=0.8)
```

这里是直接利用**species出现的次数**来当做x轴进行画图
默认是用a-z进行排序，自下而上
!![pic11](/posts_pics/forcats关于因子的操作/clipboard11.png)

fct_relevel对因子进行手动排序：
```R
ggplot(penguins,aes(y=fct_relevel(species,"Gentoo", "Adelie",'Chinstrap')))+geom_bar(width = 0.8)
```

默认是**先设置的在最下面，逐渐往上展示**
![pic12](/posts_pics/forcats关于因子的操作/clipboard12.png)

## 4.利用fct_infreq对因子按照频率进行排序，默认频率最大的在最下边

```R
ggplot(penguins,aes(y=fct_infreq(species)))+geom_bar(width = 0.8)
```

![pic13](/posts_pics/forcats关于因子的操作/clipboard13.png)


## 5.利用fct_rev可以将上面的排序进行反转
```R
ggplot(penguins,aes(y=fct_infreq(species) %>% fct_rev()))+ 
    geom_bar(width = 0.8)
```

![pic14](/posts_pics/forcats关于因子的操作/clipboard14.png)

## 6.fct_lump_n()

汇总某一列中所有的内容，指定一个n定义出现频率前n个的内容，其他内容可以更改

举例：将main_genre列出现频率最高的前7个正常打印，剩余的其他的均打印为"Less common\n genres"
原格式：

![pic15](/posts_pics/forcats关于因子的操作/clipboard15.png)

```R
mutate(main_genre = fct_lump_n(main_genre, 7,other_level = "Less common\n genres")
```
即出现频率由高到低，前7个最高的不变，其余的都更改为"Less common\n genres"

更改后：

![pic16](/posts_pics/forcats关于因子的操作/clipboard16.png)
