# WIN10_KVM
Linux系统上安装基于KVM的WIN10虚拟机
> [教程参考来源](https://tommy.net.cn/2017/01/06/install-windows-under-ubuntu-and-kvm/)

> 本文档的安装环境是Ubuntu 16.04 LTS

* [开始安装](#1)
    * [安装KVM](#2)
    * [创建桥接网络](#3)
    * [创建虚拟主机](#4)
        * [准备安装镜像](#4.1)
        * [准备windows驱动](#4.2)
        * [建立虚拟主机](#4.3)
    * [安装windows 10](#5)

# <p id="1">为什么要安装KVM虚拟机</p>

开始之前先介绍一下什么叫[KVM](https://zh.wikipedia.org/wiki/%E5%9F%BA%E4%BA%8E%E5%86%85%E6%A0%B8%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA):
    + KVM是开源软件，全程为kernel-based virtual machine(基于内核的虚拟机器)。
    + 是X86架构且硬件加速虚拟化技术(如**intel VT**或**AMD-V**)的Linux全虚拟化解决方案。
    + 它包含一个为处理器提供底层虚拟化 可加载的核心模块kvm.ko（kvm-intel.ko或kvm-AMD.ko）。
    + KVM还需要一个经过修改的QEMU软件（qemu-kvm），作为虚拟机上层控制和界面。
    + KVM能在不改变linux或windows镜像的情况下同时运行多个虚拟机，（它的意思是多个虚拟机使用同一镜像）并为每一个虚拟机配置个性化硬件环境（网卡、磁盘、图形适配器……）同时KVM还能够使用[]ksm](https://en.wikipedia.org/wiki/Kernel_same-page_merging)技术帮助宿主服务器节约内存。
    + 在主流的Linux内核，如2.6.20以上的内核均已集成了KVM核心。

