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
ms.date: 06/04/2018
ms.author: danlep
ms.openlocfilehash: 4ee8425bb5c3830b029b766aad464df0ffb15f41
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801123"
---
# <a name="run-container-applications-on-azure-batch"></a>Köra behållarprogram på Azure Batch

Azure Batch kan du köra och skala stort antal batch computing jobb på Azure. Batchaktiviteter kan köras direkt på virtuella datorer (noder) i en Batch-pool, men du kan också ställa in en Batch-pool att köra uppgifter i Docker-kompatibel behållare på noderna. Den här artikeln visar hur du skapar en pool med compute-noder som stöder behållare aktiviteter som körs och kör sedan behållaren uppgifter på poolen. 

Du bör känna till behållaren begrepp och hur du skapar en Batch-pool och jobb. Kodexemplen använder Batch .NET och Python SDK: er. Du kan också använda andra Batch SDK: er och verktyg, bland annat Azure-portalen att skapa behållaren-aktiverade Batch pooler och köra uppgifter i behållaren.

## <a name="why-use-containers"></a>Varför använda behållare?

Med hjälp av behållare ger ett enkelt sätt att köra batchaktiviteter utan att behöva hantera en miljö och beroenden för att köra program. Behållare distribuera program som lätt och smidig självständigt enheter som kan köras i flera olika miljöer. Du kan till exempel skapa och testa en behållare lokalt och sedan överför avbildningen behållare till ett register i Azure eller någon annanstans. Distributionsmodell behållaren säkerställer att körningsmiljön på ditt program är alltid korrekt installerat och konfigurerat oavsett var du är värd för programmet. Uppgifter i behållare i Batch kan också dra nytta av funktionerna i behållar-aktiviteter, inklusive programpaket och hantering av resursfiler och utdatafilerna. 

## <a name="prerequisites"></a>Förutsättningar

* **SDK-versioner**: I Batch-SDK stöd behållaren bilder från och med följande versioner:
    * Batch REST API-version 2017-09-01.6.0
    * Batch .NET SDK version 8.0.0
    * Batch Python SDK version 4.0
    * Batch Java SDK version 3.0
    * Batch Node.js SDK version 3.0

* **Konton**: du behöver skapa ett Batch-konto och eventuellt ett Azure Storage-konto i din Azure-prenumeration.

* **En VM-avbildning som stöds**: behållare stöds bara i programpooler som skapats med konfigurationen av den virtuella datorn från avbildningar som beskrivs i avsnittet ”stöd för avbildningar av virtuella datorer”. Om du anger en anpassad avbildning finns i följande avsnitt överväganden och krav i [använda en anpassad avbildning som hanterad för att skapa en pool med virtuella datorer](batch-custom-images.md). 

### <a name="limitations"></a>Begränsningar

* Batch tillhandahåller RDMA-stöd för behållare som körs på Linux-pooler.

## <a name="supported-virtual-machine-images"></a>Avbildningar av virtuella datorer

Stöd för att använda något av följande Windows eller Linux-bilder att skapa en pool av VM compute-noder för arbetsbelastningar i behållare. Läs mer om Marketplace-avbildningar som är kompatibla med Batch [lista över virtuella datoravbildningar](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Windows-avbildningar

För Windows-behållaren arbetsbelastningar Batch för närvarande stöder den **Windows Server 2016 Datacenter med behållare** avbildning i Azure Marketplace. Endast Docker behållare avbildningar stöds i Windows.

Du kan också skapa anpassade avbildningar från virtuella datorer som kör Docker i Windows.

### <a name="linux-images"></a>Linux-avbildningar

Batch stöder för närvarande följande Linux-bilder som publicerats av Microsoft Azure Batch i Azure Marketplace för Linux-behållaren arbetsbelastningar:

* **CentOS för Azure Batch behållaren pooler**

* **CentOS (med RDMA-drivrutiner) för Azure Batch behållaren pooler**

* **Ubuntu Server för Azure Batch behållaren pooler**

* **Ubuntu Server (med RDMA-drivrutiner) för Azure Batch behållaren pooler**

Dessa avbildningar stöds endast för användning i Azure Batch-pooler. De funktion:

* En förinstallerad [Moby](https://github.com/moby/moby) behållare runtime 

* Förinstallerade NVIDIA GPU drivrutiner, att förenkla distribution på Azure N-serien virtuella datorer

* Bilder med eller utan förinstallerade RDMA-drivrutiner. de här drivrutinerna tillåter att poolen noder åtkomst till Azure RDMA-nätverket när de distribueras på RDMA-kompatibla VM-storlekar  

Du kan också skapa anpassade avbildningar från virtuella datorer som kör Docker på en Linux-distributioner som är kompatibel med Batch. Om du vill ange en egen anpassad avbildning Linux instruktioner finns i [använda en anpassad avbildning som hanterad för att skapa en pool med virtuella datorer](batch-custom-images.md).

Stöd för Docker på en anpassad avbildning, installera [Docker Community Edition (CE)](https://www.docker.com/community-edition) eller [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Ytterligare överväganden för att använda en anpassad avbildning för Linux:

* Om du vill dra nytta av Azure N-serien storlekar GPU prestanda när du använder en anpassad avbildning, förinstallation drivrutinerna. Du måste också installera verktyget Docker-motorn för NVIDIA GPU [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Använd en RDMA-kompatibla VM-storlek för åtkomst till Azure RDMA-nätverket. RDMA drivrutiner är installerade i CentOS HPC och Ubuntu-avbildningar stöds av Batch. Ytterligare konfiguration kan behövas för att köra MPI arbetsbelastningar. Se [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pool](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Konfigurationen för behållaren för Batch-pool

Om du vill aktivera en Batch-pool att köra arbetsbelastningar för behållare, måste du ange [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) inställningar i poolen [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objekt. (Den här artikeln innehåller länkar till Batch .NET API-referens. Motsvarande inställningar finns i den [Batch Python](/python/api/azure.batch) API.)

Du kan skapa en behållare-aktiverade pool med eller utan prefetched behållare bilder, som visas i följande exempel. Processen pull (eller prefetch) gör att du kan förinläsa behållaren bilder från Docker-hubb eller en annan behållare registret på Internet. För bästa prestanda bör du använda en [Azure-behållaren registret](../container-registry/container-registry-intro.md) i samma region som Batch-kontot.

Fördelen med förhämtar behållare bilder är att när uppgifter börjar med de inte behöver vänta på behållaren bilden ska hämtas. Konfigurationen av behållaren hämtar behållaren avbildningar till de virtuella datorerna när poolen har skapats. Uppgifter som körs på poolen kan sedan referera till listan över behållare bilder och behållare kör alternativ.


### <a name="pool-without-prefetched-container-images"></a>Poolen utan prefetched behållare bilder

Om du vill konfigurera poolen behållaren-aktiverade utan prefetched behållare bilder, definiera `ContainerConfiguration` och `VirtualMachineConfiguration` objekt som visas i exemplet nedan Python. Det här exemplet använder Ubuntu Server för Azure Batch behållaren pooler avbildning från Marketplace.


```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            container_configuration=container_conf,
            node_agent_sku_id='batch.node.ubuntu 16.04'),
        vm_size='STANDARD_D1_V2',
        target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Prefetch avbildningar för konfigurationen behållaren

Om du vill förhämtning behållaren avbildningar på poolen lägger du till listan över behållare bilder (`container_image_names`, i Python) till den `ContainerConfiguration`. 

Följande grundläggande Python-exempel visar hur du förhämtning en standardiserad Ubuntu behållaren avbildning från [Docker-hubb](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


I följande exempel C#-exempel förutsätter att du vill förhämtning en TensorFlow-avbildning från [Docker-hubb](https://hub.docker.com). Det här exemplet innehåller en start-aktivitet som körs i VM-värden på pool-noder. Du kan köra Startuppgiften på värden, till exempel om du vill montera en filserver som kan nås från behållarna.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

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
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Prefetch bilder från ett privat behållaren register

Du kan också förhämtning behållaren avbildningar genom att autentisera till en server för privata behållare registret. I följande exempel visas den `ContainerConfiguration` och `VirtualMachineConfiguration` objekt förhämtning en privat TensorFlow-avbildning från en behållare för privat Azure-registret. Bildreferensen är samma som i föregående exempel.

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
...
```


## <a name="container-settings-for-the-task"></a>Behållaren inställningar för aktiviteten

Om du vill köra behållare uppgifter på datornoderna, måste du ange behållaren specifika inställningar, till exempel aktiviteten kör alternativ, bilder som ska användas och registret.

Använd den `ContainerSettings` egenskapen i klasserna uppgift att konfigurera behållaren-specifika inställningar. De här inställningarna definieras av den [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) klass.

Om du kör uppgifter på behållaren bilder, den [moln uppgiften](/dotnet/api/microsoft.azure.batch.cloudtask) och [manager projektaktivitet](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) kräver behållaren inställningar. Men den [aktiviteten starta](/dotnet/api/microsoft.azure.batch.starttask), [förberedelse projektaktivitet](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), och [versionen projektaktivitet](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) kräver inte behållaren inställningar (det vill säga de kan köras inom kontexten för en behållare eller direkt på noden).

När du konfigurerar inställningar för behållare, alla kataloger rekursivt nedan i `AZ_BATCH_NODE_ROOT_DIR` (roten för Azure Batch kataloger på noden) mappas till behållare och alla uppgiften miljö variabler som ska mappas till behållaren och kommandorad för uppgift körs i behållaren.

Python följande fragment visas en grundläggande kommandorad som körs i en behållare för Ubuntu som hämtas från Docker-hubb. Behållaren som kör alternativen är ytterligare argument till den `docker create` kommando som aktiviteten körs. Här är den `--rm` alternativet tar bort behållaren när uppgiften har slutförts.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='ubuntu', 
    container_run_options='--rm')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='echo hello',
    container_settings=task_container_settings
)

```

Följande C#-exempel visar inställningar för grundläggande behållare för en aktivitet i molnet:

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

* Lär dig mer om den [Moby projekt](https://mobyproject.org/), ett ramverk för att skapa behållaren-baserade system.