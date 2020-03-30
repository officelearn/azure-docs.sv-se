---
title: Skicka in ett stort antal uppgifter - Azure Batch | Microsoft-dokument
description: Så här skickar du effektivt in ett mycket stort antal uppgifter i ett enda Azure Batch-jobb
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: c3857e512da5fe4fceefa5f735ddc65f73e11623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026055"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Skicka ett stort antal aktiviteter till ett batchjobb

När du kör storskaliga Azure Batch-arbetsbelastningar kanske du vill skicka tiotusentals, hundratusentals eller ännu fler uppgifter till ett enda jobb. 

Den här artikeln ger vägledning och några kodexempel för att skicka ett stort antal uppgifter med väsentligt ökat dataflöde till ett enda batchjobb. När aktiviteter har skickats anger de batchkön för bearbetning på poolen som du anger för jobbet.

## <a name="use-task-collections"></a>Använda uppgiftssamlingar

Batch-API:erna innehåller metoder för att effektivt lägga till aktiviteter i ett jobb som en *samling,* utöver en i taget. När du lägger till ett stort antal aktiviteter bör du använda lämpliga metoder eller överbelastningar för att lägga till uppgifter som en samling. I allmänhet skapar du en uppgiftssamling genom att definiera aktiviteter när du itererar över en uppsättning indatafiler eller parametrar för jobbet.

Den maximala storleken på uppgiftssamlingen som du kan lägga till i ett enda anrop beror på vilket batch-API du använder:

* Följande batch-API:er begränsar samlingen till **100 aktiviteter**. Gränsen kan vara mindre beroende på aktiviteternas storlek, till exempel om aktiviteterna har ett stort antal resursfiler eller miljövariabler.

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Api för nod.js](/javascript/api/@azure/batch/task?view=azure-node-latest)

  När du använder dessa API:er måste du ange logik för att dela upp antalet aktiviteter för att uppfylla insamlingsgränsen och hantera fel och försök igen om tillägg av aktiviteter misslyckas. Om en aktivitetssamling är för stor för att läggas till genererar begäran ett fel och bör göras om igen med färre aktiviteter.

* Följande API:er stöder mycket större aktivitetssamlingar – begränsas endast av RAM-tillgänglighet på den skickande klienten. Dessa API:er hanterar genomskinligt uppdelning av aktivitetssamlingen i "segment" för API:er på lägre nivå och försöker igen om tillägg av aktiviteter misslyckas.

    * [.NET-API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java-API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Azure Batch CLI-tillägg](batch-cli-templates.md) med Batch CLI-mallar
    * [Python SDK-tillägg](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Öka dataflödet för uppgiftsöverföring

Det kan ta lite tid att lägga till en stor samling aktiviteter i ett jobb , till exempel upp till 1 minut för att lägga till 20 000 aktiviteter via .NET API. Beroende på batch-API:et och din arbetsbelastning kan du förbättra aktivitetsdataflödet genom att ändra ett eller flera av följande:

* **Aktivitetsstorlek** - Det tar längre tid att lägga till stora aktiviteter än att lägga till mindre. Om du vill minska storleken på varje aktivitet i en samling kan du förenkla aktivitetskommandoraden, minska antalet miljövariabler eller hantera krav för körning av uppgifter mer effektivt. I stället för att till exempel använda ett stort antal resursfiler installerar du aktivitetssamband med hjälp av en [startaktivitet](batch-api-basics.md#start-task) i poolen eller använder ett [programpaket](batch-application-packages.md) eller [Docker-behållare](batch-docker-container-workloads.md).

* **Antal parallella åtgärder** - Beroende på batch-API: et ökar du dataflödet genom att öka det maximala antalet samtidiga åtgärder av batch-klienten. Konfigurera den här inställningen med egenskapen [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) `threads` i .NET API eller parametern för metoder som [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) i tillägget Batch Python SDK. (Den här egenskapen är inte tillgänglig i den ursprungliga Batch Python SDK.) Som standard är den här egenskapen inställd på 1, men ange den högre för att förbättra dataflödet för åtgärder. Du avväger ökat dataflöde genom att förbruka nätverksbandbredd och vissa CPU-prestanda. Aktivitetsdataflödet ökar med upp till 100 gånger `MaxDegreeOfParallelism` eller `threads`. I praktiken bör du ange antalet samtidiga åtgärder under 100. 
 
  Azure Batch CLI-tillägget med batchmallar ökar antalet samtidiga åtgärder automatiskt baserat på antalet tillgängliga kärnor, men den här egenskapen kan inte konfigureras i CLI. 

* **HTTP-anslutningsgränser** - Antalet samtidiga HTTP-anslutningar kan begränsa batchklientens prestanda när den lägger till ett stort antal uppgifter. Antalet HTTP-anslutningar är begränsat med vissa API:er. När du utvecklar med .NET API, till exempel, är [egenskapen ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) inställd på 2 som standard. Vi rekommenderar att du ökar värdet till ett tal som ligger nära eller större än antalet parallella operationer.

## <a name="example-batch-net"></a>Exempel: Batch .NET

Följande C#-kodavsnitt visar inställningar som ska konfigureras när du lägger till ett stort antal uppgifter med batch-NET-API:et.

Öka värdet för egenskapen [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) för [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet)om du vill öka aktivitetsflödet. Ett exempel:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Lägg till en aktivitetsinsamling i jobbet med lämplig överbelastning av metoden [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) eller [AddTask.](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) Ett exempel:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Exempel: Batch CLI-tillägg

Med hjälp av Azure Batch CLI-tillägg med [Batch CLI-mallar](batch-cli-templates.md)skapar du en jobbmall JSON-fil som innehåller en [aktivitetsfabrik](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Aktivitetsfabriken konfigurerar en samling relaterade uppgifter för ett jobb från en enda aktivitetsdefinition.  

Följande är ett exempel på jobbmall för ett endimensionellt parametriskt svepjobb med ett stort antal aktiviteter - i det här fallet 250 000. Aktivitetskommandoraden är `echo` ett enkelt kommando.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
Information om hur du kör ett jobb med mallen finns i [Använda Azure Batch CLI-mallar och filöverföring](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Exempel: Batch Python SDK-tillägg

Om du vill använda Azure Batch Python SDK-tillägget installerar du först Python SDK och tillägget:

```
pip install azure-batch
pip install azure-batch-extensions
```

Konfigurera ett `BatchExtensionsClient` som använder SDK-tillägget:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Skapa en samling aktiviteter som du vill lägga till i ett jobb. Ett exempel:


```python
tasks = list()
# Populate the list with your tasks
...
```

Lägg till uppgiftssamlingen med [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Ange `threads` att parametern ska öka antalet samtidiga åtgärder:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Batch Python SDK-tillägget stöder också att lägga till aktivitetsparametrar i jobbet med hjälp av en JSON-specifikation för en aktivitetsfabrik. Konfigurera till exempel jobbparametrar för ett parametriskt svep som liknar det i föregående batch CLI-mallexempel:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Lägg till jobbparametrarna i jobbet. Ange `threads` att parametern ska öka antalet samtidiga åtgärder:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du använder Azure Batch CLI-tillägget med [Batch CLI-mallar](batch-cli-templates.md).
* Läs mer om [tillägget Batch Python SDK](https://pypi.org/project/azure-batch-extensions/).
