---
title: "适用于 Linux 的 Azure N 系列驱动程序安装 | Microsoft Docs"
description: "如何为 Azure 中运行 Linux 的 N 系列 VM 安装 NVIDIA GPU 驱动程序"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 421e594f7bd4df1bc1c5faedc2c8bfab0540ca61
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>在运行 Linux 的 N 系列 VM 上安装 NVIDIA GPU 驱动程序

若要利用运行 Linux 的 Azure N 系列 VM 的 GPU 功能，请安装支持的 NVIDIA 图形驱动程序。 部署 N 系列 VM 后，本文提供了驱动程序安装步骤。 针对 [Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 也提供了驱动程序安装信息。


有关 N 系列 VM 规格、存储容量和磁盘详细信息，请参阅 [GPU Linux VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 



[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-for-nc-ncv2-and-nd-vms"></a>安装适用于 NC、NCv2 和 ND VM 的 CUDA 驱动程序

从 NVIDIA CUDA 工具包在 Linux NC VM 上安装 NVIDIA 驱动程序的步骤如下。 

C 和 C++ 开发人员可以选择安装完整的工具包来生成 GPU 加速应用程序。 有关详细信息，请参阅 [CUDA 安装指南](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)。


> [!NOTE]
> 在本文发布时，此处提供的 CUDA 驱动程序下载链接是最新的。 有关最新 CUDA 驱动程序，请访问 [NVIDIA](https://developer.nvidia.com/cuda-zone) 网站。
>

若要安装 CUDA 工具包，请建立到每个虚拟机的 SSH 连接。 若要验证系统是否具有支持 CUDA 的 GPU，请运行以下命令：

```bash
lspci | grep -i NVIDIA
```
会看到类似于以下示例（显示 NVIDIA Tesla K80 卡）的输出：

![lspci 命令输出](./media/n-series-driver-setup/lspci.png)

然后，运行特定于分发的安装命令。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. 下载并安装 CUDA 驱动程序。
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_9.1.85-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  安装可能需要几分钟。

2. 若要安装完整的 CUDA 工具包，请键入：

  ```bash
  sudo apt-get install cuda
  ```

3. 重新启动 VM，并继续验证安装。

#### <a name="cuda-driver-updates"></a>CUDA 驱动程序更新

在部署后，建议定期更新 CUDA 驱动程序。

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux-73-or-74"></a>CentOS 或 Red Hat Enterprise Linux 7.3 或 7.4

1. 更新内核。

  ```
  sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
  sudo reboot

2. Install the latest Linux Integration Services for Hyper-V.

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
 
  tar xvzf lis-rpms-4.2.3-5.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. 重新连接到 VM 并使用以下命令继续安装：

  ```bash
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.1.85-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  安装可能需要几分钟。 

4. 若要安装完整的 CUDA 工具包，请键入：

  ```bash
  sudo yum install cuda
  ```

5. 重新启动 VM，并继续验证安装。


### <a name="verify-driver-installation"></a>验证驱动程序安装


要查询 GPU 设备状态，请建立到 VM 的 SSH 连接，并运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。 

如果安装了驱动程序，将看到如下输出。 请注意，除非当前正在 VM 上运行 GPU 工作负荷，否则 GPU-Util 将显示 0%。 驱动程序版本和 GPU 详细信息可能与所示的内容不同。

![NVIDIA 设备状态](./media/n-series-driver-setup/smi.png)


## <a name="rdma-network-connectivity"></a>RDMA 网络连接

可以在支持 RDMA 的 N 系列 VM（如同一可用性集中部署的 NC24r）上启用 RDMA 网络连接。 对于使用 Intel MPI 5.x 或更高版本运行的应用程序，RDMA 网络支持消息传递接口 (MPI) 流量。 其他要求如下：

### <a name="distributions"></a>分发

从 Azure Marketplace 部署 N 系列 VM 用于支持 RDMA 连接中映像 RDMA N 系列 VM:
  
* **Ubuntu 16.04 LTS** - 在 VM 上配置 RDMA 驱动程序，并注册 Intel 下载 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

> [!NOTE]
> 基于 CentOS 的 HPC 映像当前建议不要用于 N 系列 VM 上的 RDMA 连接。 最新支持 NVIDIA GPU 的 CentOS 7.4 内核不支持 RDMA。
> 


## <a name="install-grid-drivers-for-nv-vms"></a>安装适用于 NV VM 的 GRID 驱动程序

若要在 NV VM 上安装 NVIDIA GRID 驱动程序，请通过 SSH 连接到每个 VM，并执行 Linux 发行版的步骤。 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. 运行 `lspci` 命令。 验证 NVIDIA M60 卡是否显示为 PCI 设备。

2. 安装更新。

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. 禁用 Nouveau 内核驱动程序，该驱动程序与 NVIDIA 驱动程序不兼容。 （只能在 NV VM 上使用 NVIDIA 驱动程序。）若要执行此操作，请在 `/etc/modprobe.d ` 中创建包含以下内容的名为 `nouveau.conf` 的文件：

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. 重新启动 VM，并重新连接。 退出 X 服务器：

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. 下载并安装 GRID 驱动程序：

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
  ``` 

6. 当系统询问你是否要运行 nvidia-xconfig 实用程序以更新 X 配置文件时，请选择“是”。

7. 完成安装后，将 /etc/nvidia/gridd.conf.template 复制到位于 /etc/nvidia/ 的新文件 gridd.conf

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. 将下列内容添加到 `/etc/nvidia/gridd.conf`：
 
  ```
  IgnoreSP=TRUE
  ```
9. 重新启动 VM，并继续验证安装。


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS 或 Red Hat Enterprise Linux 

1. 更新内核和 DKMS。
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. 禁用 Nouveau 内核驱动程序，该驱动程序与 NVIDIA 驱动程序不兼容。 （只能在 NV VM 上使用 NVIDIA 驱动程序。）若要执行此操作，请在 `/etc/modprobe.d ` 中创建包含以下内容的名为 `nouveau.conf` 的文件：

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. 重新启动 VM、重新进行连接并安装适用于 Hyper-V 的最新 Linux 集成服务：
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz

  tar xvzf lis-rpms-4.2.3-5.tar.gz

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. 重新连接到 VM 并运行 `lspci` 命令。 验证 NVIDIA M60 卡是否显示为 PCI 设备。
 
5. 下载并安装 GRID 驱动程序：

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
  ``` 
6. 当系统询问你是否要运行 nvidia-xconfig 实用程序以更新 X 配置文件时，请选择“是”。

7. 完成安装后，将 /etc/nvidia/gridd.conf.template 复制到位于 /etc/nvidia/ 的新文件 gridd.conf
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. 将下列内容添加到 `/etc/nvidia/gridd.conf`：
 
  ```
  IgnoreSP=TRUE
  ```
9. 重新启动 VM，并继续验证安装。

### <a name="verify-driver-installation"></a>验证驱动程序安装


要查询 GPU 设备状态，请建立到 VM 的 SSH 连接，并运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。 

如果安装了驱动程序，将看到如下输出。 请注意，除非当前正在 VM 上运行 GPU 工作负荷，否则 GPU-Util 将显示 0%。 驱动程序版本和 GPU 详细信息可能与所示的内容不同。

![NVIDIA 设备状态](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 服务器
如果需要使用 X11 服务器远程连接到 NV VM，建议使用 [x11vnc](http://www.karlrunge.com/x11vnc/)，因为它允许硬件图形加速。 必须将 M60 设备的 BusID 手动添加到 xconfig 文件（Ubuntu 16.04 LTS 上的 `etc/X11/xorg.conf`、CentOS 7.3 或 Red Hat Enterprise Server 7.3 上的 `/etc/X11/XF86config`）。 添加 `"Device"` 节，如下所示：
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
此外，更新 `"Screen"` 节以使用此设备。
 
通过运行以下命令可找到 BusID

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
重新分配或重新启动 VM 后，BusID 可能会更改。 因此，重新启动 VM 后，可能需要使用脚本更新 X11 配置中的 BusID。 例如：

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

通过在 `/etc/rc.d/rc3.d` 中为此文件创建一个条目，可以在启动时以 root 身份调用此文件。

## <a name="troubleshooting"></a>故障排除

* 可以使用 `nvidia-smi` 设置持久性模式，以便在需要查询卡时该命令的输出更快。 若要设置持久性模式，请执行 `nvidia-smi -pm 1`。 请注意，如果重启 VM，此模式设置将消失。 你可以始终将该模式设置编写为在启动时执行。


## <a name="next-steps"></a>后续步骤

* 若要捕获安装了 NVIDIA 驱动程序的 Linux VM 映像，请参阅[如何通用化和捕获 Linux 虚拟机](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
