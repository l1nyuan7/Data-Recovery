# Winhex数据恢复

## MBR

### MBR作用及结构

##### 引导程序

![image-20211115185728437](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115185728437.png)

##### 磁盘签名

![image-20211115185823470](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115185823470.png)

##### 保留字节

![image-20211115185910606](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115185910606.png)

##### 分区表

前十六个字节代表一个分区

**分区表一**

![image-20211115190024469](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115190024469.png)

**一共三个分区表**

![image-20211115190053388](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115190053388.png)

##### 结束标志

![image-20211115190210199](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115190210199.png)

### MBR字节分析

##### 字节表

![image-20211115190733710](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115190733710.png)

##### 分析

![image-20211115191745981](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115191745981.png)

##### 使用工具查看

![image-20211115192031684](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115192031684.png)

### MBR修复

##### 损坏的MBR表

没有任何数据，全是空白

![image-20211115193300484](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115193300484.png)

##### 开始修复

###### 第一步

新建一个好的磁盘，将好的磁盘的MBR表复制到损坏的磁盘MBR表中

选中好的MBR表，ctrl c

![image-20211115193520082](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115193520082.png)

将鼠标放到损坏的MBR的起始位置，ctrl B 粘贴

![image-20211115193609783](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115193609783.png)

###### 第二步

修改磁盘签名，因为他的签名是从别的磁盘复制过来的，签名不能一样，随便修改，不一样即可

将磁盘签名全部改为A

![image-20211115193755501](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115193755501.png)

保留字节不需要变

引导标志 一般也不需要修改 除非是C盘，一般修复很少碰到C盘

###### 第三步

先将分区表清空

分区类型表

![image-20211115194102636](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115194102636.png)

修改分区类型表，查看导航栏，观察是格式的，按着表格来修改

![image-20211115194339320](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115194339320.png)

![image-20211115194404865](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115194404865.png)

###### 第四步

修改起始扇区号

点击这里

![image-20211115194451094](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115194451094.png)

观察左下角，提示扇区号是 2048

![image-20211115194525878](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115194525878.png)

从右往左选中四个字节，在数据解释器中输入 2048 回车

![image-20211115195038192](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115195038192.png)

###### 第五步

修改分区总扇区数

总分区大小 - 起始扇区号2048 = 分区总扇区数

![image-20211115200152517](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115200152517.png)

![image-20211115200306942](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115200306942.png)

###### 总结：

新建一个磁盘

将新建的磁盘的MBR复制到损坏的磁盘的MBR中

修改被破坏磁盘MBR的磁盘签名

找到该磁盘第一个分区的起始扇区

从该起始扇区得知文件系统、起始扇区号、分区大小

将这些数据填入MBR的分区表中

保存，在磁盘管理处进行刷新



## FAT16

### FAT16介绍和结构

#### 介绍

FAT16 磁盘 最大只有2G

#### 结构

###### 从FAT16MBR跳转到它的DBR

从右往左选中FAT16的起始扇区号，观察数据解释器，发现是128，将鼠标放到起始位置，向下跳转128即可到达FAT16 DBR

![image-20211115201003259](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115201003259.png)

DBR是什么？假如磁盘是一本书，那么DBR就是书的目录

###### FAT16结构

> MBR
>
> DBR及其保留扇区：DBR是DOS引导记录，也称为操作系统引导记录
>
> FAT1：FAT是文件分配表。FAT16有两份FAT表，FAT1是第一份，是主要FAT
>
> FAT2：FAT2是FAT16的第二份分配表，是FAT1的备份，也称为备份FTA
>
> FDT：是文件目录表，一般占用大小为32个扇区
>
> DATA：DATA是数据区

###### 如何发现是FAT16？

前连个字节是 EB 3C 就是FAT16

###### 跳转到FAT1

在DBR首个字节处，输入保留扇区数8 跳转到FTA表1

![image-20211115202309362](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115202309362.png)

###### 跳转到FAT2

得到FAT1表的大小为256，在FTA1处向下跳转256个扇区到达FAT2

![image-20211115202435366](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115202435366.png)

