---
title: Azure Linux VM-storlekar – HPC | Microsoft Docs
description: Visar en lista över de olika storlekarna för Linux-datorer i Azure. Visar information om hur många virtuella processorer, diskar och nätverkskort samt lagring dataflöde och nätverket bandbredd för storlekar i den här serien.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 748cb4612b2b5aed26ba8197cfad0782f2645e1e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37902137"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Högpresterande compute storlekar för virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Endast Intel MPI 5.x-versioner som stöds. Senare versioner (2017, 2018) på Intel MPI runtime-biblioteket är inte kompatibla med Azure Linux RDMA-drivrutiner.


### <a name="distributions"></a>Distributioner
 
Distribuera en beräkningsintensiv virtuell dator från en avbildning i Azure Marketplace som stöder RDMA-anslutning:
  
* **Ubuntu** – Ubuntu Server 16.04 LTS. Konfigurera RDMA drivrutiner på den virtuella datorn och registrera med Intel att ladda ned Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** -SLES 12 SP3 för HPC, SLES 12 SP3 för HPC (Premium), SLES 12 SP1 för HPC, SLES 12 SP1 för HPC (Premium). RDMA-drivrutiner är installerade och Intel MPI paket distribueras på den virtuella datorn. Installera MPI genom att köra följande kommando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **CentOS-baserade HPC** -CentOS-baserade 6,5 HPC eller en senare version (H-serien, version 7.1 eller senare rekommenderas). RDMA-drivrutiner och Intel MPI 5.1 är installerade på den virtuella datorn.  
 
  > [!NOTE]
  > På CentOS-baserade HPC-avbildningar, inaktiveras kernel-uppdateringar i den **yum** konfigurationsfilen. Detta beror på Linux RDMA-drivrutiner har distribuerats som en RPM-paket och drivrutinsuppdateringar kanske inte fungerar om kerneln är uppdaterad.
  > 
 
### <a name="cluster-configuration"></a>Klusterkonfiguration 
    
Ytterligare konfiguration krävs för att köra MPI-jobb på klustrade virtuella datorer. Till exempel i ett kluster av virtuella datorer kan behöva du upprätta förtroende mellan compute-noder. Vanliga inställningar Se [konfigurera ett Linux RDMA-kluster för att köra MPI-program](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Topologiöverväganden för nätverk
* Eth1 är reserverad för nätverkstrafik för RDMA i RDMA-aktiverade virtuella Linux-datorer i Azure. Ändra inte Eth1 inställningar eller all information i konfigurationsfilen som refererar till det här nätverket. Eth0 är reserverad för vanliga Azure nätverkstrafik.

* RDMA-nätverk i Azure reserverar adress utrymme 172.16.0.0/16. 


## <a name="using-hpc-pack"></a>Med HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), Microsofts kostnadsfria HPC-kluster och jobb hanteringslösning, är ett alternativ som du kan använda beräkningsintensiva instanser med Linux. De senaste versionerna av HPC Pack-stöd flera Linux-distributioner att köras på beräkningsnoder som distribueras i Azure-datorer som hanteras av en huvudnod för Windows Server. Med RDMA-kompatibla Linux-beräkningsnoder som kör Intel MPI, HPC Pack schemalägga och köra Linux MPI-program som har åtkomst till RDMA-nätverk. Se [Kom igång med Linux-beräkningsnoder i ett HPC Pack-kluster i Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Tidigare versioner](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

- Kom igång distributionen och användningen av beräkningsintensiva storlekar med RDMA på Linux, se [konfigurera ett Linux RDMA-kluster för att köra MPI-program](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Läs mer om hur [Azure-beräkningsenheter (ACU)](acu.md) kan hjälpa dig att jämföra prestanda för databearbetning mellan Azure SKU: er.




