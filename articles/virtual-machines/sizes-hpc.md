---
title: Azure VM storlekar - HPC | Microsoft-dokument
description: Visar en lista över de olika storlekar som är tillgängliga för virtuella datorer med hög prestanda i Azure. Visar information om antalet virtuella processorer, datadiskar och nätverkskort samt lagringsdataflöde och nätverksbandbredd för storlekar i den här serien.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: df22c857571e51bb886ff1d25db185a306999540
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420859"
---
# <a name="high-performance-computing-vm-sizes"></a>Datorsstorlekar med hög prestanda

Virtuella datorer i Azure H-serien (VMs) är utformade för att leverera prestanda i ledarskapsklass, MPI-skalbarhet och kostnadseffektivitet för en mängd olika HPC-arbetsbelastningar i verkliga världen.

[HBv2-serien](hbv2-series.md) Virtuella datorer har 200 Gb/sek Mellanox HDR InfiniBand, medan både HB och HC-serien virtuella datorer har 100 Gb/sek Mellanox EDR InfiniBand. Var och en av dessa vm-typer är anslutna i ett fettträd som inte blockerar för optimerad och konsekvent RDMA-prestanda. HBv2 virtuella datorer stöder adaptiv routning och dynamisk ansluten transport (DCT, utöver vanliga RC- och UD-transporter). Dessa funktioner förbättrar programmets prestanda, skalbarhet och konsekvens, och användning av dem rekommenderas starkt.

[HB-serien](hb-series.md) Virtuella datorer är optimerade för program som drivs av minnesbandbredd, till exempel vätskedynamik, explicit finit elementanalys och vädermodellering. HB virtuella datorer har 60 AMD EPYC 7551 processorkärnor, 4 GB RAM per CPU-kärna, och ingen hyperthreading. AMD EPYC-plattformen ger mer än 260 GB/sek minnesbandbredd.

[HC-serien](hc-series.md) Virtuella datorer är optimerade för applikationer som drivs av tät beräkning, till exempel implicit finita elementanalys, molekylär dynamik och beräkningskemi. HC VMs har 44 Intel Xeon Platinum 8168 processorkärnor, 8 GB RAM per CPU-kärna, och ingen hyperthreading. Intel Xeon Platinum-plattformen stöder Intels rika ekosystem av programvaruverktyg som Intel Math Kernel Library.

[H-serien](h-series.md) Virtuella datorer är optimerade för program som drivs av höga CPU-frekvenser eller stort minne per kärnkrav. H-serien virtuella datorer har 8 eller 16 Intel Xeon E5 2667 v3-processorkärnor, 7 eller 14 GB RAM per CPU-kärna och ingen hyperthreading. H-serien har 56 Gb/sek Mellanox FDR InfiniBand i en icke-blockerande fettträdskonfiguration för konsekvent RDMA-prestanda. H-serien virtuella datorer stöder Intel MPI 5.x och MS-MPI.

> [!NOTE]
> De virtuella datorerna A8 – A11 är planerade att gå i pension den 3/2021. Mer information finns i [HPC:s migreringsguide](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>RDMA-kompatibla instanser

De flesta av HPC VM storlekar (HBv2, HB, HC, H16r, H16mr, A8 och A9) har ett nätverksgränssnitt för fjärråtkomst direkt minne (RDMA) anslutning. Utvalda [N-serien]https://docs.microsoft.com/azure/virtual-machines/nc-series) ( storlekar som betecknas med "r" såsom NC24rs-konfigurationerna (NC24rs_v3, NC24rs_v2 och NC24r) är också RDMA-kapabla. Det här gränssnittet är utöver det standarda Azure-nätverksgränssnittet som är tillgängligt i de andra vm-storlekarna.

Det här gränssnittet gör det möjligt för RDMA-kompatibla instanser att kommunicera via ett IB-nätverk (InfiniBand), som arbetar med HDR-hastigheter för HBv2, EDR-hastigheter för HB, HC, FDR-frekvenser för H16r, H16mr och virtuella N-serie-datorer i N-serien och QDR-frekvenser för virtuella datorer i A8 och A9. Dessa RDMA-funktioner kan öka skalbarheten och prestandan hos vissa MPI-program (Message Passing Interface). Mer information om hastighet finns i informationen i tabellerna på den här sidan.

> [!NOTE]
> I Azure HPC finns det två klasser av virtuella datorer beroende på om de är SR-IOV aktiverat för InfiniBand. För närvarande är SR-IOV för InfiniBand-aktiverade virtuella datorer: HBv2, HB, HC och NCv3. Resten av infiniband-aktiverade virtuella datorer är inte SR-IOV aktiverat.
> RDMA över IB stöds för alla virtuella datorer med RDMA-kapacitet.
> IP över IB stöds endast på SR-IOV-aktiverade virtuella datorer.

- **Operativsystem** - Linux är mycket väl stöd för HPC virtuella datorer, distributioner som CentOS, RHEL, Ubuntu, SUSE är vanliga. När det gäller Windows-stöd stöds Windows Server 2016 på alla virtuella datorer i HPC-serien. Windows Server 2012 R2, Windows Server 2012 stöds också på de virtuella datorerna som inte är SR-IOV-aktiverade.

- **MPI** - SR-IOV-aktiverade VM-storlekar på Azure (HBv2, HB, HC, NCv3) gör att nästan alla smaker av MPI kan användas med Mellanox OFED.
På icke-SR-IOV-aktiverade virtuella datorer använder MPI-implementeringar som stöds Microsoft Network Direct -gränssnittet (ND) för att kommunicera mellan virtuella datorer. Därför stöds endast Microsoft MPI (MS-MPI) 2012 R2 eller senare och Intel MPI 5.x-versioner. Senare versioner (2017, 2018) av Intel MPI-körningsbiblioteket kanske eller kanske inte är kompatibla med Azure RDMA-drivrutinerna.

- **InfiniBandDriver<Linux| Windows> VM-tillägg** – På virtuella datorer med RDMA lägger du till InfiniBandDriver-<Linux| Windows>-tillägg för att aktivera InfiniBand. På Linux installerar VM-tillägget InfiniBandDriverLinux mellanox OFED (på SR-IOV-datorer) för RDMA-anslutning. I Windows installerar infinibanddriverwindows-tillägget Windows Network Direct-drivrutiner (på icke-SR-IOV-datorer) eller Mellanox OFED-drivrutiner (på virtuella SR-IOV-datorer) för RDMA-anslutning.
I vissa distributioner av A8- och A9-instanser läggs HpcVmDrivers-tillägget till automatiskt. Observera att tillägget HpcVmDrivers VM håller på att inaktuell. det kommer inte att uppdateras.
Om du vill lägga till VM-tillägget till en virtuell dator kan du använda Azure PowerShell-cmdlets. [Azure PowerShell](/powershell/azure/overview) 

  Följande kommando installerar den senaste versionen 1.0 InfiniBandDriverWindows-tillägget på en befintlig virtuell dator med namnet *MYVM* som används för fjärrskrivbordsresurser i resursgruppen *myResourceGroup* i regionen *Västra USA:*

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Alternativt kan VM-tillägg inkluderas i Azure Resource Manager-mallar för enkel distribution, med följande JSON-element:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Följande kommando installerar den senaste versionen 1.0 InfiniBandDriverWindows-tillägget på alla virtuella datorer som kan användas för fjärrskrivbordsresurser i en befintlig skalauppsättning för virtuella datorer med namnet *myVMSS* som distribueras i resursgruppen *myResourceGroup:*

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Mer information finns i [Tillägg och funktioner för virtuella datorer](./extensions/overview.md). Du kan också arbeta med tillägg för virtuella datorer som distribueras i den [klassiska distributionsmodellen](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **RDMA-nätverksadressutrymme** - RDMA-nätverket i Azure reserverar adressutrymmet 172.16.0.0/16. Om du vill köra MPI-program i instanser som distribueras i ett virtuellt Azure-nätverk kontrollerar du att det virtuella nätverksadressutrymmet inte överlappar RDMA-nätverket.

## <a name="cluster-configuration-options"></a>Alternativ för klusterkonfiguration

Azure innehåller flera alternativ för att skapa kluster av virtuella windows HPC-datorer som kan kommunicera med RDMA-nätverket, inklusive: 

- **Virtuella datorer** - Distribuera HPC-virtuella datorer med FJÄRRSKRIVBORDS-kompatibel i samma skalningsuppsättning eller tillgänglighetsuppsättning (när du använder distributionsmodellen för Azure Resource Manager). Om du använder den klassiska distributionsmodellen distribuerar du de virtuella datorerna i samma molntjänst.

- **Skala uppsättningar för virtuella datorer** – I en skalningsuppsättning för virtuella datorer (VMSS) kontrollerar du att du begränsar distributionen till en enda placeringsgrupp. I en Resource Manager-mall anger `singlePlacementGroup` du `true`till exempel egenskapen till . Observera att den maximala VMSS-storleken `singlePlacementGroup` som `true` kan snurras upp med egenskapen till är begränsad till 100 virtuella datorer som standard. Om dina HPC-jobbskalningsbehov är högre än 100 virtuella datorer i en enda VMSS-klientorganisation kan du begära en ökning, [öppna en online-kundsupportbegäran](../azure-supportability/how-to-create-azure-support-request.md) utan kostnad.

- **MPI bland virtuella datorer** - Om RDMA (t.ex. med MPI-kommunikation) krävs mellan virtuella datorer (VMs), se till att de virtuella datorerna är i samma virtuella datorskalauppsättning eller tillgänglighetsuppsättning.

- **Azure CycleCloud** - Skapa ett HPC-kluster i [Azure CycleCloud](/azure/cyclecloud/) för att köra MPI-jobb.

- **Azure Batch** - Skapa en [Azure Batch-pool](/azure/batch/) för att köra MPI-arbetsbelastningar. Information om hur du använder beräkningsintensiva instanser när MPI-program körs med Azure Batch finns [i Använda MPI-program (Message Passing Interface) i Azure Batch](../batch/batch-mpi.md).

- **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) innehåller en körningsmiljö för MS-MPI som använder Azure RDMA-nätverket när det distribueras på virtuella virtuella datorer med RDMA-kompatibelt Linux. Till exempel distributioner, se [Konfigurera ett Linux RDMA-kluster med HPC Pack för att köra MPI-program](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Distributionsöverväganden

- **Azure-prenumeration** – Om du vill distribuera mer än ett fåtal beräkningsintensiva instanser bör du överväga en prenumeration med användningsbaserad betalning eller andra köpalternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

- **Priser och tillgänglighet** – Dessa VM-storlekar erbjuds endast på standardprisnivån. Kontrollera [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/) för tillgänglighet i Azure-regioner.

- **Cores-kvot** – Du kan behöva öka kärnkvoten i din Azure-prenumeration från standardvärdet. Din prenumeration kan också begränsa antalet kärnor som du kan distribuera i vissa familjer med vm-storlek, inklusive H-serien. Om du vill begära en kvotökning [öppnar du en kundsupportbegäran online](../azure-supportability/how-to-create-azure-support-request.md) utan kostnad. (Standardgränserna kan variera beroende på din prenumerationskategori.)

  > [!NOTE]
  > Kontakta Azure Support om du har stora kapacitetsbehov. Azure-kvoter är kreditgränser, inte kapacitetsgarantier. Oavsett din kvot debiteras du bara för kärnor som du använder.
  
- **Virtuellt nätverk** – Ett [virtuellt Azure-nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) krävs inte för att använda beräkningsintensiva instanser. Men för många distributioner behöver du minst ett molnbaserat virtuellt Azure-nätverk eller en plats-till-plats-anslutning om du behöver komma åt lokala resurser. Skapa ett nytt virtuellt nätverk för att distribuera instanserna när det behövs. Det går inte att lägga till beräkningsintensiva virtuella datorer i ett virtuellt nätverk i en tillhörighetsgrupp.

- **Storleksändring** – På grund av deras specialiserade maskinvara kan du bara ändra storlek på beräkningsintensiva instanser i samma storleksfamilj (H-serien eller beräkningsintensiva A-serien). Du kan till exempel bara ändra storlek på en virtuell dator i H-serien från en storlek i H-serien till en annan. Dessutom stöds inte storleksändring från en icke-beräkningsintensiv storlek till en beräkningsintensiv storlek.  


## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du optimerar ditt HPC-program för Azure och några exempel på [HPC-arbetsbelastningar] (https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 

- Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
