# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744802135964-69982652-f78b-4232-aed6-d9e0ba13c28b.png)

# 做法

先自行下载Exeinfo PE这个软件

下载压缩包，解压，把解压后的文件拖进Exeinfo PE进行分析

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744802403361-259cb949-e269-40ea-88bf-6179728514dc.png)

64位

把文件进IDA（64位）

找到main，双击

按F5反编译成c语言

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744802528646-256ee660-cd9b-4ea8-ae0e-f0ca79b13e3b.png)

flag就这样水灵灵地出现了，去提交就OK了

# 更新

于2025.4.16