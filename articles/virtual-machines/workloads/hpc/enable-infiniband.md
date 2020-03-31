---
title: Aktivera InifinBand med SR-IOV - Virtuella Azure-datorer | Microsoft-dokument
description: Läs om hur du aktiverar InfiniBand med SR-IOV.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196771"
---
# <a name="enable-infiniband-with-sr-iov"></a>Aktivera InfiniBand med SR-IOV

Azure NC, ND och H-serien med virtuella datorer stöds alla av ett dedikerat InfiniBand-nätverk. Alla RDMA-aktiverade storlekar kan utnyttja nätverket med Intel MPI. Vissa VM-serier har utökat stödet för alla MPI-implementeringar och RDMA-verb via SR-IOV. RDMA-kompatibla virtuella datorer inkluderar [GPU-optimerade](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) och [HPC-virtuella datorer (High-Performance Compute).](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)

## <a name="choose-your-installation-path"></a>Välj installationssökväg

För att komma igång är det enklaste alternativet att använda en plattformsavbildning förkonfigurerad för InfiniBand, där det finns tillgängligt:

- **HPC IaaS virtuella datorer** – För att komma igång med IaaS virtuella datorer för HPC är den enklaste lösningen att använda [CentOS-HPC 7.6 VM OS-avbildningen](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), som redan är konfigurerad med InfiniBand. Eftersom den här avbildningen redan är konfigurerad med InfiniBand behöver du inte konfigurera den manuellt. Kompatibla Windows-versioner finns i Instanser av [Windows RDMA-kompatibla](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **GPU IaaS virtuella datorer** – Inga plattformsavbildningar är förkonfigurerade för GPU-optimerade virtuella datorer, med undantag för [CentOS-HPC 7.6 VM OS image](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Om du vill konfigurera en anpassad avbildning med InfiniBand finns i [Installera Mellanox OFED manuellt](#manually-install-mellanox-ofed).

Om du använder en anpassad VM-avbildning eller en [GPU-optimerad](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) virtuell dator bör du konfigurera den med InfiniBand genom att lägga till infinibanddriverLinux- eller InfiniBandDriverWindows VM-tillägget i distributionen. Lär dig hur du använder dessa VM-tillägg med [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Installera Mellanox OFED manuellt

Om du vill konfigurera InfiniBand manuellt med SR-IOV gör du följande steg. Exemplet i dessa steg visar syntax för RHEL/CentOS, men stegen är allmänna och kan användas för alla kompatibla operativsystem som Ubuntu (16.04, 18.04 19.04) och SLES (12 SP4 och 15). Inkorgsdrivrutinerna fungerar också, men Mellanox OpenFabrics-drivrutinerna ger fler funktioner.

Mer information om distributioner som stöds för Mellanox-drivrutinen finns i de senaste [Mellanox OpenFabrics-drivrutinerna](https://www.mellanox.com/page/products_dyn?product_family=26). Mer information om Mellanox OpenFabrics-drivrutinen finns i [användarhandboken mellanox](https://docs.mellanox.com/category/mlnxofedib).

Se följande exempel på hur du konfigurerar InfiniBand på Linux:

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

För Windows kan du hämta och installera [Mellanox OFED för Windows-drivrutiner](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Aktivera IP över InfiniBand

Använd följande kommandon för att aktivera IP över InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) på Azure.
