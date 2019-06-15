---
title: Aktivera InifinBand med SR-IOV - Azure-datorer | Microsoft Docs
description: Lär dig hur du aktiverar InfiniBand med SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 81acb804ed2ebb9e88bc7d8281a7fa52359d4455
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66810089"
---
# <a name="enable-infiniband-with-sr-iov"></a>Aktivera InfiniBand med SR-IOV


Det enklaste och rekommenderade sättet att konfigurera en anpassad VM-avbildning med InfiniBand (IB) är att lägga till InfiniBandDriverLinux eller InfiniBandDriverWindows VM-tillägget för din distribution.
Lär dig hur du använder dessa VM-tillägg med [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

För att manuellt konfigurera InfiniBand på SR-IOV-aktiverade virtuella datorer (för närvarande HB och HC-serien), Följ stegen nedan. Anvisningarna gäller endast för RHEL/CentOS. För Ubuntu (16.04 och 18.04) och SLES (12 SP4 och 15) fungerar drivrutinerna som bra. För Ubuntu 


## <a name="manually-install-ofed"></a>Installera manuellt OFED

Installera de senaste drivrutinerna för MLNX_OFED för ConnectX-5 från [Mellanox](http://www.mellanox.com/page/products_dyn?product_family=26).

För RHEL/CentOS (exemplet nedan för 7.6):
```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Windows, hämtar och installerar WinOF-2-drivrutiner för ConnectX-5 från [Mellanox](http://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="assign-an-ip-address"></a>Tilldela en IP-adress

Tilldela en IP-adress till gränssnittet ib0, med hjälp av antingen:

- Manuellt tilldela IP-adressen till gränssnittet ib0 (som rot).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

ELLER

- Använd WALinuxAgent för att tilldela IP-adress och göra den beständig.

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

Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) på Azure.
