# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744969634997-782311cf-b3d1-4f85-88a5-182a5a44bae5.png)

# 做法

下载压缩包，解压，把解压后的文件拖进Exeinfo PE进行分析

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744969556799-2ca91de4-197a-4473-a2dd-b7c29a021e2a.png?x-oss-process=image%2Fformat%2Cwebp%2Fresize%2Cw_796%2Climit_0)

32位，无壳

扔进IDA（32位），找到main，F5反编译

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744972258128-7be1f6bf-d3f5-48c5-844f-a144d61c264c.png)

没啥关键词，Shift+F12也找不到什么有用的点

从上往下分析吧

```
    puts("1 up");
    puts("2 down");
    puts("3 left");
    printf("4 right\n:");
```

选1234都有对应的上下左右选择，是迷宫题吗

```
scanf("%d", &v5);
if ( v5 == 2 )
    {
      ++*(_DWORD *)&v3[25];
    }
    else if ( v5 > 2 )
    {
      if ( v5 == 3 )
      {
        --v4;
      }
      else
      {
        if ( v5 != 4 )
LABEL_13:
          exit(1);
        ++v4;
      }
    }
    else
    {
      if ( v5 != 1 )
        goto LABEL_13;
      --*(_DWORD *)&v3[25];
    }
```

把我们输入的值放进v5，根据我们输入的数字不同，v3或v4会有不同变化，亦或是异常退出

```
      if ( *(_DWORD *)&v3[4 * i + 25] >= 5u )
        exit(1);
    }
    if ( v7[5 * *(_DWORD *)&v3[25] - 41 + v4] == 49 )
      exit(1);
    if ( v7[5 * *(_DWORD *)&v3[25] - 41 + v4] == 35 )
    {
      puts("\nok, the order you enter is the flag!");
```

我们输入的值让v3或v4变化，满足不同公式，系统会异常退出亦或是返回’好的，你输入的顺序就是标志‘

至此，就再无别的信息了

我们尝试双击运行解压后的文件

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744973274823-99bbacb1-7c6a-4b7e-8255-8d5001bc81de.png)

分别输入1234进行测试

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744973310755-c839a8e2-61dc-4a32-b4d0-3c0169fdc1a4.png)

尝试多次后，发现规律——每次弹出来的四个选项中，只有一个是正确答案，且这个正确答案不是固定的，要自己不断慢慢试出来

结合IDA的代码分析，我们要去算那个公式的话，有很多种不同组合方式，而且更繁杂

我们选择直接打开文件一个个测，测出一个正确的就记下来，直到返回\nok, the order you enter is the flag!

最后成果——222441144222，包上flag{}去提交吧！

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744973640945-514f975d-7b89-4f72-a84f-1fad39d4ec29.png)

# 更新

于2025.4.18