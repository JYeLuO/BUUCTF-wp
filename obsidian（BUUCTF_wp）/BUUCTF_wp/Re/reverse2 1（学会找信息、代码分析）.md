# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744808164799-37808bc3-0c8f-43b6-9058-bfcc11fcf57c.png)

# 做法

下载压缩包，解压，把解压后的文件拖进Exeinfo PE进行分析

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744808248606-3960307a-3739-4c3e-822c-4b22578fb609.png)

64位，无壳

扔进IDA（64位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744808328687-12eb34e7-ad2b-4a10-b020-e3ff042c1158.png)

老规矩，从得出flag那里开始往上看

```
if ( !strcmp(&flag, &s2) )
    result = puts("this is the right flag!");
```

如果&flag里的内容等于&s2里的内容，我们的flag就是对的

继续往上

```
  printf("input the flag:", argv);
  __isoc99_scanf("%20s", &s2);
```

不难发现，s2里的内容是用户输入的内容

```
for ( i = 0; i <= strlen(&flag); ++i )
    {
      if ( *(&flag + i) == 105 || *(&flag + i) == 114 )
        *(&flag + i) = 49;
    }
```

一个for循环，里面有关于&flag的内容

1. 将循环变量 `i` 赋值为 0 。
2. 每次循环时，调用 `strlen` 函数计算 `flag` 字符数组的长度（`&flag` 传递的是数组地址） 。
3. 检查 `i` 是否小于等于计算得到的字符串长度，若成立则进入循环体，否则结束循环。
4. 在循环体中，通过指针运算 `*(&flag + i)` 访问 `flag` 数组中索引为 `i` 的字符，判断该字符的 ASCII 码值是否为 105（字符 `'i'`）或者 114（字符 `'r'`）。若满足条件，则将该字符替换为 ASCII 码值为 49 的字符（字符 `'1'`）。
5. 循环体执行完毕后，将 `i` 的值自增 1，回到步骤 3 继续判断循环条件，重复上述过程直至循环结束。

看到数字，我们先按r把它变成字符

```
if ( *(&flag + i) == 'i' || *(&flag + i) == 'r' )
        *(&flag + i) = '1';
```

要把i/r转换成1

然后我们双击flag进去看看有没有东西

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744809194416-73ad9720-bbb0-4f9a-a0bd-0c3dd0cea2b4.png)

有个疑似flag格式的字符串

复制下来，补全格式，然后把i/r转换成1提交看看

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744809277629-c8304c72-f8ca-4bf8-a073-1dff94e013c4.png)

成功解出

# 更新

于2025.4.16