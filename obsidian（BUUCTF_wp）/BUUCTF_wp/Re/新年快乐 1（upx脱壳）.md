# 题目

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744810818926-0f897e58-f16b-4ca4-8ddf-93ac0162e25a.png)

# 做法

下载压缩包，解压，把解压后的文件拖进Exeinfo PE进行分析

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744810942555-bac25876-797b-4e87-855c-7fe64c579a88.png)

32位，有upx壳，需脱壳（自行下载upx脱壳工具）

返回桌面搜索cmd，以管理员身份运行命令提示符

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744811214317-8f43a596-9ef0-46e0-a662-e20dfe68e074.png)

cd命令切换到你下载的upx脱壳工具（总的，不是某一个文件）存放地址

然后输入代码

```
upx -d 要脱壳的文件地址
```

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744811457043-62de1cba-948b-4925-8cca-9bc7489bc78e.png?x-oss-process=image%2Fformat%2Cwebp)

脱壳成功

再把文件拖入Exeinfo PE

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744811661653-abfd426e-59b7-44ab-8fde-e87d550b8039.png)

已经没有壳了

这时再扔进IDA（32位），找到main，F5反编译（注：这里有俩main，对比一下就很清楚地知道该分析哪个）

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744865154777-5a140e2f-38c5-4d77-84fb-42ac4d11c916.png)![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744865165469-e7080a81-3dbb-4c2e-a150-514fc3dfd2c7.png)

那么好，现在我们开始从关键点从上往下进行分析

```
if ( !strncmp((const char *)&v5, &v4, strlen(&v4)) )
    result = puts("this is true flag!");
```

如果`v5` 的前 `strlen(&v4)` 个字符和 `v4` 相同，就会输出 `"this is true flag!"`

然后再往上看

```
scanf("%s", &v5);
```

v5是用户的输入内容储存地址

因此，我们需要找到v4的内容是什么

```
strcpy(&v4, "HappyNewYear!");
  v5 = 0;
  memset(&v6, 0, 0x1Eu);
```

继续往上看，关键内容就这些了

复制HappyNewYear!到v4...

诶，v4这不找到了嘛

结合上面的分析，我们把v4的内容打包一下就是这题的flag啦

![](https://cdn.nlark.com/yuque/0/2025/png/53467226/1744865799797-316061b8-9e93-4234-a6e3-c704dfe10a1c.png)

# 更新

于2025.4.17