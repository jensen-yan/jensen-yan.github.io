---
layout: post
title:  "ubuntu 双屏幕闪烁问题解决"
date:   2023-07-12 
---



## bug 描述

在前几天使用命令升级系统后

```
sudo apt update; sudo apt-fast upgrade

dkms status                                              
digimend/11, 5.15.0-71-generic, x86_64: installed
digimend/11, 5.15.0-76-generic, x86_64: installed (original_module exists)  
```

内核从5.15.0-71 升级到5.15.0-76。

升级过程中出现了黑屏的现象，观察机器还开着，但是两个显示器都没有输入信息，无奈强制关机重启。

重启后，我有两个显示屏

1. 左边的：24寸，HDMI接口，1080p 显示器A, 1.0缩放
2. 右边的：27寸，DP接口，4k 显示器B， 1.5缩放

显示器A正常，显示器B 会间隔2s左右黑屏一次，就很难受



## 尝试方法1-回退内核版本

我其实还有5.15.0-75 内核，回退回去任然有问题

切换到5.15.0-71内核，不闪烁了，但是有如下几个问题

1. 无法使用perf, 更新后移除了-71内核的linux-tools， 暂时可以使用nix 安装的perf解决

   ```bash
   $: sudo apt install linux-tools-5.15.0-71-generic           
   [sudo] yanyue 的密码： 
   正在读取软件包列表... 完成
   正在分析软件包的依赖关系树... 完成
   正在读取状态信息... 完成                 
   E: 无法定位软件包 linux-tools-5.15.0-71-generic
   E: 无法按照 glob ‘linux-tools-5.15.0-71-generic’ 找到任何软件包 
   
   $ apt serch linux-tools-5.15.0-
   最早只支持linux-tools-5.15.0-72
   
   $ nix-env -iA nixpkgs.linuxPackages.perf
   
   $ which perf
   /home/yanyue/.nix-profile/bin/perf  
   ```

2. NVIDIA显卡驱动支持异常，导致浏览网页很卡顿

   ```bash
   $ nvidia-smi
   nvidia-smi Failed to initialize NVML: Driver/library version mismatch
   
   $ sudo dmesg
   NVRM: API mismatch: the client has the version 535.54.03, but NVRM: this kernel module has the version 530.41.03. Please NVRM: make sure that this kernel module and all NVIDIA driver NVRM: components have the same version.
   ```

   这里显示我内核编译的显卡驱动driver 是530，但是目前安装的显卡驱动是535

   ```bash
   $ ubuntu-drivers devices
   == /sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0 ==
   modalias : pci:v000010DEd00002187sv000010B0sd0000139Dbc03sc00i00
   vendor   : NVIDIA Corporation
   model    : TU116 [GeForce GTX 1650 SUPER]
   driver   : nvidia-driver-535-open - distro non-free
   driver   : nvidia-driver-470 - distro non-free
   driver   : nvidia-driver-450-server - distro non-free
   driver   : nvidia-driver-525-server - distro non-free
   driver   : nvidia-driver-525 - distro non-free
   driver   : nvidia-driver-535 - distro non-free recommended
   driver   : nvidia-driver-535-server - distro non-free
   driver   : nvidia-driver-535-server-open - distro non-free
   driver   : nvidia-driver-525-open - distro non-free
   driver   : nvidia-driver-470-server - distro non-free
   driver   : xserver-xorg-video-nouveau - distro free builtin
   # 能看出推荐535驱动，也可以尝试525驱动
   
   $ sudo ubuntu-drivers autoinstall               
   All the available drivers are already installed. 
   
   $ sudo apt install nvidia-driver-530 
   $ sudo apt install nvidia-driver-535
   ```
   
   

## 尝试方法2-回退显卡驱动

   卸载535驱动，使用525驱动(目前已经没有530版本的驱动了)

```bash
$ sudo apt install nvidia-driver-525 
```

但问题任然存在



## 尝试方法3-使用自带核显

我的cpu是intel 12700k, GPU是nvidia 1650super

可以使用核显来带动4k屏幕应该没有问题

但是发现我的核显输出只有2个DP接口，而我的显示器需要1个HDMI,  一个DP，

暂时放弃这个方法



## 尝试方法4-使用Wayland

```bash
$ sudo vim /etc/gdm3/custom.conf
# 修改成true
WaylandEnable=true

$ sudo systemctl restart gdm3
```

一年前尝试使用过wayland, 但是至今任然不支持两个屏幕使用不同整数倍的缩放，如果屏幕B使用1.5倍缩放，屏幕A也默认使用1.5倍缩放，wayland 还需要继续更新啊！此外wayland 似乎和linux 搜狗输入法用的fcitx5不太兼容。

没办法，还是暂时用回X11吧



## 尝试方法5-使用高版本内核

之前有同学给我推荐过[xanmod内核](https://xanmod.org/)

按照官网安装后，使用的6.4 版本的内核

但是任然对nvidia驱动支持不好, 显示缺少一些系统固件

```bash
$ nvidia-smi
NVIDIA-SMI has failed because it could not communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.

$ sudo update-initramfs -u
update-initramfs: Generating /boot/initrd.img-6.4.3-x64v3-xanmod1
W: Possible missing firmware /lib/firmware/i915/dg2_huc_gsc.bin for module i915
W: Possible missing firmware /lib/firmware/i915/adlp_dmc.bin for module i915
W: Possible missing firmware /lib/firmware/i915/mtl_dmc.bin for module i915  
```



## 解决方法

最后还是使用ubuntu稳定版本的最新内核，调整分辨率和缩放后发现：

使用1.0 整数缩放，而不使用1.5版本的缩放，就不会有闪烁的情况了。

有点无奈，因为这样我左右两个屏幕不是很一致，左边字体较大，右边字体较小。

不过折腾了一上午，暂时先这样能用吧，希望下个版本ubuntu小版本升级能修复这个问题吧。

