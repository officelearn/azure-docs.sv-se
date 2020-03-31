---
title: Använda beräkningsintensiva virtuella Azure-datorer med batch
description: Hur du drar nytta av storlekarna för virtuella HPC- och GPU-datorer i Azure Batch-pooler. Lär dig mer om OS-beroenden och se flera scenarioexempel.
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/17/2018
ms.author: labrenne
ms.openlocfilehash: d0238e0b5b882748218835d7f06a147d435a9f90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245063"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Använda RDMA- eller GPU-instanser i batchpooler

Om du vill köra vissa batchjobb kan du dra nytta av Azure VM-storlekar som utformats för storskalig beräkning. Ett exempel:

* Om du vill köra [MPI-arbetsbelastningar](batch-mpi.md)med flera instanser väljer du H-serien eller andra storlekar som har ett nätverksgränssnitt för RDMA (Remote Direct Memory Access). Dessa storlekar ansluter till ett InfiniBand-nätverk för kommunikation mellan nod, vilket kan påskynda MPI-program. 

* För CUDA-program väljer du storlekar i N-serien som inkluderar GPU-kort (NVIDIA Tesla graphics processing unit).

Den här artikeln innehåller vägledning och exempel för att använda några av Azures specialiserade storlekar i batchpooler. För specifikationer och bakgrund, se:

* Hög prestanda beräkna VM storlekar ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU-aktiverade VM-storlekar ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Vissa vm-storlekar kanske inte är tillgängliga i de regioner där du skapar dina batchkonton. Information om att kontrollera att en storlek är tillgänglig finns i Produkter som är [tillgängliga efter region](https://azure.microsoft.com/regions/services/) och Välj en [vm-storlek för en batchpool](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Beroenden

RDMA- eller GPU-funktionerna för beräkningsintensiva storlekar i Batch stöds endast i vissa operativsystem. (Listan över operativsystem som stöds är en delmängd av dem som stöds för virtuella datorer som skapats i dessa storlekar.) Beroende på hur du skapar batchpoolen kan du behöva installera eller konfigurera ytterligare drivrutin eller annan programvara på noderna. I följande tabeller sammanfattas dessa beroenden. Mer information finns i länkade artiklar. Alternativ för att konfigurera batchpooler finns i senare i den här artikeln.

### <a name="linux-pools---virtual-machine-configuration"></a>Linux-pooler - Konfiguration av virtuella datorer

| Storlek | Funktion | Operativsystem | Programvara som krävs | Poolinställningar |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, eller<br/>CentOS-baserade HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux RDMA-drivrutiner | Aktivera kommunikation mellan nod, inaktivera samtidig körning av uppgifter |
| [NC, NCv2, NCv3, NDv2-serien](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (varierar mellan olika serier) | Ubuntu 16.04 LTS, eller<br/>CentOS 7,3 eller 7,4<br/>(Azure Marketplace) | NVIDIA CUDA- eller CUDA Toolkit-drivrutiner | Ej tillämpligt | 
| [NV, NVv2-serien](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, eller<br/>CentOS 7,3<br/>(Azure Marketplace) | NVIDIA GRID-drivrutiner | Ej tillämpligt |

<sup>*</sup>RDMA-kompatibla N-serien storlekar inkluderar också NVIDIA Tesla GPU: er

### <a name="windows-pools---virtual-machine-configuration"></a>Windows-pooler – konfiguration av virtuella datorer

| Storlek | Funktion | Operativsystem | Programvara som krävs | Poolinställningar |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 eller<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 eller senare, eller<br/> Intel MPI 5<br/><br/>Drivrutiner för Windows RDMA | Aktivera kommunikation mellan nod, inaktivera samtidig körning av uppgifter |
| [NC, NCv2, NCv3, ND, NDv2-serien](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (varierar mellan olika serier) | Windows Server 2016 eller <br/>2012 R2 (Azure Marketplace) | NVIDIA CUDA- eller CUDA Toolkit-drivrutiner| Ej tillämpligt | 
| [NV, NVv2-serien](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 eller<br/>2012 R2 (Azure Marketplace) | NVIDIA GRID-drivrutiner | Ej tillämpligt |

<sup>*</sup>RDMA-kompatibla N-serien storlekar inkluderar också NVIDIA Tesla GPU: er

### <a name="windows-pools---cloud-services-configuration"></a>Windows-pooler - Konfiguration av molntjänster

> [!NOTE]
> N-seriens storlekar stöds inte i batchpooler med Cloud Service-konfigurationen.
>

| Storlek | Funktion | Operativsystem | Programvara som krävs | Poolinställningar |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | RDMA | Windows Server 2016, 2012 R2, 2012 eller<br/>2008 R2 (Gäst OS familj) | Microsoft MPI 2012 R2 eller senare, eller<br/>Intel MPI 5<br/><br/>Drivrutiner för Windows RDMA | Aktivera kommunikation mellan nod,<br/> inaktivera samtidig körning av uppgifter |

## <a name="pool-configuration-options"></a>Konfigurationsalternativ för pool

Om du vill konfigurera en specialiserad VM-storlek för batchpoolen har du flera alternativ för att installera nödvändig programvara eller drivrutiner:

* För pooler i konfigurationen för virtuella datorer väljer du en förkonfigurerad [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) VM-avbildning som har drivrutiner och programvara förinstallerad. Exempel: 

  * [CentOS-baserade 7,4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) - inkluderar RDMA-drivrutiner och Intel MPI 5.1

  * [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) för Linux eller Windows - innehåller NVIDIA CUDA-drivrutiner

  * Linux-avbildningar för batchcontainerarbetsbelastningar som även innehåller GPU- och RDMA-drivrutiner:

    * [CentOS (med GPU- och RDMA-drivrutiner) för Azure Batch-behållarpooler](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (med GPU- och RDMA-drivrutiner) för Azure Batch-behållarpooler](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Skapa en [anpassad Windows- eller Linux VM-avbildning](batch-sig-images.md) där du har installerat drivrutiner, programvara eller andra inställningar som krävs för vm-storleken. 

* Skapa ett [batchprogrampaket](batch-application-packages.md) från en drivrutin eller programinstallationsprogram med zippade drivrutiner och konfigurera Batch för att distribuera paketet till poolnoder och installera en gång när varje nod skapas. Om programpaketet till exempel är en installatör skapar du en [startuppgiftskommandorad](batch-api-basics.md#start-task) för att tyst installera appen på alla poolnoder. Överväg att använda ett programpaket och en poolstartuppgift om arbetsbelastningen är beroende av en viss drivrutinsversion.

  > [!NOTE] 
  > Startaktiviteten måste köras med förhöjda (admin) behörigheter och den måste vänta på att lyckas. Tidskrävande aktiviteter ökar tiden för att etablera en batchpool.
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) konfigurerar automatiskt GPU- och RDMA-drivrutinerna så att de fungerar transparent med behållare arbetsbelastningar på Azure Batch. Batch Shipyard drivs helt med konfigurationsfiler. Det finns många exempel recept konfigurationer tillgängliga som möjliggör GPU och RDMA arbetsbelastningar såsom [CNTK GPU Recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) som förkonfigurerar GPU-drivrutiner på N-serien virtuella datorer och laddar Microsoft Cognitive Toolkit programvara som en Docker-avbildning.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Exempel: NVIDIA GPU-drivrutiner på Windows NC VM-pool

Om du vill köra CUDA-program på en pool med Windows NC-noder måste du installera NVDIA GPU-drivrutiner. Följande exempelsteg använder ett programpaket för att installera NVIDIA GPU-drivrutinerna. Du kan välja det här alternativet om din arbetsbelastning beror på en specifik GPU-drivrutinsversion.

1. Ladda ner ett installationspaket för GPU-drivrutinerna på Windows Server 2016 från [NVIDIA:s webbplats](https://www.nvidia.com/Download/index.aspx) – till exempel [version 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Spara filen lokalt med ett kort namn som *GPUDriverSetup.exe*.
2. Skapa en zip-fil i paketet.
3. Ladda upp paketet till ditt Batch-konto. Steg om du vill göra några steg finns i vägledningen för [programpaket.](batch-application-packages.md) Ange ett program-ID som *GPUDriver*och en version som *411.82*.
1. Med hjälp av batch-API:er eller Azure-portalen skapar du en pool i konfigurationen för den virtuella datorn med önskat antal noder och skala. I följande tabell visas exempelinställningar för att installera NVIDIA GPU-drivrutinerna tyst med hjälp av en startuppgift:

| Inställning | Värde |
| ---- | ----- | 
| **Avbildningstyp** | Marketplace (Linux/Windows) |
| **Publisher** | MicrosoftWindowsServer |
| **Erbjuder** | WindowsServer |
| **Sku** | 2016-Datacenter |
| **Nodstorlek** | NC6 Standard |
| **Referenser till programpaketet** | GPUDriver, version 411.82 |
| **Starta aktivitet aktiverad** | True<br>**Kommandoraden** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Användaridentitet** - Biljard, admin<br/>**Vänta på framgång** - Sant

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Exempel: NVIDIA GPU-drivrutiner på en Linux NC VM-pool

För att köra CUDA-program på en pool av Linux NC-noder måste du installera nödvändiga NVIDIA Tesla GPU-drivrutiner från CUDA Toolkit. Följande exempelsteg skapar och distribuerar en anpassad Ubuntu 16.04 LTS-avbildning med GPU-drivrutinerna:

1. Distribuera en virtuell dator i Azure NC-serien som kör Ubuntu 16.04 LTS. Skapa till exempel den virtuella datorn i regionen Södra centralen i USA. 
2. Lägg till [NVIDIA GPU-drivrutiner till](../virtual-machines/extensions/hpccompute-gpu-linux.md
) den virtuella datorn med hjälp av Azure-portalen, en klientdator som ansluter till Azure-prenumerationen eller Azure Cloud Shell. Du kan också följa stegen för att ansluta till den virtuella datorn och [installera CUDA-drivrutiner](../virtual-machines/linux/n-series-driver-setup.md) manuellt.
3. Följ stegen för att skapa en [bild av det delade bildgalleriet](batch-sig-images.md) för Batch.
4. Skapa ett batchkonto i en region som stöder virtuella NC-datorer.
5. Med hjälp av batch-API:er eller Azure-portalen skapar du en pool [med den anpassade avbildningen](batch-sig-images.md) och med önskat antal noder och skala. I följande tabell visas exempelpoolinställningar för bilden:

| Inställning | Värde |
| ---- | ---- |
| **Avbildningstyp** | Anpassad avbildning |
| **Anpassad bild** | *Bildens namn* |
| **Nodagent SKU** | batch.node.ubuntu 16,04 |
| **Nodstorlek** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Exempel: Microsoft MPI i en VIRTUELL Windows H16r-pool

Om du vill köra Windows MPI-program i en pool med virtuella Azure H16r-noder för virtuella datorer måste du konfigurera HpcVmDrivers-tillägget och installera [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Här är exempelsteg för att distribuera en anpassad Windows Server 2016-avbildning med nödvändiga drivrutiner och programvara:

1. Distribuera en virtuell Azure H16r-dator som kör Windows Server 2016. Skapa till exempel den virtuella datorn i regionen USA väst. 
2. Lägg till HpcVmDrivers-tillägget till den virtuella datorn genom att [köra ett Azure PowerShell-kommando](../virtual-machines/sizes-hpc.md) från en klientdator som ansluter till din Azure-prenumeration eller med Azure Cloud Shell. 
1. Upprätta en anslutning till den virtuella datorn.
1. Hämta [installationspaketet](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) för den senaste versionen av Microsoft MPI och installera Microsoft MPI.
1. Följ stegen för att skapa en [bild av det delade bildgalleriet](batch-sig-images.md) för Batch.
1. Med hjälp av batch-API:er eller Azure-portalen skapar du en pool [med det delade avbildningsgalleriet](batch-sig-images.md) och med önskat antal noder och skala. I följande tabell visas exempelpoolinställningar för bilden:

| Inställning | Värde |
| ---- | ---- |
| **Avbildningstyp** | Anpassad avbildning |
| **Anpassad bild** | *Bildens namn* |
| **Nodagent SKU** | batch.node.windows amd64 |
| **Nodstorlek** | H16r Standard |
| **Internode kommunikation aktiverad** | True |
| **Max uppgifter per nod** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Exempel: Intel MPI på en Virtuell Linux H16r-pool

Om du vill köra MPI-program på en pool av Noder i Linux H-serien är ett alternativ att använda den [CentOS-baserade 7.4 HPC-avbildningen](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) från Azure Marketplace. Linux RDMA-drivrutiner och Intel MPI är förinstallerade. Den här avbildningen stöder också Docker-behållararbetsbelastningar.

Med hjälp av batch-API:er eller Azure-portalen skapar du en pool med den här avbildningen och med önskat antal noder och skala. I följande tabell visas exempelpoolinställningar:

| Inställning | Värde |
| ---- | ---- |
| **Avbildningstyp** | Marketplace (Linux/Windows) |
| **Publisher** | OpenLogic |
| **Erbjuder** | CentOS-HPC |
| **Sku** | 7.4 |
| **Nodstorlek** | H16r Standard |
| **Internode kommunikation aktiverad** | True |
| **Max uppgifter per nod** | 1 |

## <a name="next-steps"></a>Nästa steg

* Om du vill köra MPI-jobb i en Azure Batch-pool läser du [exemplen](batch-mpi.md) på Windows eller [Linux.](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/)

* Exempel på GPU-arbetsbelastningar på Batch finns i [batchvarvsrecepten.](https://github.com/Azure/batch-shipyard/)
