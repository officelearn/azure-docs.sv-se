---
title: Aktivera InifinBand med SR-IOV – Azure Virtual Machines | Microsoft Docs
description: Lär dig hur du aktiverar InfiniBand med SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 7218fceae71969f204c6c25ba4793a7c94341693
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858484"
---
# <a name="enable-infiniband-with-sr-iov"></a>Aktivera InfiniBand med SR-IOV

Det enklaste och rekommenderade sättet att komma igång med virtuella IaaS-datorer för HPC är att använda avbildningen CentOS-HPC 7,6 VM OS. Om du använder den anpassade virtuella dator avbildningen är det enklaste sättet att konfigurera den med InfiniBand (IB) att lägga till InfiniBandDriverLinux eller InfiniBandDriverWindows VM-tillägget i distributionen.
Lär dig hur du använder dessa VM-tillägg med [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Följ stegen nedan om du vill konfigurera InfiniBand manuellt på SR-IOV-aktiverade virtuella datorer (för närvarande HB och HC-serien). De här stegen är endast för RHEL/CentOS. För Ubuntu (16,04 och 18,04) och SLES (12 SP4 och 15) fungerar Inbox-drivrutinerna bra.

## <a name="manually-install-ofed"></a>Installera OFED manuellt

Installera de senaste MLNX_OFED-drivrutinerna för ConnectX-5 från [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26).

För RHEL/CentOS (exempel nedan för 7,6):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

För Windows hämtar och installerar du WinOF-2-drivrutinerna för ConnectX-5 från [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ipoib"></a>Aktivera IPoIB

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>Tilldela en IP-adress

Tilldela en IP-adress till ib0-gränssnittet med hjälp av något av följande:

- Tilldela IP-adressen till ib0-gränssnittet manuellt (som rot).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

ELLER

- Använd WALinuxAgent för att tilldela IP-adressen och gör den beständig.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
