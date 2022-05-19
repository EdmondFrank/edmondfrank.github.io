---
layout: post
title: "数据恢复利器-Testdisk"
date: 2018-01-13 16:23:37 +0800
comments: true
categories: 技术杂谈
---
# 数据恢复利器-Testdisk

今天我要给大家介绍的主角是**Testdisk**

首先，**Testdisk**是一个强大的免费的跨平台的数据恢复工具，根据它的[官网](https://www.cgsecurity.org/wiki/TestDisk)上的简介，这款软件主要被设计用于恢复丢失的分区以及修复那些由于人为或者病毒等原因导致分区表错误而无法启动系统的问题。

除此之外，Testdisk更多的特性大家可以参考官方列出的功能列表：
>TestDisk can
Fix partition table, recover deleted partition
Recover FAT32 boot sector from its backup
Rebuild FAT12/FAT16/FAT32 boot sector
Fix FAT tables
Rebuild NTFS boot sector
Recover NTFS boot sector from its backup
Fix MFT using MFT mirror
Locate ext2/ext3/ext4 Backup SuperBlock
Undelete files from FAT, exFAT, NTFS and ext2 filesystem
Copy files from deleted FAT, exFAT, NTFS and ext2/ext3/ext4 partitions.

对于Testdisk的强大之处，肯定是不容质疑的，对此笔者在之前自己的系统修复过程切身体会过Testdisk的实用与强大之处（在笔者的旧硬盘全盘分区表丢失的情况之下，使用Testdisk成功恢复了大部分的分区，并能成功启动系统。看到系统还能成功开机那一刻别提多激动了！）

## 跨平台
Testdisk不仅强大而且还能够跨平台，跨平台，跨平台使用（重要的事情说三遍）并且还支持多种文件系统。

简单的介绍就到这里了！！

下面我们就用一个实际的例子来演示一下
Testdisk的具体使用方法：

本例子摘取自：[Testdisk 操作指南](https://www.cgsecurity.org/wiki/Testdisk_%E6%93%8D%E4%BD%9C%E6%8C%87%E5%8D%97)
PS：毕竟为了演示而认为制造一些错误也是有一定风险的，所以为了方便和安全起见笔者这里摘抄一份别人的例子啦，反正也是为了向大家安利下这个软件。

前提条件:

+ TestDisk 需要用管理员权限来运行。

使用 TestDisk 的重点:

+ 用 方向键 和 上一页/下一页 按键在TestDisk 中导航。
+ 然后, 按Enter 键确认你的选择.
+ 若要返回前一页或者退出TestDisk, 按 q (Quit) 键。
+ 若要在 TestDisk 下保存修改,按 y (Yes) 或者 Enter 键来确认
+ 如果确实要把分区信息写入主引导记录（MBR），应该选择 "Write" 选项并按 Enter 键。



## 运行Testdisk
如果 TestDisk 还没有被安装, 可以从这里下载 [TestDisk Download](https://www.cgsecurity.org/wiki/TestDisk_Download)。然后解压缩这个归档文件，包括子文件夹。

###　一、新建日志
+ 选择 Create 来让 Testdisk 新建一个日志文件（ log file） ，里边包含了一些技术信息和消息, 除非你要往一个日志里追加信息 log 或者 你从只读存储器里执行 TestDisk 而且必须在别的地方建立日志 log。
+ 选择 None 如果你不想让过程中的细节和消息记录到日志文件里 log file (比如当 Testdisk 是在只读位置执行的时候，这很有用).
+ 按 Enter 键以继续.
![enter image description here](https://www.cgsecurity.org/mw/images/Create_log.png)

###　二、选择磁盘
所有的硬盘都应该能被TestDisk检测到并且辅以正确的大小列出来：

+ 用 上/下 键 来选择丢失分区的硬盘。
+ 按 Enter 键继续。
![enter image description here](https://www.cgsecurity.org/mw/images/Select_disk_update.png)


###三、选择分区表类型
TestDisk 会显示分区表类型。

+ 选择正确的分区表类型 - 在 TestDisk 自动检测分区表类型之后，一般预设的那个值是正确的。
+ 按 Enter 键继续。
![enter image description here](https://www.cgsecurity.org/mw/images/Partition_table_type.png)

### 四、查看当前分区状态

TestDisk 显示这个菜单的时候 (参见 [TestDisk Menu Items](https://www.cgsecurity.org/wiki/Running_TestDisk)).
+ 用预设的“analyze”（分析）选项来检查当前的分区结构并搜索丢失的分区。
+ 分析过程中按 Enter 键继续。
第一个分区显示了两次，它指向了一个毁坏的分区或一个无效的分区表入口。
+ 无效的 NTFS boot 指向了一个错误的 NTFS boot 扇区, 所以这是一个损坏的文件系统。
在扩展分区中，只有一个逻辑分区(分区标签为2)可用。 有一个逻辑分区不见了。
+ 选 Quick Search （快速搜索）来继续。
然后，当前的结构就会被列出来。 接下来就可以在当前的分区结构中检查丢失或错误的分区了。

![enter image description here](https://www.cgsecurity.org/mw/images/Analyse.png)

### 五、快速搜索分区
在 Quick Search（快速搜索）的过程中, TestDisk 找到了两个分区，包括那个不见的逻辑分区（标签为 Partition 3 ）
![enter image description here](https://www.cgsecurity.org/mw/images/First_results.png)
+ 高亮这个分区并按 p 来列出文件 (若要返回前一页，请按 q ).
+ 这里所有的目录和文件都正确列出来了。
+ 按 Enter 键继续。
+ ![enter image description here](https://www.cgsecurity.org/mw/images/First_results.png)
+

### 六、保存分区表
+ 当全部分区都可用的时候 并且数据已正确列出,应该选 Write 菜单项保存分区结构. 菜单项 Extd Part gives you the opportunity to decide if the extended partition will use all available disk space or only the required (minimal) space.

+ 当一个分区,第一个,仍然找不到, 高亮菜单项 深度搜索 (没有自动进行的时候) ，按 Enter 键继续.

![enter image description here](https://www.cgsecurity.org/mw/images/Search_menu.png)
（经过笔者的几次实验和朋友的反馈，其实到了这一步已经能够解决80%以上的问题了！）

所以，有关Testdisk更加深入的功能和其他详细用法大家可以前往这个[中文版的官方指南](https://www.cgsecurity.org/wiki/Testdisk_%E6%93%8D%E4%BD%9C%E6%8C%87%E5%8D%97)在这里笔者就不赘述了！
