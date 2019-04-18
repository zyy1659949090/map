# map
map
在这篇文章中,我将展示一个关于美国地区肥胖事件的地图分析表示：

> library(ggplot2     )载入需要的包
> library(rvest)
> library(ggplot2)
> library(dplyr)
> library(scales)
> library(xml2)
> library(maps)
> obesity=read.csv("E:/obesity1.csv") 载入数据
> head(obesity)
  State.and.District Obese.adults Overweight Obese.children Obesity.rank
1  Alabama   30.10%   65.40%    16.70%      3
2  Alaska    27.30%   64.50%   11.10%      14
3 Arizona  23.30%  59.50%    12.20%        40
4 Arkansas   28.10%  64.70%    16.40%       9
> for(i in 2:4){
+   obesity[,i] = gsub("%", "", obesity[,i])  ##去除百分号
+   obesity[,i] = as.numeric(obesity[,i])  ##转换为数值变量
+ }
> str(obesity)
'data.frame':    51 obs. of  5 variables:
 $ State.and.District: Factor w/ 51 levels "Alabama","Alaska",..: 1 2 3 4 5 6 7 9 8 10 ...
 $ Obese.adults      : num  30.1 27.3 23.3 28.1 23.1 21 20.8 22.1 25.9 23.3 ...
 $ Overweight        : num  65.4 64.5 59.5 64.7 59.4 55 58.7 55 63.9 60.8 ...
 $ Obese.children    : num  16.7 11.1 12.2 16.4 13.2 9.9 12.3 14.8 22.8 14.4 ...
 $ Obesity.rank      : int  3 14 40 9 41 51 49 43 22 39 ...
> names(obesity) = make.names(names(obesity))  #转化变量名称
> names(obesity)
[1] "State.and.District" "Obese.adults"       "Overweight"        
[4] "Obese.children"     "Obesity.rank"
> states = map_data("state")   #形成关于地区的数据框
> str(states)
'data.frame':    15537 obs. of  6 variables:
 $ long     : num  -87.5 -87.5 -87.5 -87.5 -87.6 ...
 $ lat      : num  30.4 30.4 30.4 30.3 30.3 ...
 $ group    : num  1 1 1 1 1 1 1 1 1 1 ...
 $ order    : int  1 2 3 4 5 6 7 8 9 10 ...
 $ region   : chr  "alabama" "alabama" "alabama" "alabama" ...
 $ subregion: chr  NA NA NA NA ...
> obesity$region = tolower(obesity$State.and.District)##转化为小写
> states = merge(states, obesity, by="region", all.x=T)#按照地区对两个数据集进行合并
> str(states)
'data.frame':    15537 obs. of  11 variables:
 $ region            : chr  "alabama" "alabama" "alabama" "alabama" ...
 $ long              : num  -87.5 -87.5 -87.5 -87.5 -87.6 ...
 $ lat               : num  30.4 30.4 30.4 30.3 30.3 ...
 $ group             : num  1 1 1 1 1 1 1 1 1 1 ...
 $ order             : int  1 2 3 4 5 6 7 8 9 10 ...
 $ subregion         : chr  NA NA NA NA ...
 $ State.and.District: Factor w/ 51 levels "Alabama","Alaska",..: 1 1 1 1 1 1 1 1 1 1 ...
 $ Obese.adults      : num  30.1 30.1 30.1 30.1 30.1 30.1 30.1 30.1 30.1 30.1 ...
 $ Overweight        : num  65.4 65.4 65.4 65.4 65.4 65.4 65.4 65.4 65.4 65.4 ...
 $ Obese.children    : num  16.7 16.7 16.7 16.7 16.7 16.7 16.7 16.7 16.7 16.7 ...
 $ Obesity.rank      : int  3 3 3 3 3 3 3 3 3 3 ...
> library(mapproj)
> # adults
> ggplot(states, aes(x = long, y = lat, group = group, fill = Obese.adults)) + 
+   geom_polygon(color = "white") +
+   scale_fill_gradient(name = "Percent", low = "#feceda", high = "#c81f49", guide = "colorbar", na.value="black", breaks = pretty_breaks(n = 5)) + #指定渐变色
+   labs(title="Prevalence of Obesity in Adults") +
+   coord_map()  #默认系统的视角方式看地图
> # children
> ggplot(states, aes(x = long, y = lat, group = group, fill = Obese.children)) + 
+   geom_polygon(color = "white") +
+   scale_fill_gradient(name = "Percent", low = "#feceda", high = "#c81f49", guide = "colorbar", na.value="black", breaks = pretty_breaks(n = 5)) +
+   labs(title="Prevalence of Obesity in Children") +
+   coord_map()
