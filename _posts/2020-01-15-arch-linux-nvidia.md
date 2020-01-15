---
layout: post
title: Arch Linux NVIDIA 显卡配置小记
categories: Toolbox/ArchLinux
tags: [Arch Linux]
description:
---

曾经因为配置不好 NVIDIA 和 Intel 双显卡而换到别的发行版。现在重新安装发现意外的简单……

可能会有一些冗余配置……

1. 安装软件包：
   ```
   sudo pacman -S nvidia nvidia-settings xorg-xrandr
   ```
2. 复制 `arch.conf` 模版到目标路径：
   ```
   cp /usr/share/systemd/bootctl/arch.conf /boot/loader/entries/arch.conf
   ```
3. 编辑 `arch.conf` ：
   ```
   /boot/loader/entries/arch.conf
   ------------------------------
   options root=/dev/sda2 quiet splash nvidia-drm.modeset=1
   ```
4. 编辑 `/etc/default/grub` ：
   ```
   /etc/default/grub
   -----------------
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvidia-drm.modeset=1"
   ```
5. mkconfig：
   ```
   grub-mkconfig -o /boot/grub/grub.cfg
   ```
6. 编辑 `/etc/mkinitcpio.conf`：
   ```
   /etc/mkinitcpio.conf
   --------------------
   MODULES=(intel_agp i915 nvidia nvidia_modeset nvidia_uvm nvidia_drm)
   ```
7. mkinitcpio：
   ```
   sudo mkinitcpio -P
   ```
8. 创建 `/etc/pacman.d/hooks/nvidia.hook`：
   ```
   [Trigger]
   Operation=Install
   Operation=Upgrade
   Operation=Remove
   Type=Package
   Target=nvidia
   Target=linux

   [Action]
   Description=Update Nvidia module in initcpio
   Depends=mkinitcpio
   When=PostTransaction
   NeedsTargets
   Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
   ```
9. 创建 `/etc/X11/xorg.conf.d/10-nvidia-drm-outputclass.conf`：
   ```
   Section "OutputClass"
       Identifier "intel"
       MatchDriver "i915"
       Driver "modesetting"
   EndSection

   Section "OutputClass"
       Identifier "nvidia"
       MatchDriver "nvidia-drm"
       Driver "nvidia"
       Option "AllowEmptyInitialConfiguration"
       Option "PrimaryGPU" "yes"
       ModulePath "/usr/lib/nvidia/xorg"
       ModulePath "/usr/lib/xorg/modules"
   EndSection
   ```
10. 编辑 `~/.xinitrc`：
    ```
    xrandr --setprovideroutputsource modesetting NVIDIA-0
    xrandr --auto
    ```


## 参考
1. [NVIDIA Optimus - ArchWiki](https://wiki.archlinux.org/index.php/NVIDIA_Optimus)
2. [Archlinux 下 Intel 和 NVIDIA 双显卡 de 折腾笔记](https://blog.megumifox.com/public/2018/11/26/archlinux-下-intel-和-nvidia-双显卡-de-折腾笔记/)