![image-20211115202536288](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115202536288.png)

###### 跳转到根目录

接着跳转256个扇区

![image-20211115202614533](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115202614533.png)

如果磁盘中有文件的话，可以在右边ASCII值中看到文件名和后缀名

![image-20211115202905622](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115202905622.png)

###### 跳转到DATA区

向下跳转32个扇区

![image-20211115203109664](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115203109664.png)

**注意：数据区都是从2号簇开始记录的**

###### 总结：

**MBR----隐藏扇区数----DBR----保留扇区数----FAT1----FAT表大小----FAT2----FAT表大小----FDT----32----DATA**

### FAT16DBR分析

###### 参考表

![image-20211115203959738](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115203959738.png)

### FAT16文件提取

###### 参考表

![image-20211115213247017](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211115213247017.png)

###### 进入FDT目录

**MBR----128----DBR----8----FAT1----256----FAT2----256---FDT目录**

表分析

![image-20211116081904446](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116081904446.png)

###### 提取JPG文件

记录文件起始簇号：4号簇

文件大小为：111,762字节

现在我们是在FDT目录下，需要向下跳转32扇区到DATA数据区

提取：

计算要跳转的扇区：因为data数据区是2号簇，jpg文件是4号簇，4 - 2 = 2 * 每簇扇区数64 == 需要跳转的扇区数

4 - 2 = 2 * 64 = 128

向下跳转128扇区 到达图片位置

![image-20211116082523826](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116082523826.png)

在起始位置右键，选择起始位置，向下跳转图片大小个字节，右键选择尾部位置，右键粘贴至新文件

###### 提取文件夹中的内容

记录文件起始簇号为：10号簇

文件大小为：文件夹没有大小

10 - 2 = 8 * 64 = 512 向下跳转512扇区 到达文件夹位置

![image-20211116083214279](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116083214279.png)

跟提取文件一样，记录簇号和文件大小，计算跳转扇区大小，回到DATA数据区向下跳转

###### 提取TXT文件

记录文件起始簇号为：11号簇

文件大小为：19字节

计算：

11 - 2 = 9 * 64 = 576 向下跳转576个扇区到达txt文件位置 

### FAT16文件扇区与误格式化现象

###### 文件彻底扇区前分析

**FDT目录**

![image-20211116083825884](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116083825884.png)

**FAT表**

![image-20211116083909052](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116083909052.png)

###### 文件彻底删除后分析

**FAT表**

![image-20211116084308755](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116084308755.png)

**FDT目录**

![image-20211116084358293](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116084358293.png)

###### 现象

FDT文件目录项首字节变成从4A变成E5

文件底层内容没有改变，还可以根据簇号计算出跳转扇区继续提取文件



###### 分区格式化前分析

FDT

![image-20211116085029051](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116085029051.png)

文件夹内容

![image-20211116085253769](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116085253769.png)

FAT表

![image-20211116085317617](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116085317617.png)

###### 分区格式化后分析

![image-20211116085426137](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116085426137.png)

FDT目录中没有文件了

![image-20211116085458178](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116085458178.png)

计算出文件夹要跳转的扇区号，跳转发现还存在着文件

![image-20211116085739467](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116085739467.png)

###### 现象

格式化后FAT表被清空，FDT文件目录项也被清空了，文件中的文件目录项还存在，底层文件也存在

### FAT16DBR修复

修改FAT16DBR时需要修改的数据有隐藏扇区数，分区总大小，保留扇区数，FAT表大小，每簇扇区数

###### 第一步

新建一个FAT16文件系统将其DBR复制黏贴到被破坏的FAT16DBR处

###### 第二步

计算保留扇区

从DBR处向下搜索 "F8FFFF" ，到达FAT1

FAT1所在的扇区号 - DBR所在扇区号 = 保留扇区数大小

观察左下角发现：FAT1位于136号扇区 ，DBR:位于128号扇区，136 - 128 = 8 得到保留扇区数大小

###### 第三步

计算FAT表大小

从FAT1向下搜索 "F8FFFF"，到达FAT2，观察左下角，发现FAT2位于392号扇区

