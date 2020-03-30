---
title: Azure-filresurs för Azure Batch-pooler | Microsoft-dokument
description: Så här monterar du en Azure Files-resurs från beräkningsnoder i en Linux- eller Windows-pool i Azure Batch.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: 156dad25af5abd1b4d5db32569faf09a23fadfb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022519"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Använda en Azure-filresurs med en batchpool

[Azure Files](../storage/files/storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet (Server Message Block). Den här artikeln innehåller information och kodexempel för montering och användning av en Azure-filresurs på poolberäkningsnoder. Kodexemplen använder batch -NET- och Python-SDK:er, men du kan utföra liknande åtgärder med andra batch-SDK:er och verktyg.

Batch ger inbyggt API-stöd för att använda Azure Storage-blobbar för att läsa och skriva data. I vissa fall kanske du vill komma åt en Azure-filresurs från dina poolberäkningsnoder. Du har till exempel en äldre arbetsbelastning som är beroende av en SMB-filresurs, eller dina uppgifter måste komma åt delade data eller producera delad utdata. 

## <a name="considerations-for-use-with-batch"></a>Överväganden för användning med Batch

* Överväg att använda en Azure-filresurs när du har pooler som kör ett relativt lågt antal parallella uppgifter. Granska [prestanda- och skalningsmålen](../storage/files/storage-files-scale-targets.md) för att avgöra om Azure-filer (som använder ett Azure Storage-konto) ska användas, med tanke på din förväntade poolstorlek och antalet tillgångsfiler. 

* Azure-filresurser är [kostnadseffektiva](https://azure.microsoft.com/pricing/details/storage/files/) och kan konfigureras med datareplikering till en annan region så är globalt redundanta. 

* Du kan montera en Azure-filresurs samtidigt från en lokal dator.

* Se även de allmänna [planeringsövervägandena](../storage/files/storage-files-planning.md) för Azure-filresurser.


## <a name="create-a-file-share"></a>Skapa en filresurs

[Skapa en filresurs](../storage/files/storage-how-to-create-file-share.md) i ett lagringskonto som är länkat till ditt Batch-konto eller i ett separat lagringskonto.

## <a name="mount-a-share-on-a-windows-pool"></a>Montera en resurs i en Windows-pool

Det här avsnittet innehåller steg och kodexempel för att montera och använda en Azure-filresurs på en pool med Windows-noder. Mer bakgrund finns i [dokumentationen](../storage/files/storage-how-to-use-files-windows.md) för montering av en Azure-filresurs i Windows. 

I Batch måste du montera resursen varje gång en aktivitet körs på en Windows-nod. För närvarande går det inte att bevara nätverksanslutningen mellan aktiviteter på Windows-noder.

Inkludera till exempel `net use` ett kommando för att montera filresursen som en del av varje aktivitetskommandorad. För att montera filresursen behövs följande autentiseringsuppgifter:

* **Användarnamn**:\\\<AZURE storageaccountname\>,\\till exempel AZURE*mystorageaccountname*
* **Lösenord** \<: StorageAccountKeyWhichEnds in==>, till exempel *XXXXXXXXXXXXXXXXX==*

Följande kommando monterar en filresurs *myfileshare* i lagringskontot *mystorageaccountname* som *S:* enhet:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

För enkelhetens skull skickar exemplen här autentiseringsuppgifterna direkt i text. I praktiken rekommenderar vi starkt att du hanterar autentiseringsuppgifterna med hjälp av miljövariabler, certifikat eller en lösning som Azure Key Vault.

För att förenkla monteringsåtgärden, kan du behålla autentiseringsuppgifterna på noderna. Sedan kan du montera resursen utan autentiseringsuppgifter. Utför följande två steg:

1. Kör `cmdkey` kommandoradsverktyget med en startuppgift i poolkonfigurationen. Detta kvarstår autentiseringsuppgifterna på varje Windows-nod. Kommandoraden Startaktivitet liknar:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Montera resursen på varje nod som `net use`en del av varje uppgift med . Följande aktivitetskommandorad monterar till exempel filresursen som *S:-enheten.* Detta skulle följas av ett kommando eller skript som refererar till resursen. Cachelagrade autentiseringsuppgifter används `net use`i anropet till . Det här steget förutsätter att du använder samma användaridentitet för de uppgifter som du använde i startaktiviteten i poolen, vilket inte är lämpligt för alla scenarier.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C# exempel
I följande C#-exempel visas hur du behåller autentiseringsuppgifterna i en Windows-pool med hjälp av en startuppgift. Lagringsfiltjänstnamnet och lagringsautentiseringsuppgifterna skickas som definierade konstanter. Här körs startaktiviteten under ett standardkonto (icke-administratör) med poolomfattning.

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

När du har lagrat autentiseringsuppgifterna använder du aktivitetskommandoraderna för att montera resursen och referera till resursen i läs- eller skrivåtgärder. Som ett grundläggande exempel använder aktivitetskommandoraden i `dir` följande kodavsnitt kommandot för att lista filer i filresursen. Se till att köra varje jobbuppgift med samma [användaridentitet](batch-user-accounts.md) som du använde för att köra startaktiviteten i poolen. 

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

Azure-filresurser kan monteras i Linux-distributioner med [cifs-kärnklienten](https://wiki.samba.org/index.php/LinuxCIFS). I följande exempel visas hur du monterar en filresurs på en pool med Ubuntu 16.04 LTS-beräkningsnoder. Om du använder en annan Linux-distribution är de allmänna stegen likartade, men använder pakethanteraren som är lämplig för distributionen. Mer information och ytterligare exempel finns i [Använda Azure-filer med Linux](../storage/files/storage-how-to-use-files-linux.md).

Installera `cifs-utils` paketet under en administratörsanvändaridentitet och skapa monteringspunkten (till exempel */mnt/MyAzureFileShare)* i det lokala filsystemet. En mapp för en monteringspunkt kan skapas var som helst i filsystemet, men det är vanligt att skapa detta under `/mnt` mappen. Var noga med att inte `/mnt` skapa en monteringspunkt `/mnt/resource` direkt vid (på Ubuntu) eller (på andra distributioner).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Kör sedan `mount` kommandot för att montera filresursen och ange följande autentiseringsuppgifter:

* **Användarnamn:** \<storageaccountname\>, till exempel *mystorageaccountname*
* **Lösenord** \<: StorageAccountKeyWhichEnds in==>, till exempel *XXXXXXXXXXXXXXXXX==*

Följande kommando monterar en filresurs *myfileshare* i lagringskonto *mystorageaccountname* på */ mnt / MyAzureFileShare:* 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

För enkelhetens skull skickar exemplen här autentiseringsuppgifterna direkt i text. I praktiken rekommenderar vi starkt att du hanterar autentiseringsuppgifterna med hjälp av miljövariabler, certifikat eller en lösning som Azure Key Vault.

På en Linux-pool kan du kombinera alla dessa steg i en enda startuppgift eller köra dem i ett skript. Kör startuppgiften som administratörsanvändare i poolen. Ange att startuppgiften ska vänta på att slutföras innan du kör ytterligare aktiviteter i poolen som refererar till resursen.

### <a name="python-example"></a>Python exempel

Följande Python-exempel visar hur du konfigurerar en Ubuntu-pool för att montera resursen i en startaktivitet. Monteringspunkten, filresursslutpunkten och lagringsautentiseringsuppgifterna skickas som definierade konstanter. Startuppgiften körs under ett automatiskt användarkonto för administratörer med poolomfattning.

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

När du har monterat resursen och definierat ett jobb använder du resursen i aktivitetskommandoraderna. Följande grundläggande kommando används `ls` till exempel för att lista filer i filresursen.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Nästa steg

* Andra alternativ för att läsa och skriva data i Batch finns i [översikten för funktionen Batch](batch-api-basics.md) och [Beständiga jobb- och aktivitetsutdata](batch-task-output.md).

* Se även [batchvarvsverktygslådan,](https://github.com/Azure/batch-shipyard) som innehåller [Varvsrecept](https://github.com/Azure/batch-shipyard/tree/master/recipes) för att distribuera filsystem för batchcontainerarbetsbelastningar.
