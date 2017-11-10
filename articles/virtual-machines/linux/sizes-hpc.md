---
title: Azure Linux VM-storlekar - HPC | Microsoft Docs
description: "Listar de olika storlekarna som är tillgängliga för Linux med höga prestanda virtuella datorer i Azure."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 31a1ac1f58f52abd2d62e95a2de1a42ce678c43c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Högpresterande compute storlekar för virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>RDMA-kompatibla instanser
En delmängd av beräkningsintensiva instanser (H16r, H16mr, A8 och A9) har ett nätverksgränssnitt för remote direct memory access (RDMA)-anslutning. Det här gränssnittet är utöver standard Azure nätverksgränssnittet tillgänglig för andra storlekar på VM. 
  
Du kan använda RDMA-kompatibla instanser att kommunicera över ett InfiniBand-nätverk med FDR priser för H16r och H16mr virtuella datorer och QDR priser för A8 och A9 virtuella datorer i gränssnittet. Dessa funktioner för RDMA kan öka skalbarhet och prestanda för Message Passing Interface (MPI)-program som körs under Intel MPI 5.x eller en senare version.

Distribuera RDMA-kompatibla virtuella datorer i samma tillgänglighetsuppsättning (när du använder Azure Resource Manager-distributionsmodellen) eller samma tjänst i molnet (när du använder den klassiska distributionsmodellen). Ytterligare krav för RDMA-kompatibla virtuella Linux-datorer att ansluta till Azure RDMA-nätverket följer.

### <a name="distributions"></a>Distributioner
 
Distribuera en beräkningsintensiva virtuell dator från en av avbildningarna i Azure Marketplace som stöder RDMA-anslutningar:
  
* **Ubuntu** -Ubuntu Server 16.04 LTS. Konfigurera RDMA drivrutiner på den virtuella datorn och registrera med Intel hämta Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** -SLES 12 SP3 för HPC SLES 12 SP3 för HPC-kluster (Premium) SLES 12 SP1 för HPC SLES 12 SP1 för HPC (Premium). RDMA drivrutiner och Intel MPI paket distribueras på den virtuella datorn. Installera MPI genom att köra följande kommando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **CentOS-baserade HPC** -CentOS-baserade 7.3 HPC, CentOS-baserade 7.1 HPC, CentOS-baserade 6,8 HPC eller CentOS-baserade 6.5 HPC (för H-serien, version 7.1 eller senare rekommenderas). RDMA-drivrutiner och Intel MPI 5.1 är installerade på den virtuella datorn.  
 
  > [!NOTE]
  > CentOS-baserade HPC-avbildningar i kernel-uppdateringarna har inaktiverats i den **yum** konfigurationsfilen. Detta beror på att drivrutinerna Linux RDMA distribueras som en RPM-paket och drivrutinsuppdateringar kanske inte fungerar om kernel har uppdaterats.
  > 
 
### <a name="cluster-configuration"></a>Klusterkonfiguration 
    
Ytterligare konfiguration krävs för att köra MPI-jobb på klustrade virtuella datorer. Till exempel i ett kluster för virtuella datorer behöver du upprätta förtroende mellan compute-noder. Vanliga inställningar Se [ställa in ett Linux RDMA-kluster som kör MPI program](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Topologiöverväganden för nätverk
* Eth1 är reserverat för RDMA-nätverkstrafik på RDMA-aktiverade Linux virtuella datorer i Azure. Ändra inte Eth1 inställningar eller information i konfigurationsfilen som hänvisar till det här nätverket. Eth0 är reserverat för vanliga Azure nätverkstrafik.

* IP över InfiniBand (IB) stöds inte i Azure. Endast RDMA över IB stöds.

## <a name="using-hpc-pack"></a>Med HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), Microsofts ledigt HPC-kluster och jobbet hanteringslösningen, är ett alternativ som du kan använda beräkningsintensiva instanser med Linux. De senaste versionerna av HPC Pack stöd flera Linux-distributioner som ska köras på compute-noder som är distribuerad i virtuella Azure-datorer hanteras av en Windows Server-huvudnod. Med RDMA-kompatibla Linux datornoderna kör MPI Intel, HPC Pack schemalägga och köra Linux MPI program som kommer åt nätverket RDMA. Se [komma igång med Linux compute-noder i ett HPC Pack kluster i Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>Nästa steg

- Om du vill komma igång distribuerar och använder beräkningsintensiva storlekar med RDMA på Linux, se [ställa in ett Linux RDMA-kluster som kör MPI program](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Läs mer om hur [Azure compute-enheter (ACU)](acu.md) kan hjälpa dig att jämföra beräkning prestanda över Azure SKU: er.