FTA2所在的扇区号 - FAT1所在的扇区号 = FAT表大小

392 - 136 = 256 FAT表为256

###### 第四步

查看隐藏扇区数大小与分区大小

从MBR的分区表处查看分区的隐藏扇区数大小与分区大小

分区大小 2,091,008

![image-20211116093749697](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116093749697.png)

隐藏扇区数 128

![image-20211116093837655](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116093837655.png)



###### 第五步

计算每簇扇区数

数据区总扇区数 / 数据区总簇数 = 每簇扇区数

数据区总扇区数 = 分区大小 - 2 * FAT表大小 - 保留扇区数大小 - FDT占用的扇区数

数据区总簇数 = 512 * FAT表大小 /  2

```txt
数据区总簇数 = 512 * 256 / 2 == 65536
2,091,008 - 256
数据区总扇区数 = 2,091,008 - 2*256 - 8 - 32  = 2,090,456

每簇扇区数 = 2,090,456 / 65536 = 31.8978271484375   四舍五入 32
```

> 每簇山区数  = 32
>
> 隐藏扇区数 = 128
>
> 保留扇区数 = 8
>
> 分区总大小 = 2,091,008
>
> FAT表大小 = 256

依次将数据填入对于的数据块



## FAT32

### FAT32介绍与结构

#### 介绍

最大只有32G

#### 结构

> MBR       跳转128扇区到达DBR
>
> DBR及其保留扇区
>
> FAT1																																											FAT32结构
>
> FAT2
>
> DATA

##### 从MBR跳转到DBR

向下跳转 2048扇区

##### 从DBR跳转到FAT表1

向下跳转8214扇区

##### 从FAT表1跳转到FAT表2

向下跳转16373扇区

<hr></hr>

### FAT32DBR分析

无法判断是不是FAT32的情况下，向下搜索十六进制字符串 EB58

##### 参考表

![image-20211116104855332](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116104855332.png)

##### 分析

![image-20211116110151482](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116110151482.png)

### FAT32文件提取

##### FAT分析

![image-20211116110855546](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116110855546.png)

##### 跳转到DATA区

> MBR----隐藏扇区数----2048---DBR----保留扇区数----8214----FAT1----16373----FAT2----16373----DATA

![image-20211116111255377](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116111255377.png)

##### 短文件目录项

![image-20211116111332050](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116111332050.png)

大致结构跟FAT16文件差不多一样  文件名 后缀名 文件大小 都是一样的方式标识的

##### 提取TXT文件

计算公式

> （文件起始簇号 - 2） * 每簇扇区数 = 文件所在的簇号

文件簇号：36号簇

文件大小：15字节

（36 - 2） * 32 = 1,088

早DATA扇区 直接向下跳转1088扇区 跳转到txt文件所在扇区

##### 提取JPG文件

文件簇号：5号簇

文件大小：111,762字节

5 - 2 = 3 * 32 = 96

直接在DATA区里，向下跳转96个扇区，选中起始位置，向下跳转文件大小个字节 右键选中尾部，右键，粘贴至新文件

##### 提取文件夹中的JPG文件

文件簇号：14号簇

文件大小：文件夹没有大小

跟提取其他普通文件一样的方式

### FAT32文件删除与格式化现象

##### 删除前

FAT表

![image-20211116112907468](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116112907468.png)

根目录

![image-20211116113418826](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116113418826.png)

##### 删除后

FAT表

![image-20211116113522267](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116113522267.png)

根目录

![image-20211116113621963](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116113621963.png)

文件夹中的内容

![image-20211116114152122](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116114152122.png)

##### 现象

底层数据依然不变

文件名首字节变成 E5

##### 格式化前

##### 格式化后

FAT表

![image-20211116114436678](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116114436678.png)

DATA区

![image-20211116114631355](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116114631355.png)

##### 现象

FAT表被清空 DATA区没有目录名

但起始底层数据依旧没有发生变化，输入没有格式化之前文件所在的簇号 可以直接跳转到文件所在位置



### FAT32DBR修复

##### 第一种

