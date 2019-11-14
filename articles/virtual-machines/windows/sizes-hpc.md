---
title: Azure Windows VM-storlekar – HPC
description: Visar en lista över de olika storlekar som är tillgängliga för Windows-datorer med hög prestanda beräkning i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: e96c74eb4b9b96459bb5de7db2faeb47ed99a82e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065440"
---
# <a name="high-performance-compute-vm-sizes"></a>Storlekar för beräkning av virtuella datorer med höga prestanda

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Operativ system** – Windows Server 2016 på alla de virtuella datorer med HPC-serien. Windows Server 2012 R2, Windows Server 2012 stöds också på de virtuella datorer som inte är SR-IOV-aktiverade (därför utesluter HB och HC).

* **MPI** – SR-IOV-aktiverade VM-storlekar på Azure (HB, HC) tillåter nästan vilken variant av MPI som ska användas med Mellanox ofed.
På virtuella datorer som inte är SR-IOV-aktiverade använder MPI-implementeringar som stöds av Microsoft Network Direct (ND)-gränssnittet för att kommunicera mellan instanser. Därför stöds endast Microsoft MPI (MS-MPI) 2012 R2 eller senare och Intel MPI 5. x-versioner. Senare versioner (2017, 2018) av Intel MPI runtime-biblioteket kan vara kompatibla med Azure RDMA-drivrutinerna.

* **INFINIBANDDRIVERWINDOWS VM-tillägg** – i RDMA-kompatibla virtuella datorer lägger du till InfiniBandDriverWindows-tillägget för att aktivera InfiniBand. Det här Windows VM-tillägget installerar Windows Network Direct-drivrutiner (på datorer som inte är SR-IOV) eller Mellanox OFED-drivrutiner (på SR-IOV VM) för RDMA-anslutning.
I vissa distributioner av A8-och A9-instanser läggs HpcVmDrivers-tillägget till automatiskt. Observera att HpcVmDrivers VM-tillägget är inaktuellt. den kommer inte att uppdateras. Om du vill lägga till ett VM-tillägg i en virtuell dator kan du använda [Azure PowerShell](/powershell/azure/overview) -cmdletar. 

  Följande kommando installerar det senaste version 1,0 InfiniBandDriverWindows-tillägget på en befintlig RDMA-kompatibel virtuell dator med namnet *myVM* distribuerad i resurs gruppen med namnet *MYRESOURCEGROUP* i regionen *USA, västra* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Du kan också inkludera VM-tillägg i Azure Resource Manager mallar för enkel distribution med följande JSON-element:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Följande kommando installerar det senaste version 1,0 InfiniBandDriverWindows-tillägget på alla RDMA-kompatibla virtuella datorer i en befintlig skalnings uppsättning för virtuella datorer med namnet *myVMSS* distribuerat i resurs gruppen med namnet *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Mer information finns i [tillägg och funktioner för virtuella datorer](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Du kan också arbeta med tillägg för virtuella datorer som distribueras i den [klassiska distributions modellen](classic/manage-extensions.md).

* **RDMA-nätverkets adress utrymme** – RDMA-nätverket i Azure reserverar adress utrymmet 172.16.0.0/16. Om du vill köra MPI-program på instanser som har distribuerats i ett virtuellt Azure-nätverk kontrollerar du att det virtuella nätverkets adress utrymme inte överlappar RDMA-nätverket.


### <a name="cluster-configuration-options"></a>Kluster konfigurations alternativ

Azure innehåller flera alternativ för att skapa kluster med virtuella Windows HPC-datorer som kan kommunicera med RDMA-nätverket, inklusive: 

* **Virtuella datorer** – distribuera RDMA-kompatibla HPC-datorer i samma tillgänglighets uppsättning (när du använder Azure Resource Manager distributions modell). Om du använder den klassiska distributions modellen distribuerar du de virtuella datorerna i samma moln tjänst. 

* **Skalnings uppsättningar för virtuella datorer** – i en skalnings uppsättning för virtuella datorer, se till att du begränsar distributionen till en enda placerings grupp. I en Resource Manager-mall anger du till exempel egenskapen `singlePlacementGroup` som `true`. 

* **MPI mellan virtuella datorer** – om MPI kommunikation krävs mellan virtuella datorer (VM) kontrollerar du att de virtuella datorerna finns i samma tillgänglighets uppsättning eller på den virtuella datorn med samma skalnings uppsättning.

* **Azure-CycleCloud** – skapa ett HPC-kluster i [Azure CycleCloud](/azure/cyclecloud/) för att köra MPI-jobb på Windows-noder.

* **Azure Batch** – skapa en [Azure Batch](/azure/batch/) -pool för att köra MPI-arbetsbelastningar på Windows Server Compute-noder. Mer information finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-pooler](../../batch/batch-pool-compute-intensive-sizes.md). Se även [batch Shipyard](https://github.com/Azure/batch-shipyard) -projektet för att köra containerbaserade arbets belastningar i batch.

* **Microsoft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) innehåller en körnings miljö för MS-MPI som använder Azure RDMA-nätverket när det distribueras på RDMA-kompatibla virtuella Windows-datorer. Exempel på distributioner finns i [Konfigurera ett Windows RDMA-kluster med HPC Pack för att köra MPI-program](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md)
- [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

- För check listor för att använda beräknings intensiva instanser med HPC Pack på Windows Server, se [Konfigurera ett Windows RDMA-kluster med HPC Pack för att köra MPI-program](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Om du vill använda beräknings intensiva instanser när du kör MPI-program med Azure Batch, se [Använd aktiviteter med flera instanser för att köra MPI-program (Message Passing Interface) i Azure Batch](../../batch/batch-mpi.md).

- Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
