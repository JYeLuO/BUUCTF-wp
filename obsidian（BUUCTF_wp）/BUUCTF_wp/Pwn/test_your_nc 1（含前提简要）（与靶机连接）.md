# 题目：

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743130493992-354469dc-1f59-4b62-a445-9873bcefa899.png)

先启动靶机

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743296216108-612ed437-564b-43de-b344-6c2ab7283391.png)

# 方法一：

常规测试

直接在虚拟机nc与靶机进行连接

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743296282514-5f1bf6d3-2746-4daf-b40d-e6f514b885ea.png)

这道题连接后可能会没有显示东西出来，输入ls查看里面有啥文件（ls：列出当前地址/当前目录下的文件与子目录）

这里我们看到有flag文件

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743296536875-a12af91e-4162-43d7-ae50-3b1bc9966508.png)

输入cat flag即可找到flag，返回题目输入答案并提交即可（cat：查看文件内容）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743296749045-556e7f6b-10cd-430f-bae4-2ce67a2e86bc.png)

# 方法二：

脚本（exp）——题目常规做法

先把文件拖入虚拟机，用checksec命令查看该文件信息

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743298647063-793f460d-b8e6-4bdc-86ec-72046b264f7f.png)

我们用AI进行分析

- **Arch: amd64 - 64 - little**：表示该文件的架构是 64 位小端序的 AMD64 架构 。
- **RELRO: Partial RELRO**：RELRO（Relocation Read - Only）是一种针对共享库重定位表的保护机制。Partial RELRO 意味着部分重定位表在程序加载后变为只读，能防范一些通过修改全局偏移表（GOT）进行的攻击，但防护程度不如 Full RELRO 。
- **Stack: No canary found**：Stack Canary 是一种栈保护机制，向栈中插入随机值（canary 值），函数返回时检查其是否被修改来判断栈是否遭受缓冲区溢出攻击。这里表示该文件未启用 Stack Canary 保护 。
- **NX: NX enabled**：NX（No - eXecute）即不可执行，使栈、堆等内存区域不可执行，阻止攻击者在这些区域植入并执行恶意代码，这里已启用 。
- **PIE: PIE enabled**：PIE（Position - Independent Executable）即地址无关可执行文件，让程序每次加载地址随机化，增加攻击者利用固定地址进行攻击的难度，这里已启用 。
- **Stripped: No** ：表示该文件没有被 strip（去除符号表等调试信息），保留了符号表等信息，便于调试和分析。

后我们把文件拖入IDA（64位）进行查看（IDA自行网上搜索下载安装）

拖入后，弹出来的提示一直按确定即可（其他也基本一致，后不赘述）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743298970788-86bf60c6-e2d4-41b3-a898-b5e1d4f037f1.png)然后我们就来到这样的位置

先找到左边的main函数，双击进入

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743299039609-51959fba-a2c5-493e-9858-a083ddb19cb4.png)

先显示出的是汇编代码，我们按F5转换为伪代码C语言，便于我们查看（转换成C语言后，可以按Tab键回到汇编代码）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743299149387-93d236cb-8557-40ff-bfa1-2b5ecb0ad267.png)

直接给出system("/bin/sh");

权限就直接给你了，因此我们啥也不用操作，直接与其进行连接即可

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743299411914-d37976bb-75db-4b64-bc50-494072f9c9c1.png)

先用vim命令创建一个新文件夹（vim后面的文件名自己喜欢起啥就起啥，这里用题目名字来起）

（vim：打开文件，若没有该文件就创建一个新文件并用输入名字作为文件名）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743299488774-1bd262ef-035b-445c-acc1-8a761ab761b9.png)

进入文件后，按i/a进入编辑模式，然后输入以下代码

（这里每行开头的"r"可用其他字母代替，比如第二和第三行代码的第一个r，跟在其后面的代码就不能改，但是，得前后保持一致（就是一改都要改），否则会出错）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743297724598-19e1342b-ed1a-42e7-8fcc-3c86e354c140.png)

完毕，按左上角的Esc退出编辑模式，后按:wq，保存并退出文件

（:wq，保存并退出，:q，不保存并退出，若有无法退出的情况，在后面加个!即可。eg:":wq!"）

（注：若有错误，可直接:q退出再进入一次即可，不用自己慢慢删）

代码如下

```
#导入pwntools模块：
from pwn import *

#和靶机进行连接：
r = remote("ip",端口)

#获取靶机交互式终端：
r.interactive()
```

IP和端口按实际输入

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743298267616-fc481924-1b4a-4216-95a3-58a704efe1bb.png)

给权限

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1743298305710-05d08dfb-b983-4f70-82d7-a6fd5e83f089.png)

运行该脚本

然后ls（后面步骤与方法一，一致，不再赘述）

# 更新：

于2025.3.30