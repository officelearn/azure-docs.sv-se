---
title: Azure Windows VM-storlekar – HPC | Microsoft Docs
description: Visar en lista över de olika storlekarna som är tillgängliga för Windows-datorer i Azure. Visar information om hur många virtuella processorer, diskar och nätverkskort samt lagring dataflöde och nätverket bandbredd för storlekar i den här serien.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck;amverma
ms.openlocfilehash: ad490084b34a8bf6e89c7feb14d5cd2e70a8138f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755312"
---
# <a name="high-performance-compute-vm-sizes"></a>Högpresterande storlekar på virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Operativsystemet** -Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **MPI** - Microsoft MPI (MS-MPI) 2012 R2 or later, Intel MPI Library 5.x

  På SR IOV aktiverade virtuella datorer Använd stöds MPI-implementeringar gränssnittet Microsoft Network Direct (IV) för att kommunicera mellan instanser. SR-IOV aktiverade VM-storlekar (HB och HC-serien) på Azure Tillåt nästan vilken version av MPI som ska användas med Mellanox OFED. 

* **InfiniBandDriverWindows VM-tillägget** – på RDMA-kompatibla virtuella datorer, lägga till tillägget InfiniBandDriverWindows om du vill aktivera InfiniBand. Den här Windows VM-tillägget installeras Windows Network Direct drivrutiner (på SR IOV virtuella datorer) eller Mellanox OFED drivrutiner (på SR-IOV virtuella datorer) för RDMA-anslutning.
I vissa distributioner av A8 och A9-instanser läggs tillägget HpcVmDrivers automatiskt. Observera att HpcVmDrivers VM-tillägget används längre; Det kommer inte att uppdateras. Om du vill lägga till VM-tillägget till en virtuell dator, kan du använda [Azure PowerShell](/powershell/azure/overview) cmdletar. 

  Följande kommando installerar det senaste version 1.0 InfiniBandDriverWindows tillägget på en befintlig RDMA-kompatibla virtuell dator med namnet *myVM* distribuerat i resursgruppen med namnet *myResourceGroup* i  *Västra USA* region:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  VM-tillägg kan tas med i Azure Resource Manager-mallar för enkel distribution, med följande JSON-element:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Mer information finns i [virtuella datorer, tillägg och funktioner](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Du kan också arbeta med tillägg för virtuella datorer som distribueras i den [klassiska distributionsmodellen](classic/manage-extensions.md).

* **RDMA nätverksadressutrymme** – The RDMA-nätverk i Azure reserverar adress utrymme 172.16.0.0/16. Kontrollera att virtuella nätverkets adressutrymme inte överlappar RDMA-nätverk för att köra MPI-program på distribuerade instanser i Azure-nätverk.


### <a name="cluster-configuration-options"></a>Konfigurationsalternativ för kluster

Azure tillhandahåller flera alternativ för att skapa kluster i Windows HPC-datorer som kan kommunicera med RDMA-nätverk, inklusive: 

* **Virtuella datorer** – distribuera RDMA-kompatibla HPC virtuella datorer i samma tillgänglighetsuppsättning (när du använder Azure Resource Manager-distributionsmodellen). Om du använder den klassiska distributionsmodellen kan du distribuera de virtuella datorerna i samma molntjänst. 

* **VM-skalningsuppsättningar** – i en virtuell datorskalning ställer, se till att du begränsa distributionen till en enda placeringsgrupp. Till exempel i en Resource Manager-mall, ange den `singlePlacementGroup` egenskap `true`. 

* **Azure CycleCloud** -skapa ett HPC-kluster i [Azure CycleCloud](/azure/cyclecloud/) att köra MPI-jobb på Windows-noder.

* **Azure Batch** – skapa en [Azure Batch](/azure/batch/) beräkningsnoder i poolen för att köra MPI-arbetsbelastningar på Windows Server. Mer information finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pooler](../../batch/batch-pool-compute-intensive-sizes.md). Se även de [Batch skeppsvarv](https://github.com/Azure/batch-shipyard) -projektet för att köra behållarbaserade arbetsbelastningar på Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) innehåller en körningsmiljö för MS-MPI som använder Azure RDMA-nätverk när de distribueras på RDMA-kompatibla Windows virtuella datorer. Till exempel distributioner, se [ställa in ett Windows RDMA-kluster med HPC Pack för att köra MPI-program](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md)
- [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Tidigare versioner](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

- Checklistor att använda beräkningsintensiva instanser med HPC Pack på Windows Server, se [ställa in ett Windows RDMA-kluster med HPC Pack för att köra MPI-program](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Om du vill använda beräkningsintensiva instanser när du kör MPI-program med Azure Batch, se [använda aktiviteter med flera instanser för att köra program med Message Passing Interface (MPI) i Azure Batch](../../batch/batch-mpi.md).

- Läs mer om hur [Azure-beräkningsenheter (ACU)](acu.md) kan hjälpa dig att jämföra prestanda för databearbetning mellan Azure SKU: er.
