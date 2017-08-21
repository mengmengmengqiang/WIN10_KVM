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

开始之前先介绍一下什么是[KVM](https://zh.wikipedia.org/wiki/%E5%9F%BA%E4%BA%8E%E5%86%85%E6%A0%B8%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA)：
+ KVM是开源软件，全程为kernel-based virtual machine(基于内核的虚拟机器)。
+ 是X86架构且硬件加速虚拟化技术(如**intel VT**或**AMD-V**)的Linux全虚拟化解决方案。
+ 它包含一个为处理器提供底层虚拟化 可加载的核心模块kvm.ko（kvm-intel.ko或kvm-AMD.ko）。
+ KVM还需要一个经过修改的QEMU软件（qemu-kvm），作为虚拟机上层控制和界面。
+ KVM能在不改变linux或windows镜像的情况下同时运行多个虚拟机，（它的意思是多个虚拟机使用同一镜像）并为每一个虚拟机配置个性化硬件环境（网卡、磁盘、图形适配器……）同时KVM还能够使用[ksm](https://en.wikipedia.org/wiki/Kernel_same-page_merging)技术帮助宿主服务器节约内存。
+ 在主流的Linux内核，如2.6.20以上的内核均已集成了KVM核心。

然而由于各种各样的原因我们需要在Linux下安装windows的虚拟机又不想使用VMware之类的软件，或者在服务器下面无法使用这些软件。那么基于KVM的qemu管理的虚拟机就成了一个好选择。

## <p id="2">安装KVM</p>

    #首先安装KVM相关包文件，如果没有安装图形桌面则使用**--no-install-recommends**参数，这样不会安装与图形相关的库。
    sudo apt-get install -y         \
            --no-install-recommends \
            qemu-kvm                \
            qemu-utils              \
            libvirt-bin             \
            virtinst                \
            cpu-checker             /

    #验证是否安装成功
    sudo kvm-ok

    #如果安装成功则应显示
    INFO: /dev/kvm exists
    KVM acceleration can be used

## <p id="3">创建桥接网络</p>
> [网桥配置参考链接](https://github.com/mengmengmengqiang/network#桥接网络ubuntu1604)

> KVM自己会创建一个virbr0的桥接网络，但是这个网络是NAT在物理网卡上的，这样如果宿主机在远程服务器上，就无法在本地的电脑上远程访问虚拟机的VNC端口，也就无法进行下一步的安装工作。所以桥接网络属于可选配置。

    #使用命令行创建桥接网络

    #创建一个虚拟以太网桥接口br0
    sudo brctl addbr br0

    #网桥所在网络里只有一台路由器，则关闭生成树协议(STP)
    sudo brctl stp br0 off

    #将机器里可以连接外网的以太网接口附加到br0上
    sudo brctl addif br0 eth0

    #现在原来的以太网物理接口变成了网桥上的逻辑端口，变成了逻辑网桥的一部分，不需要IP地址,将IP地址释放掉(暂时未测试，请不要运行此步骤)
    sudo ifconfig eth0 down
    sudo ifconfig eth0 0.0.0.0 up

    #启用网桥
    sudo ifconfig br0 up

    #给网桥接口分配一个IP地址(与eth0在同一网段且未被使用过)
    sudo ifconfig br0 x.x.x.x

    #查看已有网桥
    sudo brctl show



