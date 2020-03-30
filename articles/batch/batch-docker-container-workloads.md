---
title: Behållararbetsbelastningar - Azure Batch
description: Lär dig hur du kör och skalar appar från behållaravbildningar på Azure Batch. Skapa en pool med beräkningsnoder som stöder körning av behållaruppgifter.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/02/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 81f4e753ffbaaefd5761c9396a6533bac9f212c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254840"
---
# <a name="run-container-applications-on-azure-batch"></a>Kör behållarprogram på Azure Batch

Med Azure Batch kan du köra och skala ett stort antal batchdatorjobb på Azure. Batchaktiviteter kan köras direkt på virtuella datorer (noder) i en batchpool, men du kan också ställa in en batchpool för att köra aktiviteter i Docker-kompatibla behållare på noderna. Den här artikeln visar hur du skapar en pool med beräkningsnoder som stöder körning av behållaruppgifter och sedan kör behållaruppgifter i poolen.

Du bör känna till behållarbegrepp och hur du skapar en batchpool och ett jobb. Kodexemplen använder batchen .NET och Python SDK:er. Du kan också använda andra Batch-SDK:er och -verktyg, inklusive Azure-portalen, för att skapa behållaraktiverade batchpooler och för att köra behållaruppgifter.

## <a name="why-use-containers"></a>Varför använda containrar?

Att använda behållare är ett enkelt sätt att köra Batch-uppgifter utan att behöva hantera en miljö och beroenden för att köra program. Behållare distribuerar program som lätta, bärbara, självförsörjande enheter som kan köras i flera olika miljöer. Till exempel skapa och testa en behållare lokalt och sedan överföra behållaravbildningen till ett register i Azure eller någon annanstans. Behållardistributionsmodellen säkerställer att körningsmiljön för ditt program alltid är korrekt installerad och konfigurerad var du än är värd för programmet. Behållarbaserade uppgifter i Batch kan också dra nytta av funktioner i uppgifter som inte är behållare, inklusive programpaket och hantering av resursfiler och utdatafiler.

## <a name="prerequisites"></a>Krav

* **SDK-versioner**: Batch-SDK:erna stöder behållaravbildningar från och med följande versioner:
    * Batch REST API version 2017-09-01.6.0
    * Batch .NET SDK version 8.0.0
    * Batch Python SDK version 4.0
    * Batch Java SDK version 3.0
    * Batch Node.js SDK version 3.0

* **Konton**: I din Azure-prenumeration måste du skapa ett batchkonto och eventuellt ett Azure Storage-konto.

* **En vm-avbildning som stöds:** Behållare stöds endast i pooler som skapas med konfigurationen för virtuella datorer, från avbildningar som beskrivs i följande avsnitt, "Datoravbildningar som stöds". Om du anger en anpassad avbildning läser du övervägandena i följande avsnitt och kraven i [Använd en hanterad anpassad avbildning för att skapa en pool med virtuella datorer](batch-custom-images.md).

### <a name="limitations"></a>Begränsningar

* Batch ger RDMA-stöd endast för behållare som körs på Linux-pooler

* För Windows-behållararbetsbelastningar rekommenderar vi att du väljer en vm-storlek med flera kärnor för din pool

## <a name="supported-virtual-machine-images"></a>Bilder som stöds av virtuella datorer

Använd någon av följande Windows- eller Linux-avbildningar som stöds för att skapa en pool med VM-beräkningsnoder för behållararbetsbelastningar. Mer information om Marketplace-avbildningar som är kompatibla med Batch finns i [listan över avbildningar för virtuella datorer](batch-linux-nodes.md#list-of-virtual-machine-images).

### <a name="windows-support"></a>Windows-stöd

Batch stöder Windows-serveravbildningar som har behållaresstödbeteckningar. Vanligtvis är dessa bildsku-namn `-with-containers` suffixerade med eller `-with-containers-smalldisk`. Dessutom kommer [API:et för att lista alla bilder som stöds i Batch](batch-linux-nodes.md#list-of-virtual-machine-images) att ange en `DockerCompatible` funktion om avbildningen stöder Docker-behållare.

Du kan också skapa anpassade avbildningar från virtuella datorer som kör Docker i Windows.

### <a name="linux-support"></a>Linux-support

För Linux-behållararbetsbelastningar stöder Batch för närvarande följande Linux-avbildningar som publiceras av Microsoft Azure Batch på Azure Marketplace utan att det behövs en anpassad avbildning.

#### <a name="vm-sizes-without-rdma"></a>VM-storlekar utan RDMA

- Publisher:`microsoft-azure-batch`
  - Erbjuder:`centos-container`
  - Erbjuder:`ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>VM-storlekar med RDMA

- Publisher:`microsoft-azure-batch`
  - Erbjuder:`centos-container-rdma`
  - Erbjuder:`ubuntu-server-container-rdma`

Dessa avbildningar stöds endast för användning i Azure Batch-pooler och är anpassade för Körning av Docker-behållare. De har:

* En förinstallerad Docker-kompatibel [Moby-containerkörning](https://github.com/moby/moby)

* Förinstallerade NVIDIA GPU-drivrutiner och NVIDIA-containerkörning för att effektivisera distributionen på virtuella azure N-serien

* Förinstallerad/förkonfigurerad avbildning `-rdma`med stöd för Infiniband RDMA VM-storlekar för avbildningar med suffixet . För närvarande stöder dessa avbildningar inte storlekarna SR-IOV IB/RDMA VM.

Du kan också skapa anpassade avbildningar från virtuella datorer som kör Docker på en av de Linux-distributioner som är kompatibel med Batch. Om du väljer att tillhandahålla din egen anpassade Linux-avbildning läser du instruktionerna i [Använd en hanterad anpassad avbildning för att skapa en pool med virtuella datorer](batch-custom-images.md).

För Docker-stöd för en anpassad avbildning installerar du [Docker Community Edition (CE)](https://www.docker.com/community-edition) eller [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Ytterligare överväganden för att använda en anpassad Linux-avbildning:

* Om du vill dra nytta av GPU-prestanda för Storlekar i Azure N-serien när du använder en anpassad avbildning förinstallerar du NVIDIA-drivrutiner. Dessutom måste du installera Docker Engine Utility för NVIDIA GPU: er, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Använd en virtuell datorstorlek för fjärrskrivbordskompatibla datorer för att komma åt Azure RDMA-nätverket. Nödvändiga RDMA-drivrutiner installeras i CentOS HPC- och Ubuntu-avbildningar som stöds av Batch. Ytterligare konfiguration kan behövas för att köra MPI-arbetsbelastningar. Se [Använda RDMA-kompatibla eller GPU-aktiverade instanser i batchpoolen](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Behållarkonfiguration för batchpool

Om du vill att en batchpool ska kunna köra behållararbetsbelastningar måste du ange [ContainersConfiguration-inställningar](/dotnet/api/microsoft.azure.batch.containerconfiguration) i poolens [VirtualMachineConfiguration-objekt.](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) (Den här artikeln innehåller länkar till batch -NET API-referensen. Motsvarande inställningar finns i [Batch Python](/python/api/overview/azure/batch) API.)

Du kan skapa en behållaraktiverad pool med eller utan försökt behållarbilder, som visas i följande exempel. Med processen pull (eller prefetch) kan du förinstalleras med behållaravbildningar från Docker Hub eller ett annat behållarregister på Internet. För bästa prestanda kan du använda ett [Azure-behållarregister](../container-registry/container-registry-intro.md) i samma region som batchkontot.

Fördelen med prefetching container bilder är att när uppgifter först börjar köras de inte behöver vänta på behållaren bilden att ladda ner. Behållarkonfigurationen drar behållaravbildningar till de virtuella datorerna när poolen skapas. Aktiviteter som körs i poolen kan sedan referera till listan över behållaravbildningar och alternativ för behållarkörning.


### <a name="pool-without-prefetched-container-images"></a>Pool utan försökt behållarbilder

Om du vill konfigurera en behållaraktiverad pool `ContainerConfiguration` utan `VirtualMachineConfiguration` försökt behållaravbildningar definierar och objekt som visas i följande Python-exempel. I det här exemplet används avbildningen Ubuntu Server för Azure Batch-behållarpooler från Marketplace.


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


### <a name="prefetch-images-for-container-configuration"></a>Prefetch-avbildningar för behållarkonfiguration

Om du vill prefetch behållaravbildningar i`container_image_names`poolen lägger `ContainerConfiguration`du till listan över behållaravbildningar ( , i Python) i .

Följande grundläggande Python-exempel visar hur du prefetch en standard Ubuntu container bild från [Docker Hub](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub.
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

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


I följande C#-exempel förutsätts att du vill prefetch en TensorFlow-avbildning från [Docker Hub](https://hub.docker.com). Det här exemplet innehåller en startaktivitet som körs i vm-värden på poolnoderna. Du kan köra en startuppgift i värden, till exempel för att montera en filserver som kan nås från behållarna.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Prefetch-avbildningar från ett privat behållarregister

Du kan också prefetch behållaravbildningar genom att autentisera till en privat containerregisterserver. I följande exempel `ContainerConfiguration` prefetch en privat TensorFlow-avbildning från `VirtualMachineConfiguration` ett privat Azure-behållarregister och objekt prefetch. Bildreferensen är densamma som i föregående exempel.

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

## <a name="container-settings-for-the-task"></a>Behållarinställningar för uppgiften

Om du vill köra en behållaruppgift på en containeraktiverad pool anger du behållarspecifika inställningar. Inställningarna inkluderar de alternativ för att använda, registret och behållarkörningen.

* Använd `ContainerSettings` egenskapen för uppgiftsklasserna för att konfigurera behållarspecifika inställningar. Dessa inställningar definieras av klassen [TaskContainerSettings.](/dotnet/api/microsoft.azure.batch.taskcontainersettings) Observera att `--rm` behållaralternativet inte `--runtime` kräver ytterligare ett alternativ eftersom det tas om hand av Batch.

* Om du kör uppgifter på behållaravbildningar kräver [aktiviteten för molnuppgift](/dotnet/api/microsoft.azure.batch.cloudtask) och [jobbhanterare](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) behållarinställningar. [Startaktiviteten,](/dotnet/api/microsoft.azure.batch.starttask) [jobbförberedelseaktiviteten](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)och [jobbfrisättningsaktiviteten](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) kräver dock inte behållarinställningar (det vill sagt de kan köras i en behållarkontext eller direkt på noden).

### <a name="container-task-command-line"></a>Kommandorad för behållaruppgift

När du kör en behållaruppgift använder Batch kommandot [docker create](https://docs.docker.com/engine/reference/commandline/create/) för att skapa en behållare med hjälp av den avbildning som anges i aktiviteten. Batch styr sedan körning av uppgifter i behållaren.

Precis som med batch-uppgifter som inte är behållare anger du en kommandorad för en behållaruppgift. Eftersom Batch automatiskt skapar behållaren anger kommandoraden bara det eller de kommandon som ska köras i behållaren.

Om behållaravbildningen för en batch-uppgift är konfigurerad med ett [ENTRYPOINT-skript](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) kan du ange att kommandoraden antingen ska använda standardPOSTPUNKTEN eller åsidosätta den:

* Om du vill använda standardPOSTPUNKTEN för behållarbilden anger `""`du aktivitetskommandoraden till den tomma strängen .

* Om du vill åsidosätta standardPOSTPUNKTEN, eller om bilden inte har en ENTRYPOINT, anger `/app/myapp` `/bin/sh -c python myscript.py`du en kommandorad som är lämplig för behållaren, till exempel eller .

Valfria [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) är ytterligare `docker create` argument som du anger till kommandot som Batch använder för att skapa och köra behållaren. Om du till exempel vill ange en `--workdir <directory>` arbetskatalog för behållaren anger du alternativet. Se [docker skapa](https://docs.docker.com/engine/reference/commandline/create/) referens för ytterligare alternativ.

### <a name="container-task-working-directory"></a>Arbetskatalog för behållaruppgift

En batch-behållaruppgift körs i en arbetskatalog i behållaren som är mycket lik katalogen Batch ställer in för en vanlig (icke-behållare) aktivitet. Observera att den här arbetskatalogen skiljer sig från [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) om den`C:\` är konfigurerad i `/` avbildningen, eller standardkatalogen för behållaren (på en Windows-behållare eller på en Linux-behållare).

För en batch-behållaruppgift:

* Alla kataloger rekursivt `AZ_BATCH_NODE_ROOT_DIR` under den på värdnoden (roten till Azure Batch-kataloger) mappas till behållaren
* Alla aktivitetsmiljövariabler mappas till behållaren
* Uppgiftsarbetskatalogen `AZ_BATCH_TASK_WORKING_DIR` på noden anges på samma sätt som för en vanlig uppgift och mappas till behållaren.

Med de här mappningarna kan du arbeta med behållaruppgifter på ungefär samma sätt som uppgifter som inte är behållare. Till exempel installera program med hjälp av programpaket, komma åt resursfiler från Azure Storage, använda inställningar för aktivitetsmiljö och spara aktivitetsutdatafiler när behållaren har stannat.

### <a name="troubleshoot-container-tasks"></a>Felsöka behållaruppgifter

Om behållaruppgiften inte körs som förväntat kan du behöva få information om WORKDIR- eller ENTRYPOINT-konfigurationen för behållaravbildningen. Om du vill se konfigurationen kör du kommandot [docker image inspect.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

Om det behövs justerar du inställningarna för behållaruppgiften baserat på bilden:

* Ange en absolut sökväg på aktivitetskommandoraden. Om bildens standardPOSTPUNKT används för aktivitetskommandoraden kontrollerar du att en absolut sökväg är inställd.

* Ändra arbetskatalogen så att den matchar WORKDIR i avbildningen i aktivitetens alternativ för behållarkörning. Ange till `--workdir /app`exempel .

## <a name="container-task-examples"></a>Exempel på behållaruppgift

Följande Python-kodavsnitt visar en grundläggande kommandorad som körs i en behållare som skapats från en fiktiv avbildning som hämtats från Docker Hub. Här tar `--rm` behållaralternativet bort behållaren när aktiviteten `--workdir` är klar och alternativet anger en arbetskatalog. Kommandoraden åsidosätter behållarPOSTEN med ett enkelt skalkommando som skriver en liten fil till uppgiftsarbetskatalogen på värden.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

I följande C#-exempel visas grundläggande behållarinställningar för en molnuppgift:

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine);
```


## <a name="next-steps"></a>Nästa steg

* Se även [batchvarvsverktygslådan](https://github.com/Azure/batch-shipyard) för enkel distribution av behållararbetsbelastningar på Azure Batch via [Varv-recept](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Mer information om hur du installerar och använder Docker CE på Linux finns i [Docker-dokumentationen.](https://docs.docker.com/engine/installation/)

* Mer information om hur du använder anpassade avbildningar finns i [Använda en hanterad anpassad avbildning för att skapa en pool med virtuella datorer](batch-custom-images.md).

* Läs mer om [Moby-projektet](https://mobyproject.org/), ett ramverk för att skapa containerbaserade system.
