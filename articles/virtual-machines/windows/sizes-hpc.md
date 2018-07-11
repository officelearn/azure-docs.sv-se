---
title: Azure Windows VM-storlekar – HPC | Microsoft Docs
description: Visar en lista över de olika storlekarna som är tillgängliga för Windows-datorer i Azure. Visar information om hur många virtuella processorer, diskar och nätverkskort samt lagring dataflöde och nätverket bandbredd för storlekar i den här serien.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 31e81741d2a627888e478b3871bdbab4e6b6d6f5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37902647"
---
# <a name="high-performance-compute-vm-sizes"></a>Högpresterande storlekar på virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Operativsystemet** -Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **MPI** -Microsoft MPI (MS-MPI) 2012 R2 eller senare, Intel MPI biblioteket 5.x

  Stöds MPI-implementeringar använda Microsoft Network Direct-gränssnittet för att kommunicera mellan instanser. 

* **RDMA nätverksadressutrymme** – The RDMA-nätverk i Azure reserverar adress utrymme 172.16.0.0/16. Kontrollera att virtuella nätverkets adressutrymme inte överlappar RDMA-nätverk för att köra MPI-program på distribuerade instanser i Azure-nätverk.

* **HpcVmDrivers VM-tillägget** – på RDMA-kompatibla virtuella datorer, lägga till tillägget HpcVmDrivers för att installera drivrutiner för Windows-nätverk för RDMA-anslutning. (I vissa distributioner av A8 och A9-instanserna HpcVmDrivers tillägget läggs automatiskt.) Om du vill lägga till VM-tillägget till en virtuell dator, kan du använda [Azure PowerShell](/powershell/azure/overview) cmdletar. 

  
  Följande kommando installerar det senaste version 1.1 HpcVMDrivers tillägget på en befintlig RDMA-kompatibla virtuell dator med namnet *myVM* distribuerat i resursgruppen med namnet *myResourceGroup* i den  *Västra USA* region:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  Mer information finns i [virtuella datorer, tillägg och funktioner](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Du kan också arbeta med tillägg för virtuella datorer som distribueras i den [klassiska distributionsmodellen](classic/manage-extensions.md).


## <a name="using-hpc-pack"></a>Med HPC Pack

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), Microsofts kostnadsfria HPC-kluster och jobb hanteringslösning, är ett alternativ för dig att skapa ett beräkningskluster i Azure för att köra Windows-baserade MPI-program och andra HPC-arbetsbelastningar. HPC Pack 2012 R2 och senare versioner innehåller en körningsmiljö för MS-MPI som använder Azure RDMA-nätverk när de distribueras på RDMA-kompatibla virtuella datorer.



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




