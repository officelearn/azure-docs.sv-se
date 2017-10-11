---
title: "Använda beräkningsintensiva virtuella Azure-datorer med Batch | Microsoft Docs"
description: Hur du drar nytta av RDMA-kompatibla eller GPU-aktiverade Virtuella datorstorlekar i Azure Batch-pooler
services: batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: danlep
ms.openlocfilehash: c52a054e4fc8f61f871acd9f35b9a3e6247e48ef
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2017
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pooler

Om du vill köra vissa batchjobb kanske du vill dra nytta av Azure VM-storlekar som utformats för storskaliga beräkning. Till exempel för att köra flera instanser [arbetsbelastningar MPI](batch-mpi.md), kan du välja A8 A9, eller H-serien storlekar som har ett nätverk gränssnitt för Remote Direct Memory Access (RDMA). Dessa storlekar ansluta till en InfiniBand-nätverk för kommunikationen mellan noder som kan påskynda MPI-program. Eller CUDA program du N-serien storlekar som innehåller NVIDIA Tesla grafikprocessorer (GPU) unit-kort.

Den här artikeln innehåller anvisningar och exempel för att använda vissa av Azures specialiserade i Batch-pooler. Specifikationer och bakgrunden finns:

* Högpresterande compute VM-storlekar ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU-aktiverade VM-storlekar ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Prenumerationen och gränser

* **Kvoter** -en eller flera Azure-kvoter kan begränsa antalet eller typen av noder som du kan lägga till en Batch-pool. Det är mer sannolikt att begränsas när du väljer RDMA-kompatibla GPU-aktiverade eller andra flera kärnor VM-storlekar. Beroende på vilken typ av Batch-kontot som du skapade kunde kvoter tillämpas på kontot i sig eller till din prenumeration.

    * Om du har skapat Batch-kontot i den **Batch-tjänsten** konfiguration, begränsas av den [dedikerade kärnor kvot per Batch-kontot](batch-quota-limit.md#resource-quotas). Som standard är den här kvoten 20 kärnor. En separat kvot gäller [VM med låg prioritet](batch-low-pri-vms.md), om du använder dem. 

    * Om du har skapat kontot i den **användarens prenumeration** konfiguration, din prenumerationsbegränsningar VM antalet kärnor per region. Se [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md). Prenumerationen gäller även en regionala kvot för vissa VM-storlekar, inklusive HPC och GPU-instanser. Inga ytterligare kvoter tillämpas på Batch-kontot i prenumerationen Användarkonfiguration. 

  Du kan behöva öka kvoter för en eller flera när du använder en särskild VM-storlek i batchen. Om du vill begära en ökning av kvoten kan du öppna ett [kundsupportärende online](../azure-supportability/how-to-create-azure-support-request.md) utan kostnad.

* **Regional tillgänglighet** - beräkningsintensiva virtuella datorer kanske inte är tillgänglig i regioner där du skapar Batch-konton. Du kan kontrollera att det finns en storlek [produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Beroenden

Beräkningsintensiva storlekar RDMA och GPU funktioner stöds endast i vissa operativsystem. Beroende på operativsystem kan behöva du installera eller konfigurera ytterligare drivrutin eller annan programvara. Följande tabeller sammanfattar dessa beroenden. Se länkade artiklar för ytterligare information. Alternativ att konfigurera Batch pooler finns senare i den här artikeln.


### <a name="linux-pools---virtual-machine-configuration"></a>Pooler för Linux - konfiguration av virtuell dator

| Storlek | Funktion | Operativsystem | Programvara som krävs | Inställningar för programpool |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r H16mr A8 A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | SUSE Linux Enterprise Server 12 HPC eller<br/>CentOS-baserade HPC<br/>(Azure Marketplace) | Intel MPI 5 | Aktivera kommunikationen mellan noder, inaktivera samtidiga uppgiftskörningen |
| [NC-serien *](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms) | NVIDIA Tesla K80 GPU | Ubuntu 16.04 LTS.<br/>Red Hat Enterprise Linux 7.3, eller<br/>CentOS-baserad 7.3<br/>(Azure Marketplace) | NVIDIA CUDA Toolkit 8.0 drivrutiner | Saknas | 
| [NV serien](../virtual-machines/linux/n-series-driver-setup.md#install-grid-drivers-for-nv-vms) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS<br/>Red Hat Enterprise Linux 7.3<br/>CentOS-baserad 7.3<br/>(Azure Marketplace) | NVIDIA RUTNÄTET 4.3 drivrutiner | Saknas |

* RDMA-anslutningar på NC24r virtuella datorer stöds på CentOS-baserade 7.3 HPC med Intel MPI.



### <a name="windows-pools---virtual-machine-configuration"></a>Pooler för Windows - konfiguration av virtuell dator

| Storlek | Funktion | Operativsystem | Programvara som krävs | Inställningar för programpool |
| -------- | ------ | -------- | -------- | ----- |
| [H16r H16mr A8 A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2 eller<br/>Windows Server 2012 (Azure Marketplace) | Microsoft MPI 2012 R2 eller senare, eller<br/> Intel MPI 5<br/><br/>HpcVMDrivers Azure VM-tillägget | Aktivera kommunikationen mellan noder, inaktivera samtidiga uppgiftskörningen |
| [NC-serien *](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla K80 GPU | Windows Server 2016 eller <br/>Windows Server 2012 R2 (Azure Marketplace) | NVIDIA Tesla drivrutiner eller CUDA Toolkit 8.0 drivrutiner| Saknas | 
| [NV serien](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 eller<br/>Windows Server 2012 R2 (Azure Marketplace) | NVIDIA RUTNÄTET 4.3 drivrutiner | Saknas |

* RDMA-anslutningar på NC24r virtuella datorer stöds i Windows Server 2012 R2 med HpcVMDrivers tillägg och Microsoft MPI eller Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Windows - pooler i Cloud services-konfiguration

> [!NOTE]
> N-serien storlekar stöds inte i Batch-pooler med cloud services-konfiguration.
>

| Storlek | Funktion | Operativsystem | Programvara som krävs | Inställningar för programpool |
| -------- | ------- | -------- | -------- | ----- |
| [H16r H16mr A8 A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2<br/>Windows Server 2012, eller<br/>Windows Server 2008 R2 (gäst-OS-familjen) | Microsoft MPI 2012 R2 eller senare, eller<br/>Intel MPI 5<br/><br/>HpcVMDrivers Azure VM-tillägget | Aktivera kommunikationen mellan noder,<br/> inaktivera samtidiga uppgiftskörningen |





## <a name="pool-configuration-options"></a>Konfigurationsalternativ för poolen

Om du vill konfigurera en särskild VM-storlek för Batch-pool tillhandahåller Batch-API: er och verktyg en flera alternativ för att installera nödvändig programvara eller drivrutiner, inklusive:

* [Aktiviteten starta](batch-api-basics.md#start-task) -överföra ett installationspaket som en resurs till ett Azure storage-konto i samma region som Batch-kontot. Skapa en starta uppgiften kommandorad för att installera resursfilen tyst när poolen startar. Mer information finns i [REST API-dokumentation](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > Startuppgiften måste köras med behörighet för utökade (admin) och den måste vänta på att lyckas.
  >

* [Programpaketet](batch-application-packages.md) – Lägg till ett komprimerade installationspaket till Batch-kontot och konfigurera en paket-referens i poolen. Den här inställningen laddar upp och därefter paketet på alla noder i poolen. Om paketet är ett installationsprogram, skapa en start uppgiften kommandoraden för att installera appen på alla noder i poolen. Du kan också installera paketet när en aktivitet är schemalagd att köras på en nod.

* [Anpassade poolen avbildningen](batch-api-basics.md#pool) – skapa en anpassad Windows eller Linux VM-avbildning som innehåller drivrutiner, program, eller andra inställningar som krävs för VM-storlek. Om du har skapat Batch-kontot i Användarkonfiguration för prenumerationen kan du ange den anpassade avbildningen för Batch-pool. (Anpassade avbildningar stöds inte i konton i konfigurationen av Batch-tjänsten.) Anpassade avbildningar kan endast användas med pooler i konfigurationen av virtuella datorn.

  > [!IMPORTANT]
  > Du kan inte använder en anpassad avbildning som skapats med hanterade diskar eller med Premium-lagring i Batch-pooler.
  >



* [Batch-skeppsvarv](https://github.com/Azure/batch-shipyard) konfigurerar automatiskt GPU och RDMA fungerar tillsammans med av arbetsbelastningar i Azure Batch. Batch skeppsvarv drivs helt med konfigurationsfiler. Det finns många exempel recept tillgängliga konfigurationer som möjliggör GPU och RDMA arbetsbelastningar, till exempel den [CNTK GPU recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) som förkonfigurerar GPU drivrutiner på N-serien virtuella datorer och läser in kognitiva Toolkit för Microsoft-programvara som en Docker-bild.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Exempel: Microsoft MPI i en A8 VM-adresspool

Om du vill köra Windows MPI program på en pool med Azure A8 noder som du behöver installera en stöds MPI-implementering. Här följer exempel steg för att installera [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) på en Windows-poolen med hjälp av ett Batch-programpaket.

1. Hämta den [installationspaketet](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) för den senaste versionen av Microsoft MPI.
2. Skapa en zip-filen i paketet.
3. Ladda upp paketet till Batch-kontot. Anvisningar finns i [programpaket](batch-application-packages.md) vägledning. Ange ett program-id som *MSMPI*, och en version som *8.1*. 
4. Skapa en pool i cloud services-konfiguration med önskat antal noder och skala med Batch-API: er eller Azure-portalen. I följande tabell visas exempel på inställningar att konfigurera MPI i obevakat läge med Startuppgiften:

| Inställning | Värde |
| ---- | ----- | 
| **Bildtyp** | Molntjänster |
| **OS-familjen** | Windows Server 2012 R2 (OS-familjen 4) |
| **Nodstorlek** | A8 Standard |
| **Dessa kommunikation aktiverad** | True |
| **Maximalt antal uppgifter per nod** | 1 |
| **Programmet paketet refererar till** | MSMPI |
| **Starta aktivitet aktiverad** | True<br>**Kommandorad** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Användaridentitet** -poolen autouser, admin<br/>**Vänta tills lyckade** – SANT

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Exempel: NVIDIA Tesla drivrutiner på NC-VM-pool

Om du vill köra CUDA program på en pool med Linux NC-noder som du behöver installera CUDA Toolkit 8.0 på noderna. Verktyget installerar nödvändiga NVIDIA Tesla GPU-drivrutiner. Här följer exempel steg för att distribuera en anpassad avbildning Ubuntu 16.04 LTS med GPU-drivrutiner:

1. Distribuera ett Azure NC6 virtuell dator som kör Ubuntu 16.04 LTS. Till exempel skapa den virtuella datorn i oss södra centrala region. Se till att du skapar den virtuella datorn med standardlagring, och *utan* hanterade diskar.
2. Följ stegen för att ansluta till den virtuella datorn och [CUDA drivrutinsinstallation](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms).
3. Ta bort etableringen av Linux-agenten och sedan avbilda Linux VM-avbildning med hjälp av Azure CLI 1.0-kommandon. Anvisningar finns [avbilda en Linux-dator som körs på Azure](../virtual-machines/linux/capture-image-nodejs.md). Anteckna avbildningen URI.
  > [!IMPORTANT]
  > Använd inte Azure CLI 2.0-kommandon för att avbilda för Azure Batch. Kommandona CLI 2.0 avbilda för närvarande endast virtuella datorer som skapades med hjälp av hanterade diskar.
  >
4. Skapa ett Batch-konto med prenumerationen Användarkonfiguration, i en region som stöder NC virtuella datorer.
5. Med hjälp av Batch-API: er eller Azure portal, skapa en pool med den anpassade avbildningen och med önskat antal noder och skala. I följande tabell visas exempel processpool-inställningar för avbildningen:

| Inställning | Värde |
| ---- | ---- |
| **Bildtyp** | Anpassad avbildning |
| **Anpassad avbildning** | Bild URI i formuläret`https://yourstorageaccountdisks.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` |
| **Noden agent SKU** | batch.node.Ubuntu 16.04 |
| **Nodstorlek** | NC6 Standard |



## <a name="next-steps"></a>Nästa steg

* Om du vill köra MPI-jobb på Azure Batch-pool finns på [Windows](batch-mpi.md) eller [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) exempel.

* Exempel på GPU arbetsbelastningar på Batch finns i [Batch skeppsvarv](https://github.com/Azure/batch-shipyard/) recept.