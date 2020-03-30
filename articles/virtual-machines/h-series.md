---
title: H-serien – virtuella Azure-datorer
description: Specifikationer för H-seriens virtuella datorer.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: cb05a8c42c5710182f81d4bc1c76b78abe59c882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79470511"
---
# <a name="h-series"></a>H-serien

H-serien virtuella datorer är optimerade för program som drivs av höga CPU-frekvenser eller stort minne per kärnkrav. H-serien virtuella datorer har 8 eller 16 Intel Xeon E5 2667 v3-processorkärnor, upp till 14 GB RAM per CPU-kärna och ingen hyperthreading. H-serien har 56 Gb/sek Mellanox FDR InfiniBand i en icke-blockerande fettträdskonfiguration för konsekvent RDMA-prestanda. H-serien virtuella datorer stöder Intel MPI 5.x och MS-MPI.

ACU: 290–300

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

| Storlek | Virtuell processor | Processor | Minne (GB) | Minnesbandbredd GB/s | Bas-CPU-frekvens (GHz) | All-cores frekvens (GHz, topp) | Enkärnfrekvens (GHz, topp) | RDMA-prestanda (Gb/s) | MPI-stöd | Temp-lagring (GB) | Maximalt antal datadiskar | Max Ethernet-nätverkskort |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> För MPI-program aktiveras dedikerade RDMA-backend-nätverk av FDR InfiniBand-nätverket.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Operativsystemavbildningar som stöds (Linux)
 
Azure Marketplace har många Linux-distributioner som stöder RDMA-anslutning:
  
* **CentOS-baserade HPC** - För icke-SR-IOV-aktiverade virtuella datorer är CentOS-baserad version 6.5 HPC eller en senare version lämpliga. För virtuella datorer i H-serien rekommenderas version 7.1 till 7.5. RDMA-drivrutiner och Intel MPI 5.1 är installerade på den virtuella datorn.
  För SR-IOV-virtuella datorer är CentOS-HPC 7.6 optimerad och förinstallerad med RDMA-drivrutinerna och olika MPI-paket installerade.
  För andra RHEL/CentOS VM-avbildningar lägger du till InfiniBandLinux-tillägget för att aktivera InfiniBand. Detta Linux VM-tillägg installerar Mellanox OFED-drivrutiner (på SR-IOV-virtuella datorer) för RDMA-anslutning. Följande PowerShell-cmdlet installerar den senaste versionen (version 1.0) av InfiniBandDriverLinux-tillägget på en befintlig virtuell virtuell dator med RDMA-kompatibel. Den virtuella datorn för fjärrskrivbordskompatibla har namnet *myVM* och distribueras i resursgruppen *myResourceGroup* i regionen *Västra USA* enligt följande:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Alternativt kan VM-tillägg inkluderas i Azure Resource Manager-mallar för enkel distribution med följande JSON-element:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Följande kommando installerar den senaste versionen 1.0 InfiniBandDriverLinux-tillägget på alla virtuella datorer med FJÄRRSKRIVARverkan i en befintlig skalauppsättning för virtuella datorer med namnet *myVMSS* som distribueras i resursgruppen *myResourceGroup:*
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > På centos-baserade HPC-avbildningar inaktiveras kärnuppdateringar i **yum-konfigurationsfilen.** Detta beror på att Linux RDMA-drivrutiner distribueras som ett RPM-paket och drivrutinsuppdateringar kanske inte fungerar om kärnan uppdateras.
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 för HPC, SLES 12 SP3 för HPC (Premium), SLES 12 SP1 för HPC, SLES 12 SP1 för HPC (Premium), SLES 12 SP4 och SLES 15. RDMA-drivrutiner installeras och Intel MPI-paket distribueras på den virtuella datorn. Installera MPI genom att köra följande kommando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16,04 LTS, 18,04 LTS. Konfigurera RDMA-drivrutiner på den virtuella datorn och registrera dig hos Intel för att hämta Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Mer information om hur du aktiverar InfiniBand, konfigurera MPI finns i [Aktivera InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