利用DBR备份

在DBR首字节处，向下跳转6个扇区到DBR备份处，ctrl  c 复制 回到DBR搜首字节 ctrl b 粘贴

##### 第二种

修改FAT32DBR时，需要修改的数据有 隐藏扇区数、分区总大小、保留扇区数、FAT表大小、每簇扇区数

###### 第一步

新建一个FAT32文件系统将其DBR复制粘贴到被破坏FAT32的DBR处

###### 第二步

计算保留扇区数

从DBR处向下搜索 "F8FFFF0F"，到达FAT1

FAT1所在的扇区号 - DBR所在的扇区号 = 保留扇区数大小

![image-20211116120644196](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116120644196.png)

DBR所在扇区是 2048，FAT1所在扇区号是10262

![image-20211116120746730](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116120746730.png)

> DBR的绝对扇区数 2048
> FAT1所在扇区号 10262
> 保留扇区数大小 ：10262 - 2048  = 8214

###### 第三步	

计算FAT表大小

从FAT表1向下搜索 "F8FFFF0F"，到达FAT2

FTA2所在的扇区号 - FAT1所在扇区号 = FAT表大小

FAT2绝对扇区号：

![image-20211116121007826](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116121007826.png)

> FAT2绝对扇区号：26635
> FTA表大小：26635 - 10262 = 16373

###### 第四步

查看隐藏扇区数大小与分区大小

从MBR的分区表处查看分区的隐藏扇区数大小与分区大小

> 隐藏扇区数：2048
>
> 分区大小：67,102,720

![image-20211116132754219](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116132754219.png)

![image-20211116132850718](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116132850718.png)

###### 第五步

计算每簇扇区数

数据区总扇区数  /  数据区总簇数 = 每簇扇区数

数据区总扇区数 = 分区大小 - 2 *FAT表大小 - 保留扇区数大小  67,061,758

数据区总簇数 = 512 * FAT表大小 / 4   2,095,744

> 67,061,758 / 2,095,744 = 31.99902182709339   32

###### 第六步

将计算出得数值 填入DBR中对应的位置



## NTFS

### NTFS介绍与结构

#### 介绍

#### 结构

### NTFS DBR分析

#### 参考表

![image-20211116135534620](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116135534620.png)

#### 分析

![image-20211116145317668](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116145317668.png)

### NTFS属性分析

#### 文件表

![image-20211116150249744](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116150249744.png)

![image-20211116150632312](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116150632312.png)

#### 属性

![image-20211116150320609](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116150320609.png)

![image-20211116150758372](C:\Users\Administrator\Desktop\笔记\数据恢复\https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116150758372.png)

### NTFS文件提取

#### 第一步

跳转到MFT起始簇号

MFT起始簇号为：786432

![image-20211116153715842](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116153715842.png)

计算公式：

> 786432 * 8 == 6,291,456

向下跳转6,291,456扇区 到MFT元文件

![image-20211116153847616](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116153847616.png)

从MFT向下跳转10个扇区，到达根目录元文件

![image-20211116153933999](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116153933999.png)

两个扇区为一个元文件，这里十个扇区就是五个元文件

#### 第二步

向下查找A0属性

![image-20211116154643688](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116154643688.png)

接下来

![image-20211116154723240](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116154723240.png)

> 36 * 8 =  288

回到DBR扇区，向下跳转288个扇区到达根目录

![image-20211116154923662](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116154923662.png)

在目录中找到文件

![image-20211116155102154](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116155102154.png)

#### 第三步

找到文件位置

![image-20211116155236167](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116155236167.png)

> 123.txt 元文件为 43 号

![image-20211116155507434](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116155507434.png)

> JPG元文件为 39号

#### 第四步

##### 提取TXT文件

回到DBR扇区，向下跳转6,291,456个扇区到达MFT元文件目录区

> 123.txt 元文件 为 43 号
>
> 公式：43 * 2 = 86

向下跳转86个扇区，到达TXT文件位置

![image-20211116155832429](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116155832429.png)

找到80属性，发现在第9个字节处 为00 代表常驻属性，标识文件体积小，可以直接存放在MFT记录中，这种属性就成为常租属性

