---
title: Behållaren arbetsbelastningar på Azure Batch | Microsoft Docs
description: Lär dig hur du kör program från behållaren bilder på Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 05/07/2018
ms.author: danlep
ms.openlocfilehash: 8c9f772c9d3908e450961239797f6ce2bd4982e4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="run-container-applications-on-azure-batch"></a>Köra behållarprogram på Azure Batch

Azure Batch kan du köra och skala stort antal batch computing jobb på Azure. Fram till nu batchaktiviteter har kört direkt på virtuella datorer (VM) i en Batch-pool, men nu kan du ställa in en Batch-pool att köra uppgifter i Docker-behållare. Den här artikeln visar hur du använder Batch .NET SDK för att skapa en pool med compute-noder som har stöd för aktiviteter som körs behållare och hur du kör behållaren uppgifter på poolen.

Med hjälp av behållare ger ett enkelt sätt att köra batchaktiviteter utan att behöva hantera en miljö och beroenden för att köra program. Behållare distribuera program som lätt och smidig självständigt enheter som kan köras i en mängd olika miljöer. Du kan till exempel skapa och testa en behållare lokalt och sedan överför avbildningen behållare till ett register i Azure eller någon annanstans. Distributionsmodell behållaren säkerställer att körningsmiljön på ditt program är alltid korrekt installerat och konfigurerat, oavsett var värd för programmet. Uppgifter i behållare i Batch kan också dra nytta av funktionerna i behållar-aktiviteter, inklusive programpaket och hantering av resursfiler och utdatafilerna. 

Den här artikeln förutsätter förtrogenhet med Docker behållare begrepp och hur du skapar en Batch-pool och jobb med hjälp av .NET SDK. Kodavsnitten är avsedda att användas i ett klientprogram som liknar den [DotNetTutorial exempel](batch-dotnet-get-started.md), och är exempel på kod som du behöver stöd för behållarprogram i en Batch.


## <a name="prerequisites"></a>Förutsättningar

* **SDK-versioner**: I Batch-SDK stöd behållaren bilder från och med följande versioner:
    * Batch REST API-version 2017-09-01.6.0
    * Batch .NET SDK version 8.0.0
    * Batch Python SDK version 4.0
    * Batch Java SDK version 3.0
    * Batch Node.js SDK version 3.0

* **Konton**: du behöver skapa ett Batch-konto och eventuellt ett Azure Storage-konto i din Azure-prenumeration.

* **En VM-avbildning som stöds**: behållare stöds bara i programpooler som skapats med konfigurationen av den virtuella datorn från avbildningar som beskrivs i avsnittet ”stöd för avbildningar av virtuella datorer”. Om du anger en anpassad avbildning programmet måste använda Azure Active Directory [(Azure AD) autentisering](batch-aad-auth.md) för att kunna köra behållare-baserade arbetsbelastningar. 


## <a name="supported-virtual-machine-images"></a>Avbildningar av virtuella datorer

Du måste använda en stöds Windows eller Linux-avbildningen för att skapa en pool med VM compute-noder för arbetsbelastningar i behållare.

### <a name="windows-images"></a>Windows-avbildningar

För Windows-behållaren arbetsbelastningar Batch stöder för närvarande anpassade avbildningar som du skapar från virtuella datorer som kör Docker i Windows eller du kan använda Windows Server 2016 Datacenter med behållare avbildning från Azure Marketplace. Den här avbildningen är kompatibel med den `batch.node.windows amd64` nod agent SKU-ID. Typ av behållare som stöds är för närvarande begränsad till Docker.

### <a name="linux-images"></a>Linux-avbildningar

För Linux-behållaren arbetsbelastningar Batch för närvarande stöder endast anpassade avbildningar som du skapar från virtuella datorer körs Docker på följande Linux-distributioner: Ubuntu 16.04 LTS eller CentOS 7.3. Om du vill ange en egen anpassad avbildning Linux instruktioner finns i [använda en anpassad avbildning som hanterad för att skapa en pool med virtuella datorer](batch-custom-images.md).

