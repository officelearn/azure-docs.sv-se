---
title: Azure Linux VM-storlekar – HPC | Microsoft Docs
description: Visar en lista över de olika storlekarna för Linux-datorer i Azure. Visar information om hur många virtuella processorer, diskar och nätverkskort samt lagring dataflöde och nätverket bandbredd för storlekar i den här serien.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
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
ms.openlocfilehash: 847f25d9be1a8654bbc0435d7874acb0ff793304
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695591"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Högpresterande compute storlekar för virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

SR-IOV aktiverade storlekar för Virtuella datorer på Azure Tillåt nästan vilken smak av MPI som ska användas.
På SR IOV aktiverade virtuella datorer stöds endast Intel MPI 5.x-versioner. Senare versioner (2017, 2018) på Intel MPI-runtime biblioteket kanske eller kanske inte är kompatibla med Azure Linux RDMA-drivrutiner.


### <a name="supported-os-images"></a>OS-avbildningar som stöds
 
Azure Marketplace har många Linux-distributioner som stöder RDMA-anslutning:
  
* **CentOS-baserade HPC** – för icke-SR-IOV-aktiverade virtuella datorer, CentOS-baserade version 6.5 HPC eller en senare version, upp till 7.5 är lämpliga. För virtuella datorer i H-serien rekommenderas versioner 7.1 7.5. RDMA-drivrutiner och Intel MPI 5.1 är installerade på den virtuella datorn.
  För SR-IOV virtuella datorer kommer CentOS-HPC 7.6 optimerade och med RDMA-drivrutiner och olika MPI-paket som är installerade.
  Lägga till tillägget InfiniBandLinux om du vill aktivera InfiniBand för andra RHEL/CentOS VM-avbildningar. Den här Linux VM-tillägget installeras Mellanox OFED drivrutiner (på SR-IOV virtuella datorer) för RDMA-anslutning. Följande PowerShell-cmdlet installerar den senaste versionen (version 1.0) av tillägget InfiniBandDriverLinux på en befintlig RDMA-kompatibla virtuell dator. RDMA-kompatibla VM heter *myVM* och har distribuerats i resursgruppen med namnet *myResourceGroup* i den *västra USA* region på följande sätt:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  VM-tillägg kan tas med i Azure Resource Manager-mallar för enkel distribution med följande JSON-element:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Följande kommando installerar tillägget InfiniBandDriverLinux senaste version 1.0 på alla RDMA-kompatibla virtuella datorer i en befintlig VM-skalningsuppsättning med namnet *myVMSS* distribuerat i resursgruppen med namnet *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > På CentOS-baserade HPC-avbildningar, inaktiveras kernel-uppdateringar i den **yum** konfigurationsfilen. Detta beror på Linux RDMA-drivrutiner har distribuerats som en RPM-paket och drivrutinsuppdateringar kanske inte fungerar om kerneln är uppdaterad.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 för HPC, SLES 12 SP3 för HPC (Premium), SLES 12 SP1 för HPC, SLES 12 SP1 för HPC (Premium), SLES 12 SP4 och SLES 15. RDMA-drivrutiner är installerade och Intel MPI paket distribueras på den virtuella datorn. Installera MPI genom att köra följande kommando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** – Ubuntu Server 16.04 LTS, 18.04 LTS. Konfigurera RDMA drivrutiner på den virtuella datorn och registrera med Intel att ladda ned Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Mer information om hur du aktiverar InfiniBand, hur du konfigurerar MPI, finns i [aktivera InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Konfigurationsalternativ för kluster

Azure tillhandahåller flera alternativ för att skapa kluster av virtuella Linux HPC-datorer som kan kommunicera med RDMA-nätverk, inklusive: 

* **Virtuella datorer** – distribuera RDMA-kompatibla HPC virtuella datorer i samma tillgänglighetsuppsättning (när du använder Azure Resource Manager-distributionsmodellen). Om du använder den klassiska distributionsmodellen kan du distribuera de virtuella datorerna i samma molntjänst. 

* **VM-skalningsuppsättningar** – i en virtuell datorskalning ställer, se till att du begränsa distributionen till en enda placeringsgrupp. Till exempel i en Resource Manager-mall, ange den `singlePlacementGroup` egenskap `true`. 

* **MPI mellan virtuella datorer** – om MPI-kommunikation om det behövs mellan virtuella datorer (VM), se till att de virtuella datorerna i samma tillgänglighetsuppsättning ställs eller den virtuella datorn samma skalningsuppsättning.

* **Azure CycleCloud** -skapa ett HPC-kluster i [Azure CycleCloud](/azure/cyclecloud/) att köra MPI-jobb på Linux-noder.

* **Azure Batch** – skapa en [Azure Batch](/azure/batch/) beräkningsnoder i poolen för att köra MPI-arbetsbelastningar i Linux. Mer information finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pooler](../../batch/batch-pool-compute-intensive-sizes.md). Se även de [Batch skeppsvarv](https://github.com/Azure/batch-shipyard) -projektet för att köra behållarbaserade arbetsbelastningar på Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) har stöd för flera Linux-distributioner att köras på beräkningsnoder som distribuerats i RDMA-kompatibla virtuella Azure-datorer som hanteras av en huvudnod för Windows Server. Ett exempel på distribution finns i [skapa HPC Pack Linux RDMA-kluster i Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Nätverksöverväganden
* Icke-SR-iov, är RDMA-aktiverade virtuella Linux-datorer i Azure, eth1 reserverad för RDMA-nätverkstrafik. Ändra inte eth1 inställningar eller all information i konfigurationsfilen som refererar till det här nätverket.
* På SR-IOV-aktiverade virtuella datorer (HB och HC-serien), ib0 är reserverat för RDMA-nätverkstrafik.
* RDMA-nätverk i Azure reserverar adress utrymme 172.16.0.0/16. Kontrollera att virtuella nätverkets adressutrymme inte överlappar RDMA-nätverk för att köra MPI-program på distribuerade instanser i Azure-nätverk.
* Beroende på ditt val av klusterhanteringsverktyg kan ytterligare konfiguration behövas för att köra MPI-jobb. I ett kluster av virtuella datorer, kan du behöva upprätta förtroende mellan noderna i klustret genom att generera SSH-nycklar eller genom att etablera lösenordslös SSH-inloggningar.


## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Tidigare versioner](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du konfigurerar, optimera och skala [HPC-arbetsbelastningar](../workloads/hpc/configure.md) på Azure.
- Läs mer om hur [Azure-beräkningsenheter (ACU)](acu.md) kan hjälpa dig att jämföra prestanda för databearbetning mellan Azure SKU: er.
