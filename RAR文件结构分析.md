# RAR文件结构分析

## RAR文件的特点

RAR通常情况比ZIP压缩比高，但压缩/解压缩速度较慢。
分卷压缩：压缩后分割为多个文件。
固实压缩：把要压缩的视为同一个文件以加大压缩比，代价是取用包中任何文件需解压整个压缩包。
恢复记录：加入冗余数据用于修复，在压缩包本身损坏但恢复记录够多时可对损坏压缩包进行恢复。
加密：RAR 2.0使用AES-128-cbc，（rar5.0以后为AES-256CBC）。之前RAR的加密算法为私有。目前均未被直接攻破（至少没有公开），没有密码时只有暴力破解。

## RAR文件码流分析

> 下面的文件格式分析是基于RAR4.x，并不是RAR5.0
> RAR 5.0签名和RAR4.x的签名不一样
> RAR 5.0签名由8个字节组成:
>
> 0x52 0x61 0x72 0x21 0x1A 0x07  0x01 0x00
>
> 比较一下
>
> RAR 4.x 签名由7字节组成：
>
> 0x52 0x61 0x72 0x21 0x1A 0x07 0x00

![image-20211117090728517](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211117090728517.png)

一个**RAR4.x**压缩文件由若干个可变长度的快组成

**常见块类型如下:**

```
标记块：HEAD_TYPE=0x72
压缩文件头：HEAD_TYPE=0x73
文件头：HEAD_TYPE=0x74
旧风格的注释头：HEAD_TYPE=0x75
旧风格的用户身份信息：HEAD_TYPE=0x76
旧风格的子块：HEAD_TYPE=0x77
旧风格的恢复记录：HEAD_TYPE=0X78
旧风格的用户身份信息：HEAD_TYPE=0X79
子块：HEAD_TYPE=0x7A
最后的结束块：HEAD_TYPE=0x7B
```

## 标记块(MARK_HEAD)

第一块为标记块（MARK_HEAD），其数据为：

```
52 61 72 21 1A 07 00 // 左边是低字节 ，右边是高字节，这是RAR4.x的签名
//签名即标志着该文件是由支持rar4.x版本的软件压缩而成，如果使用支持5.0版本的压缩软件压缩，其签名可能会不同

52 61 72 21 1A 07 01 00 //左边是低字节，右边是高字节，这是RAR5.x的签名
```

### 字节说明

52 61 为头部校验和(HEAD_CRC)，占两个字节，每一块均从HEAD_CRC开始，所有的RAR都以52 61 开头

72 为块类型(HEAD_TYPE)占一个字节，所有文件都如此

21 1A 为块标记(HEAD_FLAGS)占两个字节

07 00 标识块大小，即 52 61 72 21 1A 07 00 （标记块）共占7个字节

## 压缩文件头(MAIN_HEAD)

第二块为压缩文件头(MAIN_HEAD)，和标记块一样

![在这里插入图片描述](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NsYW1pbmdfRA==,size_16,color_FFFFFF,t_70)

头类型是`0x73`表示压缩文件头块
位标记为`0x0000` 没有位被置为1 ，如果块头被加密，则位标记应该为：0x8000
文件头大小为`0x0D00`，由上图可以看出这个压缩文件头块占13个字节

## 文件头(FILE_HEAD)

RAR文件格式及其各字段含义请参考： [RAR 5.0 archive format description.](https://www.rarlab.com/technote.htm#filehead)

接下来用rar文件来讲一下文件头部分的码流分析。

![在这里插入图片描述](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/20200509145606294.png)

16进制软件使用winhex，hex值左边是低位，右边是高位，比如`52 61` 实际上就是`0x6152`

```
D5 56 ：HEAD_CRC，2字节，也就是文件头部分的crc校验值
74 ：HEAD_TYPE，1字节，块类型，74表示块类型是文件头
20 90 ：HEAD_FLAGS，2字节，位标记，这块在资料上没找到对应的数值，不知道20 90代表什么意思。
2D 00 ：HEAD_SIZE，2字节，文件头的全部大小（包含文件名和注释）
10 00 00 00 ：PACK_SIZE，4字节，已压缩文件大小
10 00 00 00 ：UNP_SIZE，4字节，未压缩文件大小
02：HOST_OS，1字节，保存压缩文件使用的操作系统，02代表windows
C7 88 67 36：FILE_CRC，4字节，文件的CRC值
6D BB 4E 4B ：FTIME，4字节，MS DOS 标准格式的日期和时间
1D：UNP_VER，1字节，解压文件所需要的最低RAR版本
30：METHOD，1字节，压缩方式，这里是存储压缩
08 00 ：NAME_SIZE，2字节，表示文件名大小，这里文件名大小是8字节（flag.txt）
20 00 00 00 ：ATTR，4字节，表示文件属性这里是txt文件
66 6C 61 67 2E 74 78 74：FILE_NAME（文件名） ，NAME_SIZE字节大小，这里NAME_SIZE大小为8
再往后是txt文件内容，一直到第六行 65 结束，下面是另一个文件块的开始

这个块中存在两个crc值，一个是文件头块中从块类型到文件名这38个字节的校验，后一个则是压缩包中所包含文件的crc校验，解压时，会计算解压后生成文件的crc值，如果等于这里的crc，则解压完成，如果不同，则报错中断。
```

## 结尾块

![在这里插入图片描述](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/2020050915335642.png)

> 这个结尾块和标记块字节大小和分析方法是一样的。
> C4 3D ：HEAD_CRC，2字节，从HEAD_TYPE到HEAD_SIZE的crc校验值
> 7B ：HEAD_TYPE，1字节，表示该块是结尾块
> 00 40 HEAD_FALGS ，2字节，位标记
> 07 00 ：HEAD_SIZE，2字节，块大小
> 与标记块类似的是，结尾块也是一个固定字节串的块，依次是 C4 3D 7B 00 40 07 00

