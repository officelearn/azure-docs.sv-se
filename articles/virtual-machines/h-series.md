---
title: H-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i H-serien.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 03/10/2020
ms.author: jushiman
ms.openlocfilehash: f79dcb8886985d60a1ed82e1a77d231cf7d3ad24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678689"
---
# <a name="h-series"></a>H-serien

Virtuella datorer i H-serien är optimerade för program som drivs av höga CPU-frekvenser eller stora minnen per kärn krav. Virtuella datorer i H-serien 8 eller 16 Intel Xeon E5 2667 v3 processor kärnor, upp till 14 GB RAM-minne per CPU-kärna och ingen hyperthreading-funktion. H-seriens funktioner 56 GB/s Mellanox FDR InfiniBand i en icke-blockerande fett träds konfiguration för konsekvent RDMA-prestanda. Virtuella datorer i H-serien har stöd för Intel MPI 5. x och MS-MPI.

ACU: 290–300

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Direktmigrering: stöds inte

Minnes bebetjänings uppdateringar: stöds inte

| Storlek | Virtuell processor | Processor | Minne (GB) | Minnes bandbredd GB/s | Bas processor frekvens (GHz) | Frekvens för alla kärnor (GHz, högsta) | Frekvens för enkla kärnor (GHz, hög) | RDMA-prestanda (GB/s) | MPI-stöd | Temp-lagring (GB) | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Högsta Ethernet-nätverkskort |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 × 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 × 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |

<sup>1</sup> för MPI-program aktive ras dedicerade RDMA-backend-nätverk av FDR Infiniband-nätverket.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>OS-avbildningar som stöds (Linux)
 
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

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Mer information om hur du aktiverar InfiniBand, konfigurerar MPI finns i [Aktivera InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