![image-20211116160152762](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116160152762.png)

选中这一块，将文件提取出来，FFFF是结束标志

![image-20211116160428914](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116160428914.png)

##### 提取JPG文件

> JPG元文件为 39号
>
> 公式：39 * 2 = 78

从MFT向下跳转78个扇区，来到jpg的文件位置

![image-20211116160714169](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116160714169.png)

找到80属性，查看第九个字节，发现是非常租属性（文件无法全部都存入MFT）

![image-20211116160929031](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116160929031.png)

计算文件所在簇

![image-20211116161257939](C:\Users\Administrator\Desktop\笔记\数据恢复\https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116161257939.png)

观察数据解释器，起始簇号为6263

![image-20211116161322920](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116161322920.png)

> 6263 * 8（每簇扇区数） = 50104

在 21 前面就是文件的大小 为 111,762字节

![image-20211116161504995](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116161504995.png)

回到DBR，向下跳转50104个扇区来到图片所在位置

![image-20211116161644305](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116161644305.png)

选块起始位置，向下跳转图片打消你个字节，选中尾部 右键粘贴至新文件

也可以直接通过搜索 字符串 搜索 123.txt 快速定位文件位置



#### 总结

定位BDR

定位$MFT

跳转10个扇区到达6号元文件

5号文件处找到A0属性中的DATA RUN

从DBR处向下跳转

找到我们需要提取的文件名称

向上找到10字节，向前找到第一个字节就知道是几号元文件

来到MFT向下跳转几号元文件  * 2 =多少号扇区

查看是否为常租属性，是  直接提取

不是 找到 DATA RUN 来得知文件的起始簇号



### NTFS文件夹中的文件提取

##### 第一步

###### 计算文件夹所在位置

跳转到MFT

向下跳转10个扇区来到目录区

找到A0属性，查看起始簇号为36

![image-20211116165757705](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116165757705.png)

36 * 8 = 288

回到DBR向下跳转 288 个扇区来到根目录，在根目录中找到文件夹

![image-20211116165931250](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116165931250.png)

在123文件夹上面找到 01 00 ，在左边第一个字节 为 元文件所在 号: 44

![image-20211116170150225](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116170150225.png)

找到picture元文件所在号为 45号

![image-20211116170329795](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116170329795.png)



##### 第二步

回到MFT处，向下跳转文件所在号 * 2 个扇区

txt所在簇号为 44 *2==88 ，向下跳转88个扇区，来到123文件夹所在位置

![image-20211116170615716](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116170615716.png)

###### 提取文件夹中的txt文件

**计算文件所在号**

123.txt文件 72号

![image-20211116171024835](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116171024835.png)

456.txt文件 43号

![image-20211116170947537](C:\Users\Administrator\Desktop\笔记\数据恢复\https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116170947537.png)

当前是39个扇区，456.txt 为43号扇区，向下跳转4个扇区到达456.txt文件位置

![image-20211116171302571](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116171302571.png)

寻找80属性，第九个字节发现是常驻属性，直接提取即可







#### 提前picture文件夹中的图片



来到MFT，已经知道picture 为 45号元文件，45*2=90，直接向下跳转90个扇区来到picture文件位置

###### 提取jpg

向上找，找到01 00 的位置，为48号文件

![image-20211116172252747](C:\Users\Administrator\Desktop\笔记\数据恢复\https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116172252747.png)

回到MFT，48 * 2 = 96

向下跳转96个扇区，来到1.jpg位置

找到80属性，第九个字节为01 非常驻属性

![image-20211116173107656](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116173107656.png)

记录起始簇号为6420

![image-20211116173206620](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116173206620.png)

文件大小：111,762

![image-20211116173236937](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116173236937.png)

回到DBR，计算跳转扇区为：6420 * 8 = 51,360 ，向下跳转，来到1.jpg文件位置

![image-20211116173417269](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116173417269.png)

选中起始位置，向下跳转文件大小个字节，选中尾部，右键，复制黏贴为新文件

2.jpg为49号

