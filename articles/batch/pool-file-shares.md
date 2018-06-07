---
title: Azure-filresursen för Azure Batch pooler | Microsoft Docs
description: Hur du monterar en resurs i Azure-filer från beräkningsnoder i en Linux- eller Windows-pool i Azure Batch.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 88d7c0d033d7b517a396df27468de8be7ae20be9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34811741"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Använda en Azure-filresursen med en Batch-pool

[Azure Files](../storage/files/storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via Server Message Block (SMB)-protokollet. Den här artikeln innehåller information och kod som exempel för att montera och använda en Azure-filresursen på poolen compute-noder. Kodexemplen använda Batch .NET och Python SDK: er, men du kan utföra samma åtgärder med hjälp av andra Batch SDK: er och verktyg.

Batch innehåller interna API-stöd för att läsa och skriva data med Azure Storage BLOB. Men i vissa fall kanske du vill komma åt en Azure-filresursen från din pool compute-noder. Till exempel du har en äldre arbetsbelastning som beror på en SMB filresurs eller aktiviteterna behöver åtkomst till delade data eller delade resultat. 

## <a name="considerations-for-use-with-batch"></a>Överväganden för användning med Batch

* Överväg att använda en Azure-filresurs när du har pooler som kör ett relativt lågt antal parallella aktiviteter. Granska de [prestanda och skalning mål](../storage/files/storage-files-scale-targets.md) att avgöra om Azure-filer (som använder ett Azure Storage-konto) ska användas, baserat på din förväntade poolstorleken och antalet tillgångsfiler som. 

* Azure-filresurser är [kostnadseffektiv](https://azure.microsoft.com/pricing/details/storage/files/) och kan konfigureras med data replikering till en annan region så är globalt redundant. 

* Du kan montera en Azure-filresursen samtidigt från en lokal dator.

* Se även allmänna [överväganden vid planering av](../storage/files/storage-files-planning.md) för Azure-filresurser.


## <a name="create-a-file-share"></a>Skapa en filresurs

[Skapa en filresurs](../storage/files/storage-how-to-create-file-share.md) i ett lagringskonto som är kopplad till Batch-kontot, eller i ett separat lagringskonto.

## <a name="mount-a-share-on-a-windows-pool"></a>Montera en resurs på en Windows-pool

Det här avsnittet innehåller steg och kodexempel för montera och använda en Azure-resursen på en pool med Windows-noder. Ytterligare bakgrund, finns det [dokumentationen](../storage/files/storage-how-to-use-files-windows.md) för att montera en Azure-filresurs i Windows. 

I batchen behöver du monterar filresursen varje gång en aktivitet körs på en Windows-nod. För närvarande, går det inte att spara nätverksanslutningen mellan uppgifter på Windows-noder.

Innehåller till exempel en `net use` kommando för att montera filresursen som en del av kommandoraden för varje aktivitet. Följande autentiseringsuppgifter krävs för att montera filresursen:

* **Användarnamnet**: AZURE\\\<storageaccountname\>, till exempel AZURE\\*mystorageaccountname*
* **Lösenordet**: < StorageAccountKeyWhichEnds i == >, till exempel *XXXXXXXXXXXXXXXXXXXXX ==*

Följande kommando monterar en filresurs *myfileshare* i lagringskonto *mystorageaccountname* som den *S:* enhet:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

För enkelhetens skull klara exemplen här autentiseringsuppgifterna direkt i texten. Vi rekommenderar hantera autentiseringsuppgifter med hjälp av miljövariabler, certifikat eller en lösning, till exempel Azure Key Vault i praktiken.

För att förenkla monteringen, spara om du vill autentiseringsuppgifter på noderna. Du kan sedan montera filresursen utan autentiseringsuppgifter. Utför följande två steg:

1. Kör den `cmdkey` kommandoradsverktyget med Startuppgiften i konfigurationen för lagringspooler. Detta sparar autentiseringsuppgifter på varje nod i Windows. Starta uppgiften kommandoraden är ungefär:

  ```
  cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

  ```

2. Ansluta till resursen på varje nod som en del av varje uppgift med hjälp av `net use`. Till exempel följande kommandorad för uppgift monterar filresursen som den *S:* enhet. Detta skulle följas av ett kommando eller skript som refererar till resursen. Cachelagrade autentiseringsuppgifter som ska användas i anropet till `net use`. Det här steget förutsätter att du använder samma användar-ID för uppgifter som du använde i Startuppgiften på poolen, som inte är lämplig för alla scenarier.

  ```
  cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
  ```

### <a name="c-example"></a>C#-exempel
Följande C#-exempel visar hur du spara autentiseringsuppgifter på en Windows-pool med Startuppgiften. Storage service filnamnet och lagring autentiseringsuppgifter skickas som konstanterna. Här, körs starta aktiviteten under ett vanligt (icke-administratörer) automatiskt-användarkonto med poolen omfattning.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

När du har lagrat använda autentiseringsuppgifterna som din aktivitet kommandorader för att montera resursen och refererar till resursen i läsning eller skrivning. Som ett grundläggande exempel på kommandoraden för aktiviteten i följande fragment används den `dir` kommandot för att lista filer i filresursen. Se till att köra varje projektaktivitet som använder samma [användaridentitet](batch-user-accounts.md) du använde för att köra Startuppgiften i poolen. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Montera en resurs på en Linux-pool

Azure-filresurser kan monteras i Linux-distributioner som använder den [CIFS kernel klienten](https://wiki.samba.org/index.php/LinuxCIFS). I följande exempel visas hur du monterar en filresurs på en pool med Ubuntu 16.04 LTS compute-noder. Om du använder en annan distributionsplats Linux liknar de allmänna stegen, men användning av package manager som är lämplig för distribution. Information och ytterligare exempel finns [Azure filer med Linux](../storage/files/storage-how-to-use-files-linux.md).

Installera först under en administratör användarens identitet, den `cifs-utils` paketet och skapa monteringspunkten (till exempel */mnt/MyAzureFileShare*) i det lokala filsystemet. En mapp för en monteringspunkt kan skapas var som helst i filsystemet, men det är vanliga konventionen för att skapa det under den `/mnt` mapp. Se till att inte skapa en monteringspunkt direkt vid `/mnt` (på Ubuntu) eller `/mnt/resource` (på andra distributioner).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Kör sedan den `mount` kommando för att montera filresursen, tillhandahåller dessa autentiseringsuppgifter:

* **Användarnamnet**: \<storageaccountname\>, till exempel *mystorageaccountname*
* **Lösenordet**: < StorageAccountKeyWhichEnds i == >, till exempel *XXXXXXXXXXXXXXXXXXXXX ==*

Följande kommando monterar en filresurs *myfileshare* i lagringskonto *mystorageaccountname* på */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

För enkelhetens skull klara exemplen här autentiseringsuppgifterna direkt i texten. Vi rekommenderar hantera autentiseringsuppgifter med hjälp av miljövariabler, certifikat eller en lösning, till exempel Azure Key Vault i praktiken.

Du kan kombinera alla de här stegen i en enda startuppgift eller köra dem i ett skript på en Linux-pool. Kör aktiviteten starta som en administratörsanvändare på poolen. Ange din startuppgift väntetiden för att slutföras innan du kör ytterligare uppgifter på poolen som hänvisar till resursen.

### <a name="python-example"></a>Python-exempel

Python-exemplet nedan visar hur du konfigurerar en Ubuntu-pool för att montera resursen i en start-aktivitet. Monteringspunkt, filen resursen slutpunkt och lagring autentiseringsuppgifter skickas som konstanterna. Starta aktiviteten körs under ett auto-administratörskonto med poolen omfattning.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration = batchmodels.VirtualMachineConfiguration(
        image_reference = batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id = "batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(_COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Efter montering använda resursen och definiera ett jobb, resursen i din kommandorader för aktiviteten. Följande grundläggande kommando använder exempelvis `ls` att lista filer i filresursen.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Nästa steg

* Andra alternativ för att läsa och skriva data i Batch, finns det [Batch funktionsöversikt](batch-api-basics.md) och [spara projekt- och utdata](batch-task-output.md).

* Se även den [Batch skeppsvarv](https://github.com/Azure/batch-shipyard) toolkit, som innehåller [skeppsvarv recept](https://github.com/Azure/batch-shipyard/tree/master/recipes) att distribuera filsystem för arbetsbelastningar för Batch-behållaren.