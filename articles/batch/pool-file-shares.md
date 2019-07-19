---
title: Azure-filresurs för Azure Batch pooler | Microsoft Docs
description: Montera en Azure Files resurs från datornoder i en Linux-eller Windows-pool i Azure Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
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
ms.openlocfilehash: 8c9d041648408b05b7cd160d5aea9dfb33ac061d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322390"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Använda en Azure-filresurs med en batch-pool

[Azure Files](../storage/files/storage-files-introduction.md) erbjuder fullständigt hanterade fil resurser i molnet som är tillgängliga via SMB-protokollet (Server Message Block). Den här artikeln innehåller information och kod exempel för montering och användning av en Azure-filresurs på poolens datornoder. I kod exemplen används batch .NET och python SDK: er, men du kan utföra liknande åtgärder med andra batch-SDK: er och verktyg.

Batch har inbyggt API-stöd för att använda Azure Storage blobbar för att läsa och skriva data. Men i vissa fall kanske du vill ha åtkomst till en Azure-filresurs från dina datornoder. Du kan till exempel ha en äldre arbets belastning som är beroende av en SMB-filresurs, eller så måste aktiviteterna komma åt delade data eller generera delade utdata. 

## <a name="considerations-for-use-with-batch"></a>Att tänka på när du använder batch

* Överväg att använda en Azure-filresurs när du har pooler som kör ett relativt lågt antal parallella aktiviteter. Granska [prestanda-och skalnings målen](../storage/files/storage-files-scale-targets.md) för att avgöra om Azure Files (som använder ett Azure Storage konto) ska användas, baserat på den förväntade storleken på poolen och antalet till gångs filer. 

* Azure-filresurser är [kostnads effektiva](https://azure.microsoft.com/pricing/details/storage/files/) och kan konfigureras med datareplikering till en annan region så att de är globalt redundanta. 

* Du kan montera en Azure-filresurs samtidigt från en lokal dator.

* Se även de allmänna [planerings övervägandena](../storage/files/storage-files-planning.md) för Azure-filresurser.


## <a name="create-a-file-share"></a>Skapa en filresurs

[Skapa en fil resurs](../storage/files/storage-how-to-create-file-share.md) i ett lagrings konto som är länkat till ditt batch-konto eller ett separat lagrings konto.

## <a name="mount-a-share-on-a-windows-pool"></a>Montera en resurs på en Windows-pool

Det här avsnittet innehåller steg och kod exempel för att montera och använda en Azure-filresurs på en pool med Windows-noder. Mer bakgrunds information finns i [dokumentationen](../storage/files/storage-how-to-use-files-windows.md) för att montera en Azure-filresurs i Windows. 

I batch måste du montera resursen varje gång en aktivitet körs på en Windows-nod. För närvarande går det inte att spara nätverks anslutningen mellan aktiviteter på Windows-noder.

Du kan till exempel lägga `net use` till ett kommando för att montera fil resursen som en del av varje aktivitets kommando rad. Följande autentiseringsuppgifter krävs för att montera fil resursen:

* **Användar namn**: Azure\\-\<storageaccountname,\>till exempel Azure\\*mystorageaccountname*
* **Lösenord**: \<StorageAccountKeyWhichEnds i = = >, till exempel *XXXXXXXXXXXXXXXXXXXXX = =*

Följande kommando monterar en fil resurs *myfileshare* i lagrings kontot *mystorageaccountname* som *S:* enhet:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

För enkelhetens skull skickar exemplen dessa autentiseringsuppgifter direkt i texten. I praktiken rekommenderar vi starkt att du hanterar autentiseringsuppgifterna med miljövariabler, certifikat eller en lösning som Azure Key Vault.

För att förenkla monterings åtgärden, kan du, om du vill, bevara autentiseringsuppgifterna för noderna. Sedan kan du montera resursen utan autentiseringsuppgifter. Utför följande två steg:

1. `cmdkey` Kör kommando rads verktyget med en start uppgift i konfigurationen för poolen. Detta behåller autentiseringsuppgifterna på varje Windows-nod. Kommando raden start uppgift liknar:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Montera resursen på varje nod som en del av varje aktivitet med `net use`. Följande kommando rad för aktivitet monterar till exempel fil resursen som enheten *S:* . Detta kan följas av ett kommando eller skript som refererar till resursen. Cachelagrade autentiseringsuppgifter används i anropet till `net use`. Det här steget förutsätter att du använder samma användar identitet för de uppgifter som du använde i Start aktiviteten på poolen, vilket inte är lämpligt för alla scenarier.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C#exempel
I följande C# exempel visas hur du behåller autentiseringsuppgifterna för en Windows-pool med en start uppgift. Lagrings fil tjänstens namn och autentiseringsuppgifter för lagring skickas som definierade konstanter. Här körs start aktiviteten under en standard-(icke-administratör) automatiskt användar konto med pool-scope.

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

När du har lagrat autentiseringsuppgifterna använder du åtgärds kommando raderna för att montera resursen och referera till resursen i Läs-eller Skriv åtgärder. Som ett grundläggande exempel använder `dir` aktivitetens kommando rad i följande kodfragment kommandot för att lista filer i fil resursen. Se till att köra varje jobb aktivitet med samma [användar identitet](batch-user-accounts.md) som du använde för att köra start uppgiften i poolen. 

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

Azure-filresurser kan monteras i Linux-distributioner med hjälp av [CIFS kernel-klienten](https://wiki.samba.org/index.php/LinuxCIFS). I följande exempel visas hur du monterar en fil resurs i en pool med Ubuntu 16,04 LTS-datornoder. Om du använder en annan Linux-distribution är de allmänna stegen liknande, men Använd paket hanteraren som passar för distributionen. Mer information och ytterligare exempel finns i [använda Azure Files med Linux](../storage/files/storage-how-to-use-files-linux.md).

Först, under en administratörs användar identitet, installerar `cifs-utils` du paketet och skapar monterings punkten (till exempel */mnt/MyAzureFileShare*) i det lokala fil systemet. En mapp för en monterings punkt kan skapas var som helst i fil systemet, men det är en vanlig konvention för att skapa `/mnt` den under mappen. Se till att du inte skapar någon monterings punkt `/mnt` direkt vid (på Ubuntu `/mnt/resource` ) eller (på andra distributioner).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Kör `mount` sedan kommandot för att montera fil resursen och ange följande autentiseringsuppgifter:

* **Användar namn**: \<storageaccountname\>, till exempel *mystorageaccountname*
* **Lösenord**: \<StorageAccountKeyWhichEnds i = = >, till exempel *XXXXXXXXXXXXXXXXXXXXX = =*

Följande kommando monterar en fil resurs *myfileshare* i lagrings kontot *mystorageaccountname* på */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

För enkelhetens skull skickar exemplen dessa autentiseringsuppgifter direkt i texten. I praktiken rekommenderar vi starkt att du hanterar autentiseringsuppgifterna med miljövariabler, certifikat eller en lösning som Azure Key Vault.

I en Linux-pool kan du kombinera alla dessa steg i en enda start uppgift eller köra dem i ett skript. Kör aktiviteten starta som administratörs användare för poolen. Ange att start aktiviteten ska vänta tills den har slutförts innan du kör ytterligare aktiviteter på poolen som hänvisar till resursen.

### <a name="python-example"></a>Python-exempel

Följande python-exempel visar hur du konfigurerar en Ubuntu-pool för att montera resursen i en start aktivitet. Monterings punkten, fil resursens slut punkt och autentiseringsuppgifterna för lagring skickas som definierade konstanter. Start aktiviteten körs under ett konto för automatiskt användar konto med pool-scope.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

När du har monterat resursen och definierat ett jobb använder du resursen i aktivitets kommando raderna. Följande grundläggande kommando används `ls` till exempel för att visa filer i fil resursen.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Nästa steg

* Andra alternativ för att läsa och skriva data i batch finns i [Översikt över batch-funktionen](batch-api-basics.md) och [Spara jobb-och Uppgiftsutdata](batch-task-output.md).

* Se även [batch Shipyard](https://github.com/Azure/batch-shipyard) Toolkit, som innehåller [Shipyard-recept](https://github.com/Azure/batch-shipyard/tree/master/recipes) för att distribuera fil system för arbets belastningar för batch-behållare.
