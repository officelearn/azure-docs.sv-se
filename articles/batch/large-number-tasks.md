---
title: Skicka ett stort antal aktiviteter – Azure Batch | Microsoft Docs
description: Hur du effektivt skickar ett mycket stort antal aktiviteter i en enda Azure Batch-jobb
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 3e20aeb0e21eca5e4ac25206d638036f94a58202
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749703"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Skicka ett stort antal aktiviteter till ett Batch-jobb

När du kör storskaliga Azure Batch-arbetsbelastningar, kanske du vill skicka tiotusentals hundratals flera tusen eller ännu fler aktiviteter till ett enskilt jobb. 

Den här artikeln ger vägledning och vissa kodexempel för att skicka stora mängder uppgifter med avsevärt ökat genomflöde i ett enda batchjobb. När aktiviteterna har skickats kan ange de Batch-kö för bearbetning i poolen som du anger för jobbet.

## <a name="use-task-collections"></a>Använda samlingar för uppgift

Batch-API: er ger metoder för att effektivt lägga till aktiviteter till ett jobb som en *samling*, förutom en i taget. När du lägger till ett stort antal uppgifter bör du använda lämpliga metoder eller överlagringar att lägga till uppgifter som en samling. I allmänhet bör skapa du en uppgift samling genom att definiera aktiviteter som du kan iterera över en uppsättning inkommande filer eller parametrar för jobbet.

Den maximala storleken för den uppgift-samling som du kan lägga till i ett enda anrop är beroende av Batch-API som du använder:

* Följande Batch API: er begränsa mängden **100 uppgifter**. Gränsen kan vara mindre beroende på storleken på uppgifter – till exempel om aktiviteterna har ett stort antal resursfiler eller miljövariabler.

    * [REST-API](/rest/api/batchservice/task/addcollection)
    * [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API](/javascript/api/azure-batch/task?view=azure-node-latest)

  När du använder dessa API: er kan behöva du tillhandahåller logik för att dela upp antalet uppgifter att uppfylla samlingsgränsen och för att hantera fel och återförsök om det inte går att lägga till uppgifter. Om en uppgift samling är för stor för att lägga till, begäran genererar ett fel och bör försökas igen med färre steg.

* Följande API: er har stöd för mycket större uppgift samlingar – begränsas bara av RAM tillgänglighet på skickar klienten. Dessa API: er hantera transparent dividera uppgift samlingen i ”segment” för lågnivå-API: er och nya försök om det inte går att lägga till aktiviteter.

    * [.NET-API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java-API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Azure Batch CLI-tillägg](batch-cli-templates.md) med Batch CLI-mallar
    * [Python SDK-tillägg](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Öka genomflödet av uppgiften

Det kan ta lite tid att lägga till ett stort antal uppgifter i ett jobb – till exempel, upp till 1 minut att lägga till 20 000 aktiviteter via .NET API. Beroende på Batch-API och din arbetsbelastning kan förbättra du dataflödet uppgiften genom att ändra en eller flera av följande:

* **Uppgift storlek** – att lägga till stora uppgifter tar längre tid än att lägga till små. För att minska storleken på varje aktivitet i en samling kan du förenkla aktivitetens kommandorad, minska antalet miljövariabler eller hantera för körning av aktiviteten mer effektivt. Till exempel istället för att använda ett stort antal resursfiler installera aktivitetsberoenden med hjälp av en [startaktivitet](batch-api-basics.md#start-task) på poolen eller använder en [programpaket](batch-application-packages.md) eller [dockerbehållare](batch-docker-container-workloads.md).

* **Antalet parallella åtgärder** – beroende på Batch-API, öka dataflödet genom att öka det maximala antalet samtidiga aktiviteter med Batch-klient. Konfigurera den här inställningen med hjälp av den [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) -egenskapen i .NET-API eller `threads` parametern av metoder som [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)i Batch Python SDK-tillägget. (Den här egenskapen är inte tillgänglig i den interna Batch Python SDK.) Som standard är den här egenskapen inställd på 1, men högre för att förbättra dataflödet för åtgärder. Du balansera ökat genomflöde av konsumerande nätverksbandbredd och vissa CPU-prestanda. Uppgiften dataflödet ökar med upp till 100 gånger den `MaxDegreeOfParallelism` eller `threads`. I praktiken, bör du ange hur många samtidiga åtgärder under 100. 
 
  Azure Batch CLI-tillägget med Batch-mallar ökar antalet samtidiga åtgärder automatiskt baserat på antalet tillgängliga kärnor, men den här egenskapen kan inte konfigureras i CLI. 

* **HTTP-anslutningsgräns** -antalet samtidiga HTTP-anslutningar kan begränsa prestandan för Batch-klientens när det är att lägga till stort antal uppgifter. Antalet HTTP-anslutningar är begränsad med vissa API: er. När du utvecklar med .NET API, till exempel den [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) egenskapen anges till 2 som standard. Vi rekommenderar att du ökar värdet till ett antal nära eller större än antalet parallella åtgärder.

## <a name="example-batch-net"></a>Exempel: .NET för Batch

Följande C#-kodavsnitt visar inställningar för att konfigurera när du lägger till ett stort antal uppgifter med Batch .NET-API.

För att öka dataflödet för aktiviteten, öka värdet för den [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) egenskapen för den [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Exempel:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Lägg till en samling för uppgiften i jobbet med hjälp av lämplig överlagring för den [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) eller [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) metod. Exempel:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Exempel: Batch CLI-tillägg

Med hjälp av Azure Batch CLI-tillägg med [Batch CLI-mallar](batch-cli-templates.md), skapa ett jobb mall för JSON-fil som innehåller en [uppgift factory](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Uppgiften factory konfigurerar en samling relaterade aktiviteter för ett jobb från en enda aktivitetsdefinition.  

Följande är en exempelmall för jobbet för ett endimensionell parametrisk rensning jobb med ett stort antal aktiviteter – i det här fallet 250 000. Aktivitetens kommandorad är en enkel `echo` kommando.

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
Om du vill köra ett jobb med hjälp av mallen, se [använda Azure Batch CLI-mallar och filöverföring](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Exempel: Batch Python SDK-tillägg

För att använda Azure Batch Python SDK-tillägg måste du först installera Python SDK och tillägget:

```
pip install azure-batch
pip install azure-batch-extensions
```

Konfigurera en `BatchExtensionsClient` som använder SDK-tillägg:

```python

client = batch.BatchExtensionsClient(base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Skapa en samling aktiviteter att lägga till ett jobb. Exempel:


```python
tasks=list()
# Populate the list with your tasks
...

```

Lägg till den samling med hjälp av [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Ange den `threads` parameter för att öka antalet samtidiga åtgärder:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Tillägget Batch Python SDK stöder också parametrar för att lägga till aktiviteter till ett jobb med hjälp av en JSON-specifikation för en uppgift factory. Till exempel konfigurera jobbparametrar för en parametrisk rensning som liknar det i det föregående [Batch CLI mallen](#example-batch-cli-template) exempel:

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
                        "retentionTime":"PT1H"
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

Lägg till jobbparametrar för jobbet. Ange den `threads` parameter för att öka antalet samtidiga åtgärder:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du använder Azure Batch CLI-tillägg med [Batch CLI-mallar](batch-cli-templates.md).
* Läs mer om den [Batch Python SDK-tillägg](https://pypi.org/project/azure-batch-extensions/).
