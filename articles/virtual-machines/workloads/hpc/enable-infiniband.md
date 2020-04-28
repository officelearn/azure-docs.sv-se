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
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "74196771"
---
# <a name="enable-infiniband-with-sr-iov"></a>Aktivera InfiniBand med SR-IOV

Azure NC, ND och H-serien med virtuella datorer backas upp av ett dedikerat InfiniBand-nätverk. Alla RDMA-aktiverade storlekar kan dra nytta av det nätverket med hjälp av Intel MPI. En del VM-serien har expanderat stöd för alla MPI-implementeringar och RDMA-verb via SR-IOV. Virtuella datorer med RDMA-kapacitet är [GPU-optimerade](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) och HPC-virtuella datorer med [höga prestanda](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) .

## <a name="choose-your-installation-path"></a>Välj installations Sök väg

För att komma igång är det enklaste alternativet att använda en förkonfigurerad plattforms avbildning för InfiniBand, där det finns:

- **HPC IaaS VM** – för att komma igång med IaaS-VM: ar för HPC är den enklaste lösningen att använda den [CentOS-HPC 7,6 VM OS-avbildningen](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), som redan är konfigurerad med InfiniBand. Eftersom den här avbildningen redan har kon figurer ATS med InfiniBand behöver du inte konfigurera den manuellt. För kompatibla Windows-versioner, se [Windows RDMA-kompatibla instanser](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **GPU IaaS VM** – inga plattforms avbildningar för närvarande är förkonfigurerade för GPU-optimerade virtuella datorer, med undantag för [CentOS VM 7,6-avbildning av virtuella datorer](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Om du vill konfigurera en anpassad avbildning med InfiniBand läser du [Installera MELLANOX ofed manuellt](#manually-install-mellanox-ofed).

Om du använder en anpassad virtuell dator avbildning eller en [GPU-optimerad](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) virtuell dator bör du konfigurera den med InfiniBand genom att lägga till InfiniBandDriverLinux eller InfiniBandDriverWindows VM-tillägget i distributionen. Lär dig hur du använder dessa VM-tillägg med [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Installera Mellanox OFED manuellt

Följ stegen nedan om du vill konfigurera InfiniBand manuellt med SR-IOV. Exemplet i de här stegen visar syntax för RHEL/CentOS, men stegen är allmänna och kan användas för alla kompatibla operativ system som Ubuntu (16,04, 18,04 19,04) och SLES (12 SP4 och 15). Inbox-drivrutinerna fungerar också, men driv rutinerna för Mellanox OpenFabrics innehåller fler funktioner.

Mer information om de distributioner som stöds för Mellanox-drivrutinen finns i de senaste [Mellanox OpenFabrics-drivrutinerna](https://www.mellanox.com/page/products_dyn?product_family=26). Mer information om driv rutinen för Mellanox OpenFabrics finns i [användar handboken för Mellanox](https://docs.mellanox.com/category/mlnxofedib).

Se följande exempel för hur du konfigurerar InfiniBand på Linux:

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

För Windows hämtar och installerar du [Mellanox-ofed för Windows-drivrutiner](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Aktivera IP över InfiniBand

Använd följande kommandon för att aktivera IP över InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
