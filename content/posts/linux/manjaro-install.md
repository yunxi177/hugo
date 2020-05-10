+++
title = "Linux Manjaro 安装指南"
date = 2020-05-05T17:03:42+08:00
tags = ["linux"]
categories = ["linux"]
draft = false
commentId="linux-manjaro-install"
+++

![manjaro](http://pic.artacode.com/manjaro.png)
# Linux 历史
1994 年 3 月，Linux1.0 版正式发布，Marc Ewing 成立 Red Hat 软件公司，成为最著名的 Linux 经销商之一。早期 Linux 的引导管理程序（boot loader）使用 LILO（Linux Loader），早期的 LILO存在着一些难以容忍的缺陷，例如无法识别 1024 柱面以后的硬盘空间，后来的 GRUB（GRand Unified Bootloader）克服这些缺点，具有‘动态搜索内核文件’的功能，可以让用户在引导的时候，自行编辑引导设置系统文件，透过 ext2 或 ext3 文件系统中加载 Linux Kernel（GRUB通过不同的文件系统驱动可以识别几乎所有 Linux 支持的文件系统，因此可以使用很多文件系统来格式化内核文件所在的扇区，并不局限于 ext 文件系统）。

今天由 Linus Torvalds 带领下，众多开发共同参与开发和维护 Linux 内核。理查德·斯托曼领导的自由软件基金会，继续提供大量支持 Linux 内核的GNU组件。一些个人和企业开发的第三方的非GNU组件也提供对Linux 内核的支持，这些第三方组件包括大量的作品，有内核模块和用户应用程序和库等内容。Linux社区或企业都推出一些重要的 Linux 发行版，包括 Linux 内核、GNU 组件、非 GNU 组件，以及其他形式的软件包管理系统软件。
# 什么人适合 linux 系统
1. 对 Linux 保持高敏感度的人（运维人员，后端开发...）
2. 有 Geek 精神喜欢折腾的人。
3. 对 Windows 生厌， 想要尝试新系统的人，不差钱可以上 MAC。

# Linux 发行版之 Manjaro
Manjaro Linux 基于 Arch Linux，但拥有自己独立的软件仓库。Manjaro 的目标是让强大的 Arch 更方便用户使用，Manjaro 使用著名的 Pacman 且可以直接利用 AUR 上的資源。Manjaro 本身使用三个软件仓库：不稳定库，即含有那些不成熟的 Arch 包，这些包与 Arch 源有 1-2 天 的延后；测试库，每周同步一次，包含那些 Arch 不稳定源的包；以及稳定库，包含那些由开发团队确认稳定的软件。  
Manjaro Linux 拥有开箱即用的多媒体支持、成熟的硬件识别软件，并支持多核 CPU。Manjaro 拥有命令行安装器和图形安装器。同时滚动更新也意味着用户无需通过重装系统或系统更新来更新自己的操作系统。软件包管理由 Pacman 处理，未来也计划提供一个 GUI 版本。Manjaro 有 32 位 和 64 位 的版本，且都与 Arch 兼容。可对其进行配置，选择是与使用稳定库的 Arch 同步（默认），或者是与不稳定的Arch 库同步。

Manjaro 软件库由自带的 BoxIt 工具管理，BoxIt 类似git
Manjaro 对显卡驱动的兼容性高，可自主选择安装开源驱动或者闭源驱动。

# 关于滚动发布
Manjaro 采用滚动发布的方式发布版本更新，相对于传统的固定版本发布，使用 manjaro 可以保证系统一直处于最新的状态，而**不需要像固定版本发布一样需要等待一个版本发布周期才能用上最新的系统**。  
由上面的描述我们不难发现，滚动发布具有如下优势:
1. 无需进行系统升级，只需要日常的更新，就可以保证系统一直处在最新状态
2. 所有的软件包都是最新的，无需借助 snaps 和 flatpaks 运行当前软件
  
当然滚动发布也存在自身的劣势，主要劣势如下：
1. 滚动升级是以系统为单位的，因此很难将某个特定软件保持在旧版本。
2. 由于是更新整个系统因而在带宽比较小的情况下更新是一个比较有挑战的事情。
3. 在安装软件前必须更新你的系统，否则会存在系统不一致，存在部分升级的情况

# 滚挂
滚挂就是在滚动更新的过程中出错，导致系统崩溃的一种现象。 manjaro 是基于 arch 的发行版其系统更新是慢于 arch，也正是因为这样 arch 会更早的暴露滚挂的问题，及时的对滚挂的问题进行修复或更新说明。manjaro 站在了 arch 的肩膀上，本身滚挂的情况会更少于 arch，但是在使用的过程中，还是需要对此问题进行注意。  
避免滚挂有如下几种方式：
1. 不要开启 test 库
2. 一般大的更新官网会给出更新指南，因此多关注官网能够避免更新系统出现的一些问题。
3. 经常更新系统，保证系统在一个最新的状态。
4. 备份你的系统，如果一旦出现滚挂，这将是一颗让你系统起死回生的灵丹妙药。

## XFCE
XFCE 是一个轻量级的桌面环境,，它被广泛的运用于各种 UNIX 中，它非常的小巧，运行程序很快，节省系统资源。XFCE 融合了 UNIX 哲学中的“模块化”和“可重用性”这两个极为重要的思想。它包含了很多的组件，而正是这些组件构成了整个 XFCE 的强大。它轻量及的特点，也是拯救家中旧电脑的必备神器。
## KDE
由德国人创立而开发的桌面，据说还受到德国政府的资助。KDE 桌 面默认的主题与布局接近于 Windows Vista，因此 Windows 用户很容易熟悉这个桌面。不过，KDE 桌面具有强大的自定义功能，可以根据自己需要来折腾自己的桌面。
## GNOME
由 GUN 软件计划组织创立而开发的桌面，有自己的一套完整的风格，支持扩展插件与主题更换，同时也为越来越多的触摸设备做出优化。高颜值的 UI 设计，也在这个颜值即正义的时代，迎来了越来越多人的青睐。

# Manjaro ISO 下载地址
[Manjaro 下载地址](https://manjaro.org/download/)  

# WINDOWS 下 U盘启动工具制作（虚拟机安装忽略该章节）
[U盘安装Linux必备软件 Rufus 下载](https://rufus.ie/)
打开下载好的 refus 软件，相关设置如下，注意点了开始之后，选择DD模式写入，ISO 模式写入的启动不了。
![Rufus](http://pic.artacode.com/rufus.png)

![Rufus-check](http://pic.artacode.com/refus-write.png)

# 启动安装
将制作好的 U盘，插入需要安装系统电脑，进入 BIOS 关闭安全启动，保存，重启，按F9，进入启动选项，选择 uefi usb3.0 的启动项

![](http://pic.artacode.com/install-1.png)
关于 driver 选项有连个 free 和 nonfree, free 为社区提供的开源版， nonfree 为 AMD 或 Nvidia 厂商提供的驱动版本，也意味这有更好的性能和更少的 bug,因而建议选择 nofree 选项。  
在所有选项配置完毕后，选择 `Boot: Manjaro x86_64 gnome` 进入安装界面

![launch-install](http://pic.artacode.com/manjaro-launch-install.png)

点击 `Launch install` 启动安装程序

# 选择语言

![选择安装语言](http://pic.artacode.com/manaro-step1.png)

# 选择位置
![选择未知](http://pic.artacode.com/manjaro-install-step2.png)

# 选择键盘排布方式
![选择键盘排布方式](http://pic.artacode.com/manjaro-install-step3.png)


# 分区
## 主分区、扩展分区、逻辑分区 关系

磁盘分区有三种： 主分区，扩展分区，逻辑分区。  
通常情况下，一个硬盘中最多能够分割四个主分区。因为硬盘中分区表的大小只有64Bytes，而分割一个分区就需要利用16Bytes空间来存储这个分区的相关信息。由于这个分区表大小的限制，硬盘之能够分给为四个主分区。如果此时一块硬盘有120个G，而管理员划分了4个主分区，每个主分区的空间为20个G。那么总共才用去了80G的空间。这块硬盘剩余的40G空间就将无法使用。这显然浪费了硬盘的空间。  
为了突破这最多四个主分区的限制，Linux系统引入了扩展分区的概念。即管理员可以把其中一个主分区设置为扩展分区(注意只能够使用一个扩展分区)来进行扩充。而在扩充分区下，又可以建立多个逻辑分区。也就是说，扩展分区是无法直接使用的，必须在细分成逻辑分区才可以用来存储数据。通常情况下，逻辑分区的起始位置及结束位置记录在每个逻辑分区的第一个扇区，这也叫做扩展分区表。在扩展分区下，系统管理员可以根据实际情况建立多个逻辑分区，将一个扩展分区划割成多个区域来使用。  


## 分区说明
- /boot 存放系统启动文件 800M-2G(ext4 文件系统)
- swap 交换空间，交换空间大小与计算机内存内存存在关联。内存在8 G 以下设置  内存容量 * 2，8 G 以上设置和内存同等大小即可。
    - 当内存不够用时，系统会将长时间没有运行的程序的缓存从内存中写入 swap 分区中，并释放该程序所占用的内存给其他程序使用
    - Linux 电源管理有休眠模式，系统会将内存中程序运行状态存储在 swap 中然后设备进入完全断电状态，当下次启动计算机时系统会从 swap 恢复休眠前的计算机运行状态。
- / 根目录， 25G 左右 （etx4 文件系统）
- /home （etx4 文件系统） Linux 系统中用户的家文件，此目录空间越大越好。

## 新建分区表
![新建分区表](http://pic.artacode.com/manjaro-step4-1-new.png)

点击新建分区表，选择主引导记录，点击 OK 完成分区表创建。

## 创建分区

![](http://pic.artacode.com/manjaro-install-step4-0-new.png)

1. 选择空闲空间
2. 点击创建进行分区创建

### 创建 boot 分区
![](http://pic.artacode.com/manjaro-install-step4-boot.png)
### 创建 swap （交换空间）

![](http://pic.artacode.com/manjaro-step4-2.png)

### 创建根分区

![](http://pic.artacode.com/manjaro-step4-3.png)

### 创建 home 分区
![](http://pic.artacode.com/manjaro-step4-4.png)

# 设置用户
![](http://pic.artacode.com/manjaro-step5.png)

# 选择 office 套件安装
建议不安装，安装完系统后可以用 wps for linux 替代
![](http://pic.artacode.com/manjaro-step6.png)

# 安装
摘要部分安装前确认步骤，如果没有需要调整的配置项，点击安装进入系统安装过程，安装完成后重启拔掉 U盘，进入系统就可以了。
![](http://pic.artacode.com/manjaro-step7.png)
安装进度在 95% 左右，有时会出现卡住的情况，是因为国内连接外国网络速度过慢导致的，可以断开网络跳过。

# 安装后配置
## 更换源
```
$ sudo pacman -Syy
$ sudo pacman-mirrors -i -c China -m rank
$ sudo pacman -Syyu
```
使用root权限编辑/etc/pacman.conf增加以下内容
```
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server =https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```
然后执行
```
$ sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring
```

## 安装中文输入法

```
sudo pacman -S fcitx-lilydjwg-git
sudo pacman -S fcitx-sogoupinyin
sudo pacman -S fcitx-im         # 全部安装
```
编辑~/.xprofile文件，在文件末尾增加以下内容
```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

## 安装 中文字体
```
sudo pacman -S ttf-roboto noto-fonts ttf-dejavu
# 文泉驿
sudo pacman -S wqy-bitmapfont wqy-microhei wqy-microhei-lite wqy-zenhei
# 思源字体
sudo pacman -S noto-fonts-cjk adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts
```
## 安装 wps
```
sudo pacman -S wps-office
sudo pacman -S ttf-wps-fonts
```

## AUR助手（以防官方仓库没有想要的软件）
```
sudo pacman -S yay
```

## 安装 chrome 浏览器
```
sudo pacman -S google-chrome
```

# 总结
关于 manjaro 安装已经告一段落，参考上述操作，已经搭建出一个可用的 Linux 系统。值得注意的是，Linux 系统与 Windows 系统不同，在 Linux 中，你可以拥有很高的权限，即便是系统核心文件也是可以进行操作的，因此如果你是刚刚接触 Linux 的用户，养成及时备份的习惯是非常明智的选择。最后， 欢迎入坑，我相信在以后 Linux 使用中你一定是痛并快乐着的，坚持下来，时间会给正确的选择以回报。