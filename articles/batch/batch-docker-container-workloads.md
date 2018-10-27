---
title: Arbetsbelastningar på Azure Batch | Microsoft Docs
description: Lär dig hur du kör program från behållaravbildningar på Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 10/24/2018
ms.author: danlep
ms.openlocfilehash: 458b0f7bbf581c7f2490a8122f351dac612b4ff0
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155634"
---
# <a name="run-container-applications-on-azure-batch"></a>Kör program med behållare på Azure Batch

Azure Batch kan du köra och skala stora mängder batch-jobb på Azure. Batch-aktiviteterna kan köras direkt på virtuella datorer (noder) i en Batch-pool, men du kan också ställa in en Batch-pool för körning av uppgifter i Docker-kompatibla behållare för noderna. Den här artikeln visar hur du skapar en pool med beräkningsnoder som stöd för aktiviteter som körs behållare och sedan köra behållare uppgifter på poolen. 

Du bör känna till behållaren begrepp och hur du skapar ett Batch-poolen och jobbet. Kodexemplen använda Batch .NET och Python SDK: er. Du kan också använda andra Batch SDK: er och verktyg, bland annat Azure-portalen att skapa behållaren-aktiverade Batch-pooler och för att köra behållare uppgifter.

## <a name="why-use-containers"></a>Varför använda behållare?

Med hjälp av behållare ger ett enkelt sätt att köra Batch-aktiviteterna utan att behöva hantera en miljö och beroenden för att köra program. Behållare kan du distribuera program som enkel och smidig oberoende enheter som kan köras i flera olika miljöer. Du kan till exempel skapa och testa en behållare lokalt och sedan överföra behållaravbildningen till ett register i Azure eller någon annanstans. Distributionsmodell för behållaren säkerställer att körningsmiljö för ditt program alltid korrekt installerat och konfigurerat oavsett var du vara värd för programmet. Behållarbaserad aktiviteter i Batch kan också dra nytta av funktionerna i icke-container aktiviteter, inklusive programpaket och hantering av resursfiler och utdatafiler. 

## <a name="prerequisites"></a>Förutsättningar

* **SDK-versioner**: The Batch SDK: er stöd behållaravbildningar från och med följande versioner:
    * Batch REST-API version 2017-09-01.6.0
    * Batch .NET SDK version 8.0.0
    * Batch Python SDK version 4.0
    * Batch Java SDK version 3.0
    * Batch Node.js SDK version 3.0

* **Konton**: I Azure-prenumerationen, måste du skapa ett Batch-konto och eventuellt ett Azure Storage-konto.

* **En VM-avbildning som stöds**: behållare stöds bara i pooler som skapats med konfigurationen av virtuell dator från avbildningar som beskrivs i avsnittet ”stöd för avbildningar av virtuella datorer”. Om du anger en anpassad avbildning finns i överväganden i följande avsnitt och kraven i [använda en hanterad anpassad avbildning för att skapa en pool med virtuella datorer](batch-custom-images.md). 

### <a name="limitations"></a>Begränsningar

* Batch tillhandahåller RDMA-stöd endast för behållare som körs på Linux-pooler

* För Windows-behållararbetsbelastningar rekommenderar vi att du väljer en flerkärniga VM-storlek för poolen

## <a name="supported-virtual-machine-images"></a>Avbildningar av virtuella datorer stöds

Stöd för att använda något av följande Windows eller Linux-avbildningar för att skapa en pool med VM-beräkningsnoder för behållararbetsbelastningar. Läs mer om Marketplace-avbildningar som är kompatibla med Batch [lista över avbildningar av virtuella datorer](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Windows-avbildningar

För Windows-behållararbetsbelastningar, Batch stöder för närvarande den **Windows Server 2016 Datacenter med behållare** avbildning i Azure Marketplace. Docker-behållaravbildningar endast stöds på Windows.

Du kan också skapa anpassade avbildningar från virtuella datorer som kör Docker på Windows.

### <a name="linux-images"></a>Linux-avbildningar

Batch stöder för närvarande följande Linux-avbildningar som publicerats av Microsoft Azure Batch på Azure Marketplace för Linux-behållararbetsbelastningar:

* **CentOS för Azure Batch-pooler för behållare**

* **CentOS (med RDMA-drivrutiner) för Azure Batch-pooler för behållare**

* **Ubuntu-Server för Azure Batch-pooler för behållare**

* **Ubuntu-Server (med RDMA-drivrutiner) för Azure Batch-pooler för behållare**

Dessa avbildningar stöds endast för användning i Azure Batch-pooler. De omfattar:

* En förinstallerade [Moby](https://github.com/moby/moby) behållare runtime 

* Förinstallerade NVIDIA GPU-drivrutiner, att förenkla distributionen på Azure virtuella datorer i N-serien

* Bilder med eller utan förinstallerade RDMA-drivrutiner. de här drivrutinerna Tillåt poolnoder komma åt Azure RDMA-nätverket när de distribueras på RDMA-kompatibla VM-storlekar  

Du kan också skapa anpassade avbildningar från virtuella datorer som kör Docker på en Linux-distributioner som är kompatibel med Batch. Om du väljer att ge dina egna anpassade Linux-avbildning, se anvisningarna i [använda en hanterad anpassad avbildning för att skapa en pool med virtuella datorer](batch-custom-images.md).

Stöd för Docker på en anpassad avbildning installera [Docker Community Edition (CE)](https://www.docker.com/community-edition) eller [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Ytterligare överväganden för att använda en anpassad Linux-avbildning:

* Om du vill dra nytta av GPU-prestanda i Azure N-serien storlekar när du använder en anpassad avbildning, förinstallation av drivrutinerna. Du måste också installera verktyget Docker-motorn för NVIDIA GPU: er, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Du kommer åt Azure RDMA-nätverk genom att använda en RDMA-kompatibla VM-storlek. RDMA-drivrutiner som krävs har installerats i HPC CentOS och Ubuntu-avbildningar som stöds av Batch. Ytterligare konfiguration kan behövas för att köra MPI-arbetsbelastningar. Se [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pool](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Konfigurationen av behållaren för Batch-pool

Om du vill aktivera en Batch-pool att köra arbetsbelastningar med behållare, måste du ange [ska köras](/dotnet/api/microsoft.azure.batch.containerconfiguration) inställningar i poolens [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objekt. (Den här artikeln innehåller länkar till Batch .NET API-referens. Motsvarande inställningar finns i den [Batch Python](/python/api/azure.batch) API.)

Du kan skapa en behållare-aktiverade pool med eller utan prefetched behållaravbildningar, som visas i följande exempel. Processen för pull (eller prefetch) kan du Förhandsladda behållaravbildningar från Docker Hub eller en annan behållarregister på Internet. För bästa prestanda bör använda en [Azure-behållarregister](../container-registry/container-registry-intro.md) i samma region som Batch-kontot.

Fördelen med förhämtar, behållaravbildningar är att när aktiviteter börjar som kör de inte behöver vänta på att ladda ned behållaravbildningen. Konfigurationen av behållaren hämtar behållaravbildningar till de virtuella datorerna när poolen skapas. Aktiviteter som körs i poolen kan sedan referera till listan över behållaravbildningar och körningsalternativ för behållare.


### <a name="pool-without-prefetched-container-images"></a>Pool utan prefetched behållaravbildningar

Om du vill konfigurera en container-aktiverade pool utan prefetched behållaravbildningar, definiera `ContainerConfiguration` och `VirtualMachineConfiguration` objekt som visas i följande exempel för Python. Det här exemplet använder Ubuntu-Server för Azure Batch-pooler för behållaravbildningen från Marketplace.


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


### <a name="prefetch-images-for-container-configuration"></a>Förhämtning av avbildningar för behållarkonfiguration

Om du vill förhämtning behållaravbildningar för pool, lägger du till listan över behållaravbildningar (`container_image_names`, i Python) till den `ContainerConfiguration`. 

I följande grundläggande Python-exempel visar hur du förhämtning av en standard Ubuntu-behållaravbildning från [Docker Hub](https://hub.docker.com).

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


Följande C# exemplet förutsätter vi att du vill förhämtning av en TensorFlow-avbildning från [Docker Hub](https://hub.docker.com). Det här exemplet innehåller en startaktivitet som körs i VM-värden på noderna i poolen. Du kan köra en startaktivitet på värden, till exempel om du vill montera en filserver som kan nås från behållarna.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Förhämtning av avbildningar från ett privat behållarregister

Du kan också förhämtning behållaravbildningar genom att autentisera till en privat container registry-server. I följande exempel visas den `ContainerConfiguration` och `VirtualMachineConfiguration` objekt förhämtning av en privat TensorFlow-avbildning från ett privat Azure container registry. Referensen till avbildningen är samma som i föregående exempel.

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


## <a name="container-settings-for-the-task"></a>Behållarinställningar för aktiviteten

Du måste ange container-specifika inställningar som behållaren kör alternativ och bilder för att använda registret för att köra behållare aktiviteter på beräkningsnoderna.

Använd den `ContainerSettings` egenskapen i klasserna uppgift att konfigurera container-specifika inställningar. De här inställningarna definieras av den [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) klass.

Om du kör uppgifter på behållaravbildningar, den [molnet uppgift](/dotnet/api/microsoft.azure.batch.cloudtask) och [job manager-aktivitet](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) kräver behållarinställningar. Men den [startaktivitet](/dotnet/api/microsoft.azure.batch.starttask), [jobbförberedelseaktiviteten](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), och [jobbpubliceringsaktivitet](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) kräver inte behållarinställningar (det vill säga de kan köras inom kontexten för en behållare eller direkt på noden).

Den valfria [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) finns ytterligare argument till den `docker create` kommandot körs aktiviteten för att skapa behållaren.

### <a name="container-task-working-directory"></a>Arbetskatalogen behållare

Kommandoraden för en Azure Batch-aktiviteter för behållare som körs i en arbetskatalog i behållaren som liknar den miljö som Batch ställer in för en vanlig (icke-behållare):

* Alla kataloger rekursivt nedan i `AZ_BATCH_NODE_ROOT_DIR` (rot med Azure Batch-kataloger på noden) mappas till behållaren
* Alla miljövariabler för aktiviteten är mappade till behållaren
* Programmet arbetskatalogen anges desamma som för en vanlig aktivitet så att du kan använda funktioner som programpaket och resursfiler

Aktiviteten körs på en plats som skiljer sig från arbetskatalogen vanliga behållaren eftersom Batch ändras standard arbetskatalogen i behållaren, (till exempel `c:\` som standard på en Windows-behållare eller `/` på Linux eller någon annan katalogen om konfigurerat i behållaravbildningen). För att säkerställa att dina behållarprogram köras i Batch-kontexten, gör du något av följande: 

* Se till att aktivitetens kommandorad (eller behållare arbetskatalog) anger en absolut sökväg om det inte redan har konfigurerats på så sätt.

* Ange en arbetskatalog i körningsalternativ för behållare i aktivitetens Containerconfiguration. Till exempel `--workdir /app`.

Python följande utdrag visar en grundläggande kommandorad som körs i en Ubuntu-behållare som hämtas från Docker Hub. Här är den `--rm` behållare som kör alternativet tar bort behållaren när uppgiften har slutförts.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='ubuntu', 
    container_run_options='--rm')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/echo hello',
    container_settings=task_container_settings
)

```

I följande C#-exempel visas grundläggande behållarinställningar för en cloud-aktivitet:

```csharp
// Simple container task command

string cmdLine = "c:\myApp.exe";

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

* Se även de [Batch skeppsvarv](https://github.com/Azure/batch-shipyard) toolkit för enkel distribution av arbetsbelastningar med behållare på Azure Batch via [skeppsvarv recept](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Mer information om att installera och använda Docker CE för Linux finns i den [Docker](https://docs.docker.com/engine/installation/) dokumentation.

* Mer information om hur du använder anpassade avbildningar finns i [använda en hanterad anpassad avbildning för att skapa en pool med virtuella datorer ](batch-custom-images.md).

* Läs mer om den [Moby projekt](https://mobyproject.org/), ett ramverk för att skapa behållaren-baserade system.