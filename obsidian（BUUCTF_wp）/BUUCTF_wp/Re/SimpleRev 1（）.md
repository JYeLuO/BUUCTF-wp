# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1745021821724-3863a0d8-da61-4d8c-9af5-19fcbad70e3d.png)

# 做法

下载文件，拖进Exeinfo PE

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1745021978433-a8c6ddd1-114f-489b-b97a-3e1ae436ccd7.png)

64位，无壳

扔进IDA（64位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1745022054250-bf21938f-cf23-4712-b46d-99fd502e3908.png)

有数字，我们按r转换为字符看看

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1745023591377-e338f2a6-86c9-477f-a9fa-2047c6c5fe16.png)

若v4=d/D，我们就可以进入Decry函数

若v4=q/Q，我们就退出

输入除去这几个的其他字母，结果均是break

我们点进去Decry函数看看

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1745022705647-66627e25-8bfe-483a-b547-585950cec064.png)

找到真面目了

```
  if ( !strcmp(text, str2) )
    puts("Congratulation!\n");
```

看到这里的congratulation，结合上面的Please input your flag，我们就知道该从这里往上分析了

这里的意思是：让text等于str2

上面有一大串函数，简单来说就是v1满足不同条件，v2

# 更新

于2025.4.

# 笔记