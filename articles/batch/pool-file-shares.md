---
title: Azure-filresurs för Azure Batch-pooler | Microsoft Docs
description: Hur du monterar en Azure Files-resurs från beräkningsnoder i en Linux- eller Windows-pool i Azure Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 1e9d039769e7fbcb9c2b7285aa727acd7322bcdf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127836"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Använda en Azure-filresurs med en Batch-pool

[Azure Files](../storage/files/storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via protokollet Server Message Block (SMB). Den här artikeln innehåller information och kod exempel för att montera och använda en Azure-filresurs på beräkningsnoder i poolen. Kodexemplen använda Batch .NET och Python SDK: er, men du kan utföra liknande åtgärder med andra Batch SDK: er och verktyg.

Batch innehåller inbyggt stöd för API för att läsa och skriva data med Azure Storage-blobbar. Men i vissa fall kan du få åtkomst till en Azure-filresurs från dina beräkningsnoder i poolen. Exempel: du har en äldre arbetsbelastning som beror på en SMB-filresurs eller dina aktiviteter måste få åtkomst till delade data eller delade resultat. 

## <a name="considerations-for-use-with-batch"></a>Överväganden för användning med Batch

* Överväg att använda en Azure-filresurs när du har pooler som kör ett relativt lågt antal parallella uppgifter. Granska den [mål för prestanda och skala](../storage/files/storage-files-scale-targets.md) att avgöra om Azure Files (som använder ett Azure Storage-konto) användas, baserat på din förväntade poolstorlek och antalet tillgångsfiler. 

* Azure-filresurser är [kostnadseffektiv](https://azure.microsoft.com/pricing/details/storage/files/) och kan konfigureras med data replikering till en annan region så är globalt redundant. 

* Du kan montera en Azure-filresurs samtidigt från en lokal dator.

* Se även allmänna [saker att tänka på](../storage/files/storage-files-planning.md) för Azure-filresurser.


## <a name="create-a-file-share"></a>Skapa en filresurs

[Skapa en filresurs](../storage/files/storage-how-to-create-file-share.md) i ett lagringskonto som är kopplad till ditt Batch-konto eller i ett separat lagringskonto.

## <a name="mount-a-share-on-a-windows-pool"></a>Montera en filresurs på en Windows-pool

Det här avsnittet innehåller steg och kodexempel för att montera och använda en Azure-fil dela på en pool med Windows-noder. Mer bakgrundsinformation finns i den [dokumentation](../storage/files/storage-how-to-use-files-windows.md) för att montera en Azure-filresurs på Windows. 

I Batch behöver du montera filresursen varje gång en aktivitet körs på en Windows-nod. Det är för närvarande inte möjligt att bevara nätverksanslutningen mellan aktiviteter på Windows-noder.

Till exempel innehålla en `net use` kommando för att montera filresursen som en del av varje uppgift från kommandoraden. Om du vill montera filresursen, krävs följande autentiseringsuppgifter:

* **Användarnamnet**: AZURE\\\<storageaccountname\>, till exempel AZURE\\*mystorageaccountname*
* **Lösenordet**: < StorageAccountKeyWhichEnds i == >, till exempel *XXXXXXXXXXXXXXXXXXXXX ==*

Kommandot monterar en filresurs *myfileshare* i storage-konto *mystorageaccountname* som den *S:* enhet:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

För enkelhetens skull skicka exemplen här autentiseringsuppgifter direkt i texten. I praktiken rekommenderar vi hantera autentiseringsuppgifter med hjälp av miljövariabler, certifikat eller en lösning, till exempel Azure Key Vault.

För att förenkla monteringen, sparar du kan också autentiseringsuppgifter på noderna. Du kan sedan montera resursen utan autentiseringsuppgifter. Utför följande två steg:

1. Kör den `cmdkey` kommandoradsverktyget med hjälp av en startaktivitet i poolkonfigurationen. Detta håller kvar autentiseringsuppgifter på varje Windows-nod. Starta aktivitetens kommandorad liknar:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Montera filresursen på varje nod som en del av varje aktivitet med `net use`. Till exempel följande kommandorad för uppgift monterar filresursen som den *S:* enhet. Detta skulle följas av ett kommando eller skript som refererar till resursen. Cachelagrade autentiseringsuppgifter som ska användas i anropet till `net use`. Det här steget förutsätter att du använder samma användar-ID för de uppgifter som du använde i Startuppgiften för poolen, vilket inte är lämplig för alla scenarier.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C#-exempel
Följande C# exempel visas hur du sparar autentiseringsuppgifter på en Windows-pool med hjälp av en startaktivitet. Storage service filnamnet och storage-autentiseringsuppgifter skickas som definierade konstanter. Här kan körs startaktiviteten under ett vanligt (inte administratör) automatiskt-användarkonto med pool omfattning.

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

När du har lagrat använda autentiseringsuppgifterna som din kommandorader för att montera filresursen och refererar till resursen i läs- eller skrivåtgärder. Ett grundläggande exempel aktivitetens kommandorad i följande kodavsnitt använder den `dir` för att lista filer i filresursen. Se till att köra varje projektaktivitet med samma [användaridentitet](batch-user-accounts.md) använde för att köra Startuppgiften i poolen. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Montera en filresurs på en Linux-pool

Azure-filresurser kan monteras i Linux-distributioner som använder den [CIFS kernel klienten](https://wiki.samba.org/index.php/LinuxCIFS). I följande exempel visas hur du monterar en filresurs på en pool med beräkningsnoder för Ubuntu 16.04 LTS. Om du använder en annan Linux-distribution, de allmänna stegen är liknande, men använda Pakethanteraren som är lämpliga för distributionen. Information och ytterligare exempel finns i [Använd Azure Files med Linux](../storage/files/storage-how-to-use-files-linux.md).

Installera först under en administratör användarens identitet, den `cifs-utils` paketera och skapa monteringspunkten (till exempel */mnt/MyAzureFileShare*) i det lokala filsystemet. Kan skapa en mapp för en monteringspunkt var som helst i filsystemet, men det är vanliga konventionen att skapa den här under de `/mnt` mapp. Glöm inte att skapa en monteringspunkt direkt vid `/mnt` (på Ubuntu) eller `/mnt/resource` (på andra distributioner).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Kör sedan den `mount` kommando för att montera filresursen med dessa autentiseringsuppgifter:

* **Användarnamn**: \<storageaccountname\>, till exempel *mystorageaccountname*
* **Lösenordet**: < StorageAccountKeyWhichEnds i == >, till exempel *XXXXXXXXXXXXXXXXXXXXX ==*

Kommandot monterar en filresurs *myfileshare* i storage-konto *mystorageaccountname* på */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

För enkelhetens skull skicka exemplen här autentiseringsuppgifter direkt i texten. I praktiken rekommenderar vi hantera autentiseringsuppgifter med hjälp av miljövariabler, certifikat eller en lösning, till exempel Azure Key Vault.

Du kan kombinera alla de här stegen i en enda startaktivitet eller köra dem i ett skript på en Linux-pool. Kör startaktiviteten som en administratörsanvändare för poolen. Ange startaktiviteten vänta ska slutföras innan du kör ytterligare aktiviteter på poolen som hänvisar till resursen.

### <a name="python-example"></a>Python-exempel

I följande Python-exempel visas hur du konfigurerar en Ubuntu-pool för att montera filresursen i en startaktivitet. Monteringspunkt, filen resurs slutpunkt och storage-autentiseringsuppgifter skickas som definierade konstanter. Startaktiviteten körs under ett administratörskonto automatiskt användare med pool omfattning.

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

När montera resursen och definierat ett jobb kan du använda resursen i din kommandorader. Till exempel följande grundläggande kommando använder `ls` filer ska listas i filresursen.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Nästa steg

* Andra alternativ att läsa och skriva data i Batch finns i den [Batch-funktionsöversikten](batch-api-basics.md) och [bevara jobb- och uppgiftsutdata](batch-task-output.md).

* Se även de [Batch skeppsvarv](https://github.com/Azure/batch-shipyard) toolkit, som innehåller [skeppsvarv recept](https://github.com/Azure/batch-shipyard/tree/master/recipes) att distribuera filsystem för Batch-behållararbetsbelastningar.