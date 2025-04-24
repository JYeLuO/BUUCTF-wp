# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744870896559-f90a4cc6-987a-4498-8da6-8d7ce5320c58.png)

# 做法

下载压缩包，解压，把解压后的文件拖进Exeinfo PE进行分析

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744871002045-c2abbd74-e769-4ebd-91fd-999639a1f239.png)

32位，无壳

扔进IDA（32位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744871273973-b7f15615-977c-47db-ae97-5a4ac6a39f0a.png)

只是因为在人群中多看了你一眼——第31行的right flag，关键词找到，我们就从这里开始向上分析

```
if ( !strncmp(Dest, Str2, v2) )
    sub_41132F("rigth flag!\n");
```

如果Dest和Str2的前v2个字符相同，系统会打印一个right flag给我们，sub_41132F不难猜出是print

我们点进Dest和Str2看看有啥东西（Dest没啥东西，Str2点进去如下，得到Str2的值）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744876484336-3df90065-325c-40ca-9b86-c17aaba27428.png)

结合上面分析，得出Dest的值（即Str2的值）

往上继续分析

```
for ( j = 0; j < v8; ++j )
    Dest[j] += j;
```

一个for循环，Dest[0] = Dest[0]+0，Dest[1] = Dest[1]+1 ......这般规律为结果生成下去，直到j=v8（该循环对Dest做了变化）

再往上就没啥了

但是有一个点不清楚它的作用

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744875610503-0afa183a-3b45-48ae-ba75-f8990a7f03b1.png)

点进sub_4110BE看看

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744875655126-94633484-e33c-4edb-a199-cfe3a9ada7ae.png)

继续点进去

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744875682868-bd749d7e-5e37-457f-9a3c-c2d7e6c91d68.png)

遍历一下

（注：看到第16行的 *3 和第19行的 *4 ，这里就是base64对字符的二进制编码做的处理（经验之谈），要不确定的话我们可以继续往下看，这是定义了v9和v10来存放base64对字符的二进制变化；

Base64加密的核心逻辑（分组、补零、填充）必须通过if判断和循环来实现。下面还有if判断和while循环还有for循环，至此，我们基本可以判断本题是Base64加密题）

exp

```
import base64           #导入模块
Des = "e3nifIH9b_C@n@dH"    #定义目标字符串
flag = ""                   #初始化结果字符串
for i in range(len(Des)):
    flag += chr(ord(Des[i]) - i)    #Des[i]：取出字符串 Des 中索引为 i 的字符。
                                    #ord(Des[i])：使用 ord() 函数获取（）内字符的 ASCII 码值。
                                    #ord(Des[i]) - i：将该 ASCII 码值减去其索引位置 i。
print(base64.b64decode(flag))       #进行 Base64 解码并输出结果
```

得出结果，' '里的内容换成flag{}格式提交即可

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744876150709-64ea8704-1416-49ae-a848-1b94ad730bc3.png)

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744876209277-52c2f8b3-4194-443f-b290-f5ed684a7364.png)

# 更新

于2025.4.17