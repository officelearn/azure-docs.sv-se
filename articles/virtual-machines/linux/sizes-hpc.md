---
title: VIRTUELLA Azure Linux-storlekar – HPC
description: Visar en lista över de olika storlekar som är tillgängliga för virtuella Linux-datorer med höga prestanda i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 214ab48b6b0dca37eff3b3f155aaa92afc7fee16
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034899"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Storlek för beräkning av virtuella datorer med höga prestanda

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

SR-IOV-aktiverade VM-storlekar på Azure tillåter nästan vilken variant av MPI som ska användas.
På virtuella datorer som inte är SR-IOV-aktiverade stöds endast Intel MPI 5. x-versioner. Senare versioner (2017, 2018) av Intel MPI runtime-biblioteket kan vara kompatibla med Azure Linux RDMA-drivrutinerna.


### <a name="supported-os-images"></a>OS-avbildningar som stöds
 
Azure Marketplace har många Linux-distributioner som stöder RDMA-anslutning:
  
* **CentOS-baserade HPC** -för icke-SR-IOV-aktiverade virtuella datorer, CentOS version 6,5 HPC eller en senare version, upp till 7,5 är lämpliga. För virtuella datorer i H-serien rekommenderas version 7,1 till 7,5. RDMA-drivrutiner och Intel MPI 5,1 är installerade på den virtuella datorn.
  För virtuella datorer med SR-IOV är CentOS-HPC 7,6 optimerade och förinstallerade med RDMA-drivrutinerna och olika MPI-paket installerade.
  För andra RHEL/CentOS VM-avbildningar lägger du till InfiniBandLinux-tillägget för att aktivera InfiniBand. Detta Linux VM-tillägg installerar Mellanox OFED-drivrutiner (på SR-IOV VM) för RDMA-anslutning. Följande PowerShell-cmdlet installerar den senaste versionen (version 1,0) av InfiniBandDriverLinux-tillägget på en befintlig RDMA-kompatibel virtuell dator. Den RDMA-kompatibla virtuella datorn heter *myVM* och distribueras i resurs gruppen med namnet *MYRESOURCEGROUP* i regionen *USA, västra* enligt följande:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Du kan också inkludera VM-tillägg i Azure Resource Manager mallar för enkel distribution med följande JSON-element:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Följande kommando installerar det senaste version 1,0 InfiniBandDriverLinux-tillägget på alla RDMA-kompatibla virtuella datorer i en befintlig skalnings uppsättning för virtuella datorer med namnet *myVMSS* distribuerat i resurs gruppen med namnet *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > I CentOS-baserade HPC-avbildningar inaktive ras kernel-uppdateringar i **yum** -konfigurationsfilen. Detta beror på att Linux RDMA-drivrutinerna distribueras som ett RPM-paket, och driv rutins uppdateringar kanske inte fungerar om kerneln uppdateras.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 för HPC, SLES 12 SP3 för HPC (Premium), SLES 12 SP1 för HPC, SLES 12 SP1 för HPC (Premium), SLES 12 SP4 och SLES 15. RDMA-drivrutiner är installerade och Intel MPI-paket distribueras på den virtuella datorn. Installera MPI genom att köra följande kommando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -Ubuntu Server 16,04 LTS, 18,04 LTS. Konfigurera RDMA-drivrutiner på den virtuella datorn och registrera dig för Intel för att ladda ned Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Mer information om hur du aktiverar InfiniBand, konfigurerar MPI finns i [Aktivera InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Kluster konfigurations alternativ

Azure innehåller flera alternativ för att skapa kluster av virtuella Linux HPC-datorer som kan kommunicera med RDMA-nätverket, inklusive: 

* **Virtuella datorer** – distribuera RDMA-kompatibla HPC-datorer i samma tillgänglighets uppsättning (när du använder Azure Resource Manager distributions modell). Om du använder den klassiska distributions modellen distribuerar du de virtuella datorerna i samma moln tjänst. 

* **Skalnings uppsättningar för virtuella datorer** – i en skalnings uppsättning för virtuella datorer, se till att du begränsar distributionen till en enda placerings grupp. I en Resource Manager-mall anger du till exempel egenskapen `singlePlacementGroup` som `true`. 

* **MPI mellan virtuella datorer** – om MPI kommunikation krävs mellan virtuella datorer (VM) kontrollerar du att de virtuella datorerna finns i samma tillgänglighets uppsättning eller på den virtuella datorn med samma skalnings uppsättning.

* **Azure-CycleCloud** – skapa ett HPC-kluster i [Azure CycleCloud](/azure/cyclecloud/) för att köra MPI-jobb på Linux-noder.

* **Azure Batch** – skapa en [Azure Batch](/azure/batch/) pool för att köra MPI-arbetsbelastningar på Linux Compute-noder. Mer information finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-pooler](../../batch/batch-pool-compute-intensive-sizes.md). Se även [batch Shipyard](https://github.com/Azure/batch-shipyard) -projektet för att köra containerbaserade arbets belastningar i batch.

* **Microsoft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) stöder flera Linux-distributioner som kan köras på Compute-noder som distribueras i RDMA-kompatibla virtuella Azure-datorer, som hanteras av en Windows Server Head-nod. Ett exempel på distribution finns i [skapa HPC-paket Linux RDMA-kluster i Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Nätverks överväganden
* På icke-SR-IOV, RDMA-aktiverade virtuella Linux-datorer i Azure, är eth1 reserverat för RDMA-nätverkstrafik. Ändra inte några eth1-inställningar eller någon information i konfigurations filen som refererar till det här nätverket.
* På SR-IOV-aktiverade virtuella datorer (HB och HC-serien) är ib0 reserverat för RDMA-nätverkstrafik.
* RDMA-nätverket i Azure reserverar adress utrymmet 172.16.0.0/16. Om du vill köra MPI-program på instanser som har distribuerats i ett virtuellt Azure-nätverk kontrollerar du att det virtuella nätverkets adress utrymme inte överlappar RDMA-nätverket.
* Beroende på ditt val av kluster hanterings verktyg kan ytterligare system konfiguration behövas för att köra MPI-jobb. I ett kluster med virtuella datorer kan du till exempel behöva upprätta förtroende mellan klusternoderna genom att generera SSH-nycklar eller genom att upprätta lösen ords lösa SSH-inloggningar.


## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att konfigurera, optimera och skala [HPC-arbetsbelastningar](../workloads/hpc/configure.md) på Azure.
- Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
