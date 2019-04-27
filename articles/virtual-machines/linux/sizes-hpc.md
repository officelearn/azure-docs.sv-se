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
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 44b965bd60d976d4d28dc5e31d78a1c838d4ee02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542358"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Högpresterande compute storlekar för virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Endast Intel MPI 5.x-versioner som stöds.

> [!NOTE]
> Senare versioner (2017, 2018) på Intel MPI-runtime biblioteket kanske eller kanske inte är kompatibla med Azure Linux RDMA-drivrutiner.

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
 
### <a name="cluster-configuration-options"></a>Konfigurationsalternativ för kluster

Azure tillhandahåller flera alternativ för att skapa kluster av virtuella Linux HPC-datorer som kan kommunicera med RDMA-nätverk, inklusive: 

* **Virtuella datorer** – distribuera RDMA-kompatibla HPC virtuella datorer i samma tillgänglighetsuppsättning (när du använder Azure Resource Manager-distributionsmodellen). Om du använder den klassiska distributionsmodellen kan du distribuera de virtuella datorerna i samma molntjänst. 

* **VM-skalningsuppsättningar** – i en VM-skalningsuppsättning ställer, se till att du begränsa distributionen till en enda placeringsgrupp. Till exempel i en Resource Manager-mall, ange den `singlePlacementGroup` egenskap `true`. 

* **Azure CycleCloud** -skapa ett HPC-kluster i [Azure CycleCloud](/azure/cyclecloud/) att köra MPI-jobb på Linux-noder.

* **Azure Batch** – skapa en [Azure Batch](/azure/batch/) beräkningsnoder i poolen för att köra MPI-arbetsbelastningar i Linux. Mer information finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pooler](../../batch/batch-pool-compute-intensive-sizes.md). Se även de [Batch skeppsvarv](https://github.com/Azure/batch-shipyard) -projektet för att köra behållarbaserade arbetsbelastningar på Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) har stöd för flera Linux-distributioner att köras på beräkningsnoder som distribuerats i RDMA-kompatibla virtuella Azure-datorer som hanteras av en huvudnod för Windows Server. Ett exempel på distribution finns i [skapa HPC Pack Linux RDMA-kluster i Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

Beroende på ditt val av klusterhanteringsverktyg kan ytterligare konfiguration behövas för att köra MPI-jobb. I ett kluster av virtuella datorer, kan du behöva upprätta förtroende mellan noderna i klustret genom att generera SSH-nycklar eller genom att etablera lösenordslös SSH-förtroende.

### <a name="network-topology-considerations"></a>Topologiöverväganden för nätverk
* Eth1 är reserverad för nätverkstrafik för RDMA i RDMA-aktiverade virtuella Linux-datorer i Azure. Ändra inte Eth1 inställningar eller all information i konfigurationsfilen som refererar till det här nätverket. Eth0 är reserverad för vanliga Azure nätverkstrafik.

* RDMA-nätverk i Azure reserverar adress utrymme 172.16.0.0/16. 




## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Tidigare versioner](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur [Azure-beräkningsenheter (ACU)](acu.md) kan hjälpa dig att jämföra prestanda för databearbetning mellan Azure SKU: er.




