---
title: Azure Windows VM-storlekar - HPC | Microsoft Docs
description: "Listar de olika storlekarna som är tillgängliga för Windows med höga prestanda virtuella datorer i Azure."
services: virtual-machines-windows
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.openlocfilehash: a0596d134e9c26877848f93d72f35bfd2c957570
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="high-performance-compute-vm-sizes"></a>Högpresterande beräkning VM-storlekar

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>RDMA-kompatibla instanser
En delmängd av beräkningsintensiva instanser (H16r, H16mr, A8 och A9) har ett nätverksgränssnitt för remote direct memory access (RDMA)-anslutning. Det här gränssnittet är utöver standard Azure nätverksgränssnittet tillgänglig för andra storlekar på VM. 
  
Du kan använda RDMA-kompatibla instanser att kommunicera över ett InfiniBand-nätverk med FDR priser för H16r och H16mr virtuella datorer och QDR priser för A8 och A9 virtuella datorer i gränssnittet. Dessa funktioner för RDMA kan öka den skalbarhet och prestanda för Message Passing Interface (MPI) program.

Följande är kraven för RDMA-kompatibla virtuella Windows-datorer att ansluta till Azure RDMA-nätverket: 

* **Operativsystem**
  
  Windows Server 2012 R2, Windows Server 2012
  
  > [!NOTE]
  > Windows Server 2016 stöder för närvarande inte RDMA-anslutningar i Azure.
  >

* **Tillgänglighetsuppsättning eller Molntjänsten** – distribuera RDMA-kompatibla virtuella datorer i samma tillgänglighetsuppsättning (när du använder Azure Resource Manager-distributionsmodellen) eller samma tjänst i molnet (när du använder den klassiska distributionsmodellen). Om du använder Azure Batch måste RDMA-kompatibla virtuella datorer vara i samma pool.

* **MPI** -Microsoft MPI (MS-MPI) 2012 R2 eller senare, Intel MPI biblioteket 5.x

  Stöds MPI-implementeringar kan du använda Microsoft Network Direct-gränssnittet för kommunikation mellan instanser. 

* **RDMA nätverks-adressutrymme** -av RDMA-nätverk i Azure reserverar adressutrymme 172.16.0.0/16. Kontrollera att virtuella nätverkets adressutrymme inte överlappar RDMA-nätverk för att köra MPI program på instanser distribuerade i Azure-nätverk.

* **HpcVmDrivers VM-tillägget** -på RDMA-kompatibla virtuella datorer, måste du lägga till tillägget HpcVmDrivers för att installera drivrutiner för Windows-nätverk för RDMA-anslutning. (I vissa distributioner av A8- och A9 instanser HpcVmDrivers tillägg läggs automatiskt.) Om du vill lägga till tillägg för virtuell dator till en virtuell dator, kan du använda [Azure PowerShell](/powershell/azure/overview) cmdlets. 

  
  Senaste version 1.1 HpcVMDrivers tillägget installeras i följande kommando på en befintlig RDMA-kompatibla virtuell dator med namnet *myVM* distribuerats i resursgruppen med namnet *myResourceGroup* i den  *USA, västra* region:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  Mer information finns i [tillägg för virtuell dator och funktioner](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Du kan också fungera med tillägg för virtuella datorer som distribueras i det [klassiska distributionsmodellen](classic/manage-extensions.md).


## <a name="using-hpc-pack"></a>Med HPC Pack

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), Microsofts ledigt HPC-kluster och jobbet hanteringslösningen, är ett alternativ för att skapa ett beräkningskluster i Azure kör MPI för Windows-baserade program och andra HPC-arbetsbelastningar. HPC Pack 2012 R2 och senare versioner innehåller en körningsmiljö för MS-MPI som använder Azure RDMA-nätverk när de distribueras på RDMA-kompatibla virtuella datorer.




## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md)
- [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)

## <a name="next-steps"></a>Nästa steg

- Checklistor använda beräkningsintensiva instanser med HPC Pack på Windows Server, se [ställa in ett RDMA-Windows-kluster med HPC Pack som kör MPI program](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Om du vill använda beräkningsintensiva instanser när du kör MPI-program med Azure Batch, se [använder flera instanser uppgifter för att köra program för Message Passing Interface (MPI) i Azure Batch](../../batch/batch-mpi.md).

- Läs mer om hur [Azure compute-enheter (ACU)](acu.md) kan hjälpa dig att jämföra beräkning prestanda över Azure SKU: er.