Docker-support, installera [Docker Community Edition (CE)](https://www.docker.com/community-edition) eller [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Om du vill dra nytta av GPU prestanda hos Azure NC eller NV VM-storlekar som du behöver installera drivrutinerna på bilden. Du måste också installera och köra verktyget Docker-motorn för NVIDIA GPU [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

Använda RDMA-kompatibla VM-storlekar, till exempel A8 A9, H16r, H16mr eller NC24r för åtkomst till Azure RDMA-nätverket. RDMA drivrutiner är installerade i CentOS 7.3 HPC och Ubuntu 16.04 LTS bilder från Azure Marketplace. Ytterligare konfiguration kan behövas för att köra MPI arbetsbelastningar. Se [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pool](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Begränsningar

* Batch tillhandahåller RDMA-stöd för behållare som körs på Linux-pooler.


## <a name="authenticate-using-azure-active-directory"></a>Autentisera med hjälp av Azure Active Directory

Om du använder en anpassad VM-avbildning för att skapa Batch-pool, klientprogrammet måste autentisera med Azure AD-integrerad autentisering (delad nyckelautentisering fungerar inte). Kontrollera att du registrera den i Azure AD att upprätta en identitet för den och ange dess behörigheter till andra program innan du kör programmet.

Dessutom när du använder en anpassad VM-avbildning, måste du ge IAM åtkomstkontroll till programmet för att komma åt den Virtuella datoravbildningen. I Azure-portalen klickar du på **alla resurser**, Välj behållare avbildningen, och från den **åtkomstkontroll (IAM)** avsnittet image-sidan klickar du på **Lägg till**. I den **lägga till behörigheter** anger en **rollen**i **bevilja åtkomst till**väljer **Azure AD-användare, grupp eller programmet**, sedan i  **Välj** Ange programnamnet.

Skicka en Azure AD-autentiseringstoken i ditt program när du skapar Batch-klienten. Om du utvecklar med Batch .NET SDK, använda [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), enligt beskrivningen i [autentiserar Batch tjänstelösningar med Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Referera till en VM-avbildning för att skapa en pool

Ange en referens till VM-avbildning som ska användas när compute-noder för poolen i din programkod. Det gör du genom att skapa en [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) objekt. Du kan ange avbildningen som ska användas på något av följande sätt:

* Om du använder en anpassad avbildning kan du ange en Azure Resource Manager resursidentifieraren för avbildningen av virtuella datorn. Bild-ID har en sökvägsformat som visas i följande exempel:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    För att få den här avbildnings-ID från Azure-portalen kan öppna **alla resurser**, markerar du den anpassade avbildningen och från den **översikt** avsnittet av sidan avbildningen kopiera sökvägen i **resurs-ID**.

* Om du använder en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based) bild, ange en uppsättning parametrar som beskriver bilden: utgivaren, erbjudandetypen, SKU och version för avbildningen som anges i [lista över virtuella datoravbildningar](batch-linux-nodes.md#list-of-virtual-machine-images):

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Konfigurationen för behållaren för Batch-pool

Om du vill aktivera en Batch-pool att köra arbetsbelastningar för behållare, måste du ange [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) inställningar i poolen [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objekt.

Du kan skapa en behållare-aktiverade pool med eller utan prefetched behållare bilder, som visas i följande exempel. Processen pull (eller prefetch) gör att du kan förinläsa behållaren bilder från Docker-hubb eller en annan behållare registret på Internet. Fördelen med förhämtar behållare bilder är att när uppgifter börjar med de inte behöver vänta på behållaren bilden ska hämtas. Konfigurationen av behållaren hämtar behållaren avbildningar till de virtuella datorerna när poolen har skapats. Uppgifter som körs på poolen kan sedan referera till listan över behållare bilder och behållare kör alternativ.



### <a name="pool-without-prefetched-container-images"></a>Poolen utan prefetched behållare bilder

Om du vill konfigurera poolen behållaren-aktiverade utan prefetched behållare bilder, definiera `ContainerConfiguration` och `VirtualMachineConfiguration` objekt som visas i följande exempel. Det här och följande exempel förutsätter att du använder en anpassad avbildning Ubuntu 16.04 LTS med Docker-motorn.

```csharp
// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>Prefetch avbildningar för konfigurationen behållaren

Om du vill förhämtning behållaren avbildningar på poolen lägger du till listan över behållare bilder (`containerImageNames`) till den `ContainerConfiguration`, och namnge Bildlistan. I följande exempel förutsätter att du använder en anpassad avbildning Ubuntu 16.04 LTS och förhämtning en TensorFlow-avbildning från [Docker-hubb](https://hub.docker.com). Det här exemplet innehåller en start-aktivitet som körs i VM-värden på pool-noder. Du kan köra Startuppgiften på värden, till exempel om du vill montera en filserver som kan nås från behållarna.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Prefetch bilder från ett privat behållaren register

Du kan också förhämtning behållaren avbildningar genom att autentisera till en server för privata behållare registret. I följande exempel visas den `ContainerConfiguration` och `VirtualMachineConfiguration` objekt som använder en anpassad avbildning Ubuntu 16.04 LTS och förhämtning en privat TensorFlow-avbildning från en behållare för privat Azure-registret.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>Behållaren inställningar för aktiviteten

Om du vill köra behållare uppgifter på datornoderna, måste du ange behållaren specifika inställningar, till exempel aktiviteten kör alternativ, bilder som ska användas och registret.

Använd den `ContainerSettings` egenskapen i klasserna uppgift att konfigurera behållaren-specifika inställningar. De här inställningarna definieras av den [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) klass.

Om du kör uppgifter på behållaren bilder, den [moln uppgiften](/dotnet/api/microsoft.azure.batch.cloudtask) och [manager projektaktivitet](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) kräver behållaren inställningar. Men den [aktiviteten starta](/dotnet/api/microsoft.azure.batch.starttask), [förberedelse projektaktivitet](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), och [versionen projektaktivitet](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) kräver inte behållaren inställningar (det vill säga de kan köras inom kontexten för en behållare eller direkt på noden).

När du konfigurerar inställningar för behållare, alla kataloger rekursivt nedan i `AZ_BATCH_NODE_ROOT_DIR` (roten för Azure Batch kataloger på noden) mappas till behållare och alla uppgiften miljö variabler som ska mappas till behållaren och kommandorad för uppgift körs i behållaren.

Kodexempel i [förhämtning avbildningar för konfigurationen behållaren](#prefetch-images-for-container-configuration) visades hur du anger en behållare konfiguration för en start-aktivitet. Följande kodexempel visar hur du anger konfigurationen för behållaren för en aktivitet i molnet:

```csharp
// Simple container task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Nästa steg

* Se även den [Batch skeppsvarv](https://github.com/Azure/batch-shipyard) toolkit för enkel distribution av arbetsbelastningar i behållare på Azure Batch via [skeppsvarv recept](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Mer information om att installera och använda Docker CE på Linux finns i [Docker](https://docs.docker.com/engine/installation/) dokumentation.

* Mer information om hur du använder anpassade avbildningar finns [använda en anpassad avbildning som hanterad för att skapa en pool med virtuella datorer ](batch-custom-images.md).