![image-20211116172643817](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116172643817.png)

同上



### NTFS文件删除与格式化现象

#### 误删除

##### 删除前

**1.doc元文件 位于 53号元文件**

回到MFT 53 * 2 = 106 向下跳转106个扇区来到doc位置

![image-20211116191631013](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116191631013.png)

查看文件80属性，属于非常租属性，下面是文件位置内容

![image-20211116191558674](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116191558674.png)

**999文件夹 位于 45 号元文件**

文件夹位于 90号扇区

![image-20211116191935439](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116191935439.png)

2.jpg 位于 49号元文件

![image-20211116192127930](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116192127930.png)

位于：6,448个簇  * 8 = 51,584扇区

文件大小：111,762

**卢本伟.jpg 位于 52号元文件**

起始簇号：6504 * 8 = 52,032扇区

问价大小：111,762

DBR向下跳转

![image-20211116192524994](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116192524994.png)

##### 删除后

##### 现象

底层数据依然没变，文件存放地址一样



##### 误格式化前

png文件位于 39号元文件

MFT处，向下跳转 39 * 2 = 78个扇区

![image-20211116193648242](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116193648242.png)

起始簇号：6,263簇 * 8 = 50,104扇区

文件大小：203,705

##### 误格式化后

MFT跳转过后，没有发现文件

![image-20211116193955184](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116193955184.png)

##### 现象

底层文件位置依然存在 也一样

### NTFSDBR修复

#### 第一种方法

向下搜索分区大小 - 1个扇区，到达DBR备份扇区，复制到被删除的DBR上即可

#### 第二种方法

手工修复

第一步

新建一个虚拟磁盘，建立一个任意大小NTFS分区，将该新建分区的DBR拷贝到我们需要修复的DBR中然后保存对其中一些重要数据进行修改

第二步

保存DBR后，需要修改的DBR关键数据有

1、隐藏扇区数

2、分区总扇区数

3、$MFT起始簇号

4、$MFTMirr起始簇号

5、每簇扇区数

6、文件记录大小

7、索引缓冲的大小描述

![image-20211116195157182](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116195157182.png)

![image-20211116195433142](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116195433142.png)

## ExFAT

### ExFAT介绍与结构

#### 介绍

#### 结构

![image-20211116200733579](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116200733579.png)

##### 跳转到DBR

起始扇区向下跳转2048个扇区到达DBR

##### 跳转到FAT表

从DBR向下跳转2048扇区到达FAT表

##### 跳转到簇位图

从FAT表向下跳转4096个扇区到达簇位图

### ExFAT32DBR字节分析

ExFAT文件系统的DBR是由6部分组成

跳转指令

OEM代号

保留区

BPB参数

引导程序

结束标志

![image-20211116201931418](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116201931418.png)

![image-20211116202658359](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116202658359.png)

### ExFAT文件提取

结构分析

![image-20211116204315513](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116204315513.png)

##### 提取1.doc

![image-20211116204631778](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116204631778.png)

起始簇号：18

文件大小：9,728

跳转到簇位图，向下跳转 18 - 2 * 256 = 4096个扇区，到达1.doc文件位置

##### 提取文件夹中的文件夹中的文件夹的内容

起始簇号：7

跳转 到达456文件夹

![image-20211116205405422](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116205405422.png)

465文件夹起始簇号：10  到达789目录

![image-20211116205456198](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116205456198.png)

789文件夹起始簇号：11   到达文件位置

![image-20211116205601936](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116205601936.png)

提取1.doc

起始簇号：12

文件大小：9,728

回到簇位图：12 - 2 * 256 = 2560 向下跳转扇区 到达文件位置

### ExFATDBR修复

![image-20211116210559885](https://github.com/l1nyuan7/Data-Recovery/blob/main/image/image-20211116210559885.png)

总簇数计算：

分区总扇区数：83,879,936 - 6144 = 83,873,792

分区的总簇数：83,873,792 - 256 = 327,632



FAT表大小

327,632 + 2 / 256 = 2,559.609375    2560

![image-20211116212445770](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20211116212445770.png)
