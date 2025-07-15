---
title: Arch Linux安装初体验"
date: 2025-07-15T19:51:37+08:00
draft: false
tags: ["OS"]
---

什么嘛，Arch Linux哪里劝退了（）  

观前提醒，遇到问题先查手册，本篇专栏仅进行踩坑记录  

## 从镜像到启动

配置键盘布局和字体，验证引导模式，验证互联网连接，更新系统时间，然后是磁盘分区。  
磁盘分区就是第一个坑，一定要给root和efi划够，哪怕你的swap只有8G也没事，把它俩先照顾好，要不然后面装固件和内核的时候直接失败。  
挂载对应分区（一定不要挂错了！挂错了后面装固件和内核还是莫名失败！）。  
获取中国大陆的镜像站列表，取消清华源和科大源的注释，使用pacstrap安装启动必需的软件包，使用pacman安装vim和dhcpcd（至少要把vim装了，然后dhcpcd在后面网络配置的时候有大用，如果嫌麻烦还要安装NetworkManager）。  
fstab，arch-chroot，设置时区（后面可以通过网络授时把漂移问题解决），本地化，配置主机名，设置root密码（这一部分抄手册即可）。  
安装grub。好，劲爆尾杀。最难看懂的部分来了。这里在稍微skim了一下GRUB的wiki页面之后我在GPT-4o上使用了如下prompt：  
```
https://wiki.archlinuxcn.org/wiki/GRUB
现在我在安装archlinux的最后一步了，手册让我安装grub，我现在有/dev/sda1作为/efi，/dev/sda2作为swap，/dev/sda3作为/，接下来我该怎么做呢？
```

获得回答后尝试如下的步骤：
```shell
pacman -S grub efibootmgr
mount | grep /efi
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
exit
unmount -R /mnt
swapoff /dev/sda2
reboot
```
启动系统完成。

## 从启动到桌面

创建非特权账户，准备安装一些自己惯用的命令行工具，安装后发现pacman的所有镜像源都不通了，ping所有的网站（包括1.1.1.1）都不通，着手修复网络问题。  
先尝试了回到安装环境安装，但是arch-chroot命令直接无法获得，启动system-networkd和system-resolved，然后
```shell
ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
```
依然不行。大概率不是软件问题，尝试寻找硬件问题。 ``ip addr`` 的结果显示我的enp0s3网卡处于DOWN状态，使用 ``ip link set enp0s3 up`` ，编辑 ``/etc/systemd/network/20-wired.network`` 如下：  
```toml
[Match]
Name=enp0s3

[Network]
DHCP=yes
```
然后重启服务：  
```shell
systemctl restart systemd-networkd
```
网络恢复  
尝试安装kde桌面，如下
```shell
# 更新系统
sudo pacman -Syu
# 安装kde plasma
sudo pacman -S plasma kde-applications
# 安装SDDM用于图形界面的登录
sudo pacman -S sddm
sudo systemctl enable sddm
# 安装Xorg图形系统
sudo pacman -S xorg-server xorg-apps
# 安装显卡驱动（AMD版，勿照抄）
sudo pacman -S mesa xf86-video-amdgpu vulkan-radeon
# 重启进入kde
reboot
```

进入桌面，但......  

## 从桌面到环境

锁屏界面输入密码，发现无法正常进入桌面，闪烁一下，然后回到锁屏页面。从头尝试C-M-F{2-6}来进入命令行界面，回到root，建立对应的home文件夹，如下：  
```shell
sudo mkdir -p /home/vzstless
sudo chown vzstless:users /home/vzstless
```
重新启动，进入桌面。先用root身份打开终端，下载sudo，但是sudo在普通用户下无法正常使用，回到root，再设置环境变量：  
```shell
# ~/.bashrc
export EDITOR=`which vim`
```
使用visudo命令，编辑：  
```
vzstless ALL=(ALL:ALL)ALL
```

再次回到普通用户，正常使用sudo，结束。  
还好，没有想象中那么难，下次试试gentoo（bushi）  