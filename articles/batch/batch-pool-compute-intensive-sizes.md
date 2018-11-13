---
title: Använda beräkningsintensiva virtuella Azure-datorer med Batch | Microsoft Docs
description: Hur du drar nytta av RDMA-kompatibla eller GPU-aktiverade Virtuella datorstorlekar i Azure Batch-pooler
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: 6969f0c6a05ebf5b34fb746d2a83b884687ad710
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258263"
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Använda RDMA-kompatibla GPU-aktiverade instanser i Batch-pooler

För att köra vissa batchjobb, kanske du vill dra nytta av Azure VM-storlekar som är utformad för storskaliga beräkning. Till exempel för att köra flera instanser [MPI arbetsbelastningar](batch-mpi.md), du kan välja A8, A9, eller storlekarna i H-serien som har ett nätverk gränssnitt för direktåtkomst minne till (RDMA). Dessa storlekar ansluta till ett InfiniBand-nätverk för kommunikation mellan noder, som kan påskynda MPI-program. Eller CUDA program du storlekar för N-serien med NVIDIA Tesla grafikprocessorer (GPU) unit-kort.

Den här artikeln ger vägledning och exempel för att använda en del av Azures specialiserade storlekar i Batch-pooler. Specifikationer och bakgrundsinformation finns:

* Högpresterande compute VM-storlekar ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU-aktiverade VM-storlekar ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Prenumeration och begränsningar

* **Kvoter och begränsningar** – [kärnkvot per Batch-konto](batch-quota-limit.md#resource-quotas) kan begränsa antalet noder i en given storlek som du kan lägga till en Batch-pool. Det är mer sannolikt att nå en kvot när du väljer RDMA-kompatibla GPU-aktiverade eller andra flerkärniga VM-storlekar. 

  Dessutom är användningen av vissa VM-serier i Batch-kontot, till exempel NCv2, NCv3 och ND, begränsade på grund av begränsad kapacitet. Användning av dessa grupper finns bara genom att begära en kvot från standardvärdet 0 kärnor.  

  Om du behöver [öka kvoten](batch-quota-limit.md#increase-a-quota) utan kostnad.

* **Regiontillgänglighet** – beräkningsintensiva virtuella datorer kanske inte är tillgängliga i regioner där du skapar Batch-konton. Du kan kontrollera att en storlek är tillgänglig [produkttillgänglighet per region](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Beroenden

Beräkningsintensiva storlekar RDMA och GPU-funktioner stöds endast i vissa operativsystem. Beroende på ditt operativsystem, kan du behöva installera eller konfigurera ytterligare drivrutin eller annan programvara. Följande tabeller sammanfattar dessa beroenden. Se länkade artiklarna för mer information. Alternativ för att konfigurera Batch-pooler, finns senare i den här artikeln.


### <a name="linux-pools---virtual-machine-configuration"></a>Pooler för Linux - konfiguration av virtuell dator

| Storlek | Funktion | Operativsystem | Programvara som krävs | Inställningar för lagringspool |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS,<br/>SUSE Linux Enterprise Server 12 HPC, eller<br/>CentOS-baserade HPC<br/>(Azure Marketplace) | Intel MPI 5 | Aktivera kommunikation mellan noder, inaktivera för körning av samtidiga aktiviteten |
| [NC, NCv2, NCv3, ND-serien *](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (varierar beroende på serien) | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3 eller 7.4, eller<br/>CentOS 7.3 eller 7.4<br/>(Azure Marketplace) | NVIDIA CUDA Toolkit drivrutiner | Gäller inte | 
| [NV-serien](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3, eller<br/>CentOS 7.3<br/>(Azure Marketplace) | NVIDIA GRID-drivrutiner | Gäller inte |

* RDMA-anslutning på RDMA-kompatibla N-serien virtuella datorer kan kräva [ytterligare konfiguration](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) som varierar beroende på distributionsplatsen.



### <a name="windows-pools---virtual-machine-configuration"></a>Windows-pooler – konfiguration av virtuell dator

| Storlek | Funktion | Operativsystem | Programvara som krävs | Inställningar för lagringspool |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, eller<br/>2012 (azure Marketplace) | Microsoft MPI 2012 R2 eller senare, eller<br/> Intel MPI 5<br/><br/>HpcVMDrivers Azure VM-tillägg | Aktivera kommunikation mellan noder, inaktivera för körning av samtidiga aktiviteten |
| [NC, NCv2, NCv3, ND-serien *](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (varierar beroende på serien) | Windows Server 2016 eller <br/>2012 R2 (Azure Marketplace) | NVIDIA Tesla-drivrutiner eller CUDA Toolkit drivrutiner| Gäller inte | 
| [NV-serien](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 eller<br/>2012 R2 (Azure Marketplace) | NVIDIA GRID-drivrutiner | Gäller inte |

* RDMA-anslutning på RDMA-kompatibla N-serien virtuella datorer stöds på Windows Server 2016 eller Windows Server 2012 R2 (från Azure Marketplace) med tillägget HpcVMDrivers och Microsoft MPI eller Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Windows-pooler – Cloud services-konfiguration

> [!NOTE]
> Storlekar för N-serien stöds inte i Batch-pooler med Cloud Service-konfiguration.
>

| Storlek | Funktion | Operativsystem | Programvara som krävs | Inställningar för lagringspool |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012, eller<br/>2008 R2 (gästoperativsystemfamilj) | Microsoft MPI 2012 R2 eller senare, eller<br/>Intel MPI 5<br/><br/>HpcVMDrivers Azure VM-tillägg | Aktivera kommunikation mellan noder<br/> inaktivera för körning av samtidiga aktiviteten |





## <a name="pool-configuration-options"></a>Pool-konfigurationsalternativ

För att konfigurera en specialiserad virtuell datorstorlek för Batch-poolen, anger Batch-API: er och verktyg du flera alternativ för att installera nödvändig programvara eller drivrutiner, inklusive:

* [Startaktivitet](batch-api-basics.md#start-task) – ladda upp ett installationspaket som en resurs till en Azure-lagringskonto i samma region som Batch-kontot. Skapa en start aktivitetens kommandorad för att installera resursfilen tyst när poolen startar. Mer information finns i [dokumentationen om REST API](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > Startuppgiften måste köras med behörighet för utökad (admin) och den måste vänta tills lyckades.
  >

* [Programpaket](batch-application-packages.md) – lägga till ett komprimerade installationspaket till Batch-kontot och konfigurera en paketreferens i poolen. Den här inställningen laddar upp och därefter paketet på alla noder i poolen. Om paketet är ett installationsprogram, skapar du en kommandorad för start-uppgift för obevakad installation av appen på alla poolnoder. Du kan också installera paketet när en aktivitet är schemalagd att köras på en nod.

* [Anpassad pool bild](batch-custom-images.md) – skapa en anpassad Windows eller Linux VM avbildning som innehåller drivrutiner, programvara, eller andra inställningar som krävs för virtuella datorstorlek. 

* [Batch-skeppsvarv](https://github.com/Azure/batch-shipyard) konfigurerar automatiskt GPU- och RDMA till fungerar tillsammans med arbetsbelastningar i behållare på Azure Batch. Batch skeppsvarv drivs helt och hållet med konfigurationsfiler. Det finns många exempel recept konfigurationer som möjliggör GPU- och RDMA-arbetsbelastningar, till exempel den [CNTK GPU recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) som förkonfigurerat GPU-drivrutiner på virtuella datorer i N-serien och läser in Microsoft Cognitive Toolkit-programvara som en Docker-avbildning.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Exempel: Microsoft MPI på en A8 VM-pool

Om du vill köra Windows MPI-program i en pool med Azure A8 noder som du behöver installera en stöds MPI-implementering. Här följer exempel steg för att installera [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) på en Windows-poolen med hjälp av ett Batch-programpaket.

1. Ladda ned den [installationspaketet](https://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) för den senaste versionen av Microsoft MPI.
2. Skapa en zip-fil av paketet.
3. Ladda upp paketet till Batch-kontot. Anvisningar finns i den [programpaket](batch-application-packages.md) vägledning. Ange en program-id som *MSMPI*, och en version som *8.1*. 
4. Skapa en pool i cloud services-konfiguration med det önskade antalet noder och skala med Batch-API: er eller Azure-portalen. I följande tabell visas exempel på inställningar att konfigurera MPI i obevakat läge med hjälp av en startaktivitet:

| Inställning | Värde |
| ---- | ----- | 
| **Avbildningstyp** | Cloud Services |
| **OS-familj** | Windows Server 2012 R2 (OS-familj 4) |
| **Nodstorlek** | A8-Standard |
| **Kommunikation mellan aktiverat** | True |
| **Högsta antal aktiviteter per nod** | 1 |
| **Referenser för programpaket** | MSMPI |
| **Startaktivitet aktiverat** | True<br>**Från kommandoraden** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Användaridentitet** -Pool autouser, admin<br/>**Vänta tills lyckades** – SANT

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Exempel: NVIDIA Tesla-drivrutiner på NC VM-pool

Om du vill köra CUDA-program i en pool med Linux NC-noder som du behöver installera CUDA Toolkit 9.0 på noderna. Verktyget installerar nödvändiga NVIDIA Tesla GPU-drivrutiner. Här följer exempel steg för att distribuera en anpassad Ubuntu 16.04 LTS-avbildning med GPU-drivrutiner:

1. Distribuera en Azure VM för NC-serien som kör Ubuntu 16.04 LTS. Till exempel skapa den virtuella datorn i regionen södra centrala USA. Se till att du skapar den virtuella datorn med en hanterad disk.
2. Följ stegen för att ansluta till den virtuella datorn och [CUDA drivrutinsinstallation](../virtual-machines/linux/n-series-driver-setup.md).
3. Ta bort etableringen av Linux-agenten och sedan [på Linux VM-avbildning](../virtual-machines/linux/capture-image.md).
4. Skapa ett Batch-konto i en region som har stöd för Nätverksstyrenhetens virtuella datorer.
5. Med hjälp av Batch-API: er eller Azure-portalen och skapa en pool [med hjälp av den anpassade avbildningen](batch-custom-images.md) och med det önskade antalet noder och skala. I följande tabell visas exempel processpool-inställningar för avbildningen:

| Inställning | Värde |
| ---- | ---- |
| **Avbildningstyp** | Anpassad avbildning |
| **Anpassad avbildning** | Namnet på avbildningen |
| **Nodagentens SKU** | batch.node.Ubuntu 16.04 |
| **Nodstorlek** | Standard för NC6 |



## <a name="next-steps"></a>Nästa steg

* För att köra MPI-jobb på en Azure Batch-pool, se den [Windows](batch-mpi.md) eller [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) exempel.

* Exempel på GPU-arbetsbelastningar på Batch finns i [Batch skeppsvarv](https://github.com/Azure/batch-shipyard/) recept.