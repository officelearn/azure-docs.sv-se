---
title: Använda beräkningsintensiva virtuella Azure-datorer med Batch | Microsoft Docs
description: Hur du kan utnyttja fördelarna med HPC och GPU VM-storlekar i Azure Batch-pooler
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: 4663cb467d7d1d864425f4899c829b6618b9547c
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584470"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Använda RDMA eller GPU-instanser i Batch-pooler

Om du vill köra vissa Batch-jobb kan du dra nytta av Azure VM-storlekar som är utformad för storskaliga beräkning. Exempel:

* Att köra flera instanser [MPI arbetsbelastningar](batch-mpi.md), Välj H-serien eller andra storlekar som har ett nätverksgränssnitt för direktåtkomst minne till (RDMA). Dessa storlekar ansluta till ett InfiniBand-nätverk för kommunikation mellan noder, som kan påskynda MPI-program. 

* För CUDA-program, väljer du storlekar för N-serien med NVIDIA Tesla grafikprocessorer (GPU) unit-kort.

Den här artikeln ger vägledning och exempel för att använda en del av Azures specialiserade storlekar i Batch-pooler. Specifikationer och bakgrundsinformation finns:

* Högpresterande compute VM-storlekar ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU-aktiverade VM-storlekar ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Vissa storlekar kanske inte är tillgängliga i de regioner där du skapar Batch-konton. Du kan kontrollera att en storlek är tillgänglig [produkttillgänglighet per region](https://azure.microsoft.com/regions/services/) och [välja en VM-storlek för en Batch-pool](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Beroenden

Beräkningsintensiva storlekar i Batch RDMA eller GPU-funktioner stöds endast i vissa operativsystem. (Listan över operativsystem som stöds är en delmängd av de som stöds för virtuella datorer som skapats i dessa storlekar.) Beroende på hur du skapar Batch-pool, kan du behöva installera eller konfigurera ytterligare drivrutin eller annan programvara på noderna. Följande tabeller sammanfattar dessa beroenden. Se länkade artiklarna för mer information. Alternativ för att konfigurera Batch-pooler, finns senare i den här artikeln.

### <a name="linux-pools---virtual-machine-configuration"></a>Pooler för Linux - konfiguration av virtuell dator

| Storlek | Funktion | Operativsystem | Programvara som krävs | Poolinställningar |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, eller<br/>CentOS-baserade HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux RDMA-drivrutiner | Aktivera kommunikation mellan noder, inaktivera för körning av samtidiga aktiviteten |
| [NC, NCv2, NCv3, NDv2-serien](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (varierar beroende på serien) | Ubuntu 16.04 LTS, eller<br/>CentOS 7.3 eller 7.4<br/>(Azure Marketplace) | NVIDIA CUDA eller CUDA Toolkit drivrutiner | Gäller inte | 
| [NV NVv2-serien](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, eller<br/>CentOS 7.3<br/>(Azure Marketplace) | NVIDIA GRID-drivrutiner | Gäller inte |
<sup>*</sup>RDMA-kompatibla storlekar för N-serien även innehålla NVIDIA Tesla GPU: er

### <a name="windows-pools---virtual-machine-configuration"></a>Windows-pooler – konfiguration av virtuell dator

| Storlek | Funktion | Operativsystem | Programvara som krävs | Poolinställningar |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2, eller<br/>2012 (azure Marketplace) | Microsoft MPI 2012 R2 eller senare, eller<br/> Intel MPI 5<br/><br/>Windows RDMA-drivrutiner | Aktivera kommunikation mellan noder, inaktivera för körning av samtidiga aktiviteten |
| [NC, NCv2, NCv3, ND, NDv2-serien](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (varierar beroende på serien) | Windows Server 2016 eller <br/>2012 R2 (Azure Marketplace) | NVIDIA CUDA eller CUDA Toolkit drivrutiner| Gäller inte | 
| [NV NVv2-serien](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 eller<br/>2012 R2 (Azure Marketplace) | NVIDIA GRID-drivrutiner | Gäller inte |
<sup>*</sup>RDMA-kompatibla storlekar för N-serien även innehålla NVIDIA Tesla GPU: er

### <a name="windows-pools---cloud-services-configuration"></a>Windows-pooler – Cloud services-konfiguration

> [!NOTE]
> Storlekar för N-serien stöds inte i Batch-pooler med Cloud Service-konfiguration.
>

| Storlek | Funktion | Operativsystem | Programvara som krävs | Poolinställningar |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012, eller<br/>2008 R2 (gästoperativsystemfamilj) | Microsoft MPI 2012 R2 eller senare, eller<br/>Intel MPI 5<br/><br/>Windows RDMA-drivrutiner | Aktivera kommunikation mellan noder<br/> inaktivera för körning av samtidiga aktiviteten |

## <a name="pool-configuration-options"></a>Pool-konfigurationsalternativ

Om du vill konfigurera en specialiserad virtuell datorstorlek för Batch-poolen, har du flera alternativ för att installera nödvändig programvara eller drivrutiner:

* För pooler i den virtuella datorkonfigurationen, Välj en förkonfigurerad [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) VM-avbildning som har drivrutiner och programvara installerad. Exempel: 

  * [CentOS-baserade 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) -innehåller drivrutiner för RDMA och Intel MPI 5.1

  * [Virtuell dator för datavetenskap](../machine-learning/data-science-virtual-machine/overview.md) för Linux eller Windows - inkluderar NVIDIA CUDA-drivrutiner

  * Linux-avbildningar för Batch-behållararbetsbelastningar som också innehåller GPU- och RDMA-drivrutiner:

    * [CentOS (med GPU- och RDMA-drivrutiner) för Azure Batch-pooler för behållare](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu-Server (med GPU- och RDMA-drivrutiner) för Azure Batch-pooler för behållare](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Skapa en [anpassade Windows eller Linux VM-avbildning](batch-custom-images.md) på som du har installerat drivrutiner, programvara eller andra inställningar som krävs för virtuella datorstorlek. 

* Skapa en Batch [programpaket](batch-application-packages.md) från en komprimerade drivrutin eller installationsprogrammet för programmet, och konfigurera Batch så att distribuera paketet till poolnoder och installera när när alla noder har skapats. Till exempel om programpaketet är ett installationsprogram, skapa en [startaktivitet](batch-api-basics.md#start-task) kommandoraden för att installera appen på alla poolnoder. Överväg att använda ett programpaket och en pool startaktivitet om din arbetsbelastning är beroende av en viss drivrutin-version.

  > [!NOTE] 
  > Startuppgiften måste köras med behörighet för utökad (admin) och den måste vänta tills lyckades. Långvariga aktiviteter ökar den tid att etablera en Batch-pool.
  >

* [Batch-skeppsvarv](https://github.com/Azure/batch-shipyard) konfigurerar automatiskt de GPU- och RDMA drivrutinerna från fungerar tillsammans med arbetsbelastningar i behållare på Azure Batch. Batch skeppsvarv drivs helt och hållet med konfigurationsfiler. Det finns många exempel recept konfigurationer som möjliggör GPU- och RDMA-arbetsbelastningar, till exempel den [CNTK GPU recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) som förkonfigurerat GPU-drivrutiner på virtuella datorer i N-serien och läser in Microsoft Cognitive Toolkit-programvara som en Docker-avbildning.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Exempel: NVIDIA GPU-drivrutiner på Windows NC VM-pool

Du måste installera NVDIA GPU-drivrutiner för att köra CUDA-program i en pool med Windows NC-noder. Följande exempel använder ett programpaket för att installera NVIDIA GPU-drivrutiner. Du kan välja det här alternativet om din arbetsbelastning är beroende av en specifik GPU-drivrutinsversion.

1. Hämta en installationspaketet för GPU-drivrutiner på Windows Server 2016 från den [NVIDIA-webbplatsen](http://us.download.nvidia.com/Windows/) , till exempel [version 411.82](http://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Spara filen lokalt med hjälp av ett kort namn som liknar *GPUDriverSetup.exe*.
2. Skapa en zip-fil av paketet.
3. Ladda upp paketet till Batch-kontot. Anvisningar finns i den [programpaket](batch-application-packages.md) vägledning. Ange en program-id som *GPUDriver*, och en version som *411.82*.
1. Skapa en pool i konfigurationen av virtuella datorn med det önskade antalet noder och skala med Batch-API: er eller Azure-portalen. I följande tabell visas exempel på inställningar att installera NVIDIA GPU-drivrutiner tyst med hjälp av en startaktivitet:

| Inställning | Värde |
| ---- | ----- | 
| **Avbildningstyp** | Marketplace (Linux/Windows) |
| **Utgivare** | MicrosoftWindowsServer |
| **Erbjudande** | WindowsServer |
| **Sku** | 2016-Datacenter |
| **Nodstorlek** | Standard för NC6 |
| **Referenser för programpaket** | GPUDriver, version 411.82 |
| **Startaktivitet aktiverat** | True<br>**Från kommandoraden** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Användaridentitet** -Pool autouser, admin<br/>**Vänta tills lyckades** – SANT

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Exempel: NVIDIA GPU-drivrutiner på en Linux NC VM-pool

Om du vill köra CUDA-program i en pool med Linux NC-noder som du behöver installera NVIDIA Tesla GPU-drivrutiner som krävs från CUDA Toolkit. Följande exempel skapar och distribuerar en anpassad Ubuntu 16.04 LTS-avbildning med GPU-drivrutiner:

1. Distribuera en Azure VM för NC-serien som kör Ubuntu 16.04 LTS. Till exempel skapa den virtuella datorn i regionen södra centrala USA. 
2. Lägg till den [NVIDIA GPU-drivrutiner tillägget](../virtual-machines/extensions/hpccompute-gpu-linux.md
) till den virtuella datorn med hjälp av Azure portal och en klientdator som ansluter till Azure-prenumeration eller Azure Cloud Shell. Du kan också följa stegen för att ansluta till den virtuella datorn och [CUDA drivrutinsinstallation](../virtual-machines/linux/n-series-driver-setup.md) manuellt.
3. Följ stegen för att skapa en [ögonblicksbild och anpassade Linux VM-avbildning](batch-custom-images.md) för Batch.
4. Skapa ett Batch-konto i en region som har stöd för Nätverksstyrenhetens virtuella datorer.
5. Med hjälp av Batch-API: er eller Azure-portalen och skapa en pool [med hjälp av den anpassade avbildningen](batch-custom-images.md) och med det önskade antalet noder och skala. I följande tabell visas exempel processpool-inställningar för avbildningen:

| Inställning | Värde |
| ---- | ---- |
| **Avbildningstyp** | Anpassad avbildning |
| **Anpassad avbildning** | *Namnet på avbildningen* |
| **Nodagentens SKU** | batch.node.Ubuntu 16.04 |
| **Nodstorlek** | Standard för NC6 |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Exempel: Microsoft MPI på en Windows H16r VM-pool

Om du vill köra Windows MPI-program i en pool med Azure H16r VM-noder måste du konfigurera HpcVmDrivers-tillägget och installera [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Här följer exempel steg för att distribuera en anpassad Windows Server 2016-avbildning med programvara och drivrutiner som krävs:

1. Distribuera en Azure H16r-dator som kör Windows Server 2016. Till exempel skapa den virtuella datorn i regionen västra USA. 
2. Lägg till HpcVmDrivers tillägget till den virtuella datorn genom att [som kör ett Azure PowerShell-kommando](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) från en klientdator som ansluter till din Azure-prenumeration eller använda Azure Cloud Shell. 
1. Se en fjärrskrivbordsanslutning till den virtuella datorn.
1. Ladda ned den [installationspaketet](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) för den senaste versionen av Microsoft MPI och installera Microsoft MPI.
1. Följ stegen för att skapa en [ögonblicksbild och anpassade Windows VM-avbildning](batch-custom-images.md) för Batch.
1. Med hjälp av Batch-API: er eller Azure-portalen och skapa en pool [med hjälp av den anpassade avbildningen](batch-custom-images.md) och med det önskade antalet noder och skala. I följande tabell visas exempel processpool-inställningar för avbildningen:

| Inställning | Värde |
| ---- | ---- |
| **Avbildningstyp** | Anpassad avbildning |
| **Anpassad avbildning** | *Namnet på avbildningen* |
| **Nodagentens SKU** | batch.node.Windows amd64 |
| **Nodstorlek** | H16r Standard |
| **Kommunikation mellan aktiverat** | True |
| **Högsta antal aktiviteter per nod** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Exempel: Intel MPI på en Linux H16r VM-pool

För att köra MPI-program i en pool med Linux H-serien noder, ett alternativ är att använda den [CentOS-baserade 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) avbildning från Azure Marketplace. Linux RDMA-drivrutiner och Intel MPI är förinstallerade. Den här avbildningen har även stöd för Docker-behållararbetsbelastningar.

Med hjälp av Batch-API: er eller Azure-portalen och skapa en pool med den här avbildningen och med det önskade antalet noder och skala. I följande tabell visas exempel poolinställningarna:

| Inställning | Värde |
| ---- | ---- |
| **Avbildningstyp** | Marketplace (Linux/Windows) |
| **Utgivare** | OpenLogic |
| **Erbjudande** | CentOS-HPC |
| **Sku** | 7.4 |
| **Nodstorlek** | H16r Standard |
| **Kommunikation mellan aktiverat** | True |
| **Högsta antal aktiviteter per nod** | 1 |

## <a name="next-steps"></a>Nästa steg

* För att köra MPI-jobb på en Azure Batch-pool, se den [Windows](batch-mpi.md) eller [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) exempel.

* Exempel på GPU-arbetsbelastningar på Batch finns i [Batch skeppsvarv](https://github.com/Azure/batch-shipyard/) recept.