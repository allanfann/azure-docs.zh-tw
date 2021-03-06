---
title: 在 Azure 中建立及上傳 Ubuntu Linux VHD
description: 了解如何建立及上傳包含 Ubuntu Linux 作業系統的 Azure 虛擬硬碟 (VHD)。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 7776e0005facb57d223a1ba1e73d1efa30edec49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60327944"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>準備適用於 Azure 的 Ubuntu 虛擬機器
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>官方 Ubuntu 雲端映像
Ubuntu 現在發佈官方 Azure VHD 提供下載，位於：[https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)。 如果您需要針對 Azure 建置您專用的 Ubuntu 映像，而不使用下面的手動程序，建議您視需要從使用這些已知可運作的 VHD 並加以自訂來開始建置。 最新的映像版本一律可以在下列位置找到︰

* Ubuntu 12.04/Precise︰ [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty︰ [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial︰ [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 18.04/Bionic：[bionic-server-cloudimg-amd64.vhd.zip](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vhd.zip)
* Ubuntu 18.10/Cosmic：[cosmic-server-cloudimg-amd64.vhd.zip](https://cloud-images.ubuntu.com/cosmic/current/cosmic-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>必要條件
本文假設您已將 Ubuntu Linux 作業系統安裝到虛擬硬碟。 可使用多种工具创建 .vhd 文件，如 Hyper-V 等虚拟化解决方案。 有关说明，请参阅 [安装 Hyper-V 角色和配置虚拟机](https://technet.microsoft.com/library/hh846766.aspx)。

**Ubuntu 安裝注意事項**

* 另請參閱 [一般 Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes) ，了解為 Azure 準備 Linux 的更多秘訣。
* Azure 不支持 VHDX 格式，仅支持 **固定大小的 VHD**。  您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet，將磁碟轉換為 VHD 格式。
* 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。 這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。 如果需要，可以在数据磁盘上使用 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 不要在操作系统磁盘上配置交换分区。 您可以設定 Linux 代理程式在暫存資源磁碟上建立交換檔。  您可以在以下步驟中找到與此有關的詳細資訊。
* Azure 上的所有 VHD 必須具有與 1 MB 對應的虛擬大小。 從未經處理的磁碟轉換成 VHD 時，您必須確定未經處理的磁碟大小在轉換前是 1 MB 的倍數。 如需詳細資訊，請參閱 [Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes)。

## <a name="manual-steps"></a>手動步驟
> [!NOTE]
> 在嘗試為 Azure 建立您的自訂 Ubuntu 映像之前，請考慮改為使用來自 [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) 的已預先建置並已測試的映像。
> 
> 

1. 在 Hyper-V 管理員的中間窗格中，選取虛擬機器。

2. 单击“连接”  打开虚拟机窗口。

3. 取代映像中的目前儲存機制，以使用 Ubuntu 的 Azure 儲存機制。 此步驟會隨著 Ubuntu 版本而略有不同。
   
    建議您在編輯 `/etc/apt/sources.list` 之前先進行備份：
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04：
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04：
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04：
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Ubuntu Azure 映像现在遵循*硬件支持* (HWE) 内核要求。 執行下列命令，將作業系統更新為最新的核心：

    Ubuntu 12.04：
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04：
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04：
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    **另請參閱：**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. 修改 Grub 的核心開機程式行，使其額外包含用於 Azure 的核心參數。 若要這樣做，請在文字編輯器中開啟 `/etc/default/grub`，找到名為 `GRUB_CMDLINE_LINUX_DEFAULT` 的變數 (或視需要加入它)，然後進行編輯以使其包含以下參數：
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    儲存並關閉此檔案，然後執行 `sudo update-grub`。 這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 技術支援團隊進行問題偵錯程序。

6. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。  這通常是預設值。

7. 安装 Azure Linux 代理：
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  若已安裝 `NetworkManager` 和 `NetworkManager-gnome` 套件，則 `walinuxagent` 套件可能會將它們移除。

对于 Ubuntu 18.04/18.10，更新 Azure 数据源，编辑 /etc/cloud/cloud.cfg.d/90-azure.cfg，将此代码添加到该文件末尾：

**重要提示：必须严格按照所示添加代码，包括空格。**

```bash
datasource:
   Azure:
     agent_command: [service, walinuxagent, start]
```

1. 執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. 在 Hyper-V 管理器中单击“操作”->“关闭”。 您現在可以將 Linux VHD 上傳至 Azure。

## <a name="references"></a>參考
[Ubuntu 硬體啟用 (HWE) 核心](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>後續步驟
您現在可以開始使用您的 Ubuntu Linux 虛擬硬碟在 Azure 建立新的虛擬機器。 如果您是第一次將 .vhd 檔案上傳至 Azure，請參閱[從自訂磁碟建立 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。

