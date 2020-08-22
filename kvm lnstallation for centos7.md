## KVM 概述

KVM 是一个 linux 内核模块，允许用户空间程序访问 Interl 和 AMD 处理器的硬件虚拟化功能。使用 KVM 内核模块， VM 作为普通用户空间进行。

KVM 使用 QEMU 进行 I/O 硬件仿正，可以在主机处理器上模拟各种各种用户处理器，并具有良好的性能。

KVM 通过 libvirt API 和libvirt 工具进行管理。libvirt 工具包括 virsh， vir-install 和 virt-clone。

- 主机(Host)：安装 VM 的虚拟管理程序或物理服务器即宿主机
- 虚拟机(VM)：安装在 Host 上的虚拟服务器

## KVM 准备

检查 CPU 是否支持硬件虚拟化

英特尔和AMD都已为其处理器开发了扩展，分别称为Intel VT-x（代号Vanderpool）和AMD-V（代号Pacifica）。要查看处理器是否支持其中之一，可以查看以下命令的输出：

```
~]# grep -E 'svm|vmx' /proc/cpuinfo
```
- vmx 适用于 Intel 处理器
- svm 适用于 AMD 处理器

```
~]# systemctl  stop firewalld
~]# systemctl  disable firewalld
~]# setenforce 0
~]# sed -i 's@enforcing@disabled@g' /etc/selinux/config
```
## KVM 安装

通过 YUM 安装 kvm 基础包和管理工具

```
~]# yum install qemu-kvm libvirt libvirt-python libguestfs-tools virt-install virt-manager bridge-utils
~]# systemctl enable libvirtd && systemctl start libvirtd
```

- qemu-kvm 主要的KVM程序包
- python-virtinst 创建虚拟机所需要的命令行工具和程序库
- virt-manager GUI虚拟机管理工具
- virt-top 虚拟机统计命令
- virt-viewer GUI连接程序，连接到已配置好的虚拟机
- libvirt C语言工具包，提供libvirt服务
- libvirt-client 为虚拟客户机提供的C语言工具包
- virt-install 基于libvirt服务的虚拟机创建命令
- bridge-utils 创建和管理桥接设备的工具

查看KVM模块是否被正确加载

```
~]# lsmod | grep kvm
kvm                   586948  1 kvm_intel
irqbypass              13503  1 kvm
```

## 安装虚拟机

使用 virt-manager 安装虚拟机， 根据图形界面提示一步一步安装即可

```
~]# virt-manager
```
```
~]# virsh list
 Id    名称                         状态
----------------------------------------------------
 2     centos7.0                      running
```

使用 virt-install 安装虚拟机, 使用 vnc 连接上图形界面进行操作

```
~]# qemu-img  create -f qcow2 /opt/centos7.1.qcow2 20G
~]# virt-install --name centos7.1   \
                 --virt-type  kvm \
                 --memory 512,maxmemory=1024 \
                 --vcpus 2 \
                 --cdrom /tmp/CentOS-7-x86_64-Everything-1810.iso  \
                 --os-variant rhel7 --disk /opt/centos7.1.qcow2  \
                 --network default \
                 --graphics vnc,listen=0.0.0.0,port=5910 \
                 --noautoconsole
```
