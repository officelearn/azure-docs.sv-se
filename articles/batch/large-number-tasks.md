---
title: Skicka ett stort antal aktiviteter
description: Hur man effektivt skickar ett mycket stort antal uppgifter i ett enda Azure Batch jobb
ms.topic: how-to
ms.date: 08/24/2018
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 0442be6f0c56aecc401ac4322c565a9ef999df63
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936902"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Skicka in ett stort antal uppgifter till ett batch-jobb

När du kör storskaliga Azure Batch arbets belastningar kanske du vill skicka flera tusen tusen, hundratals tusen eller ännu fler uppgifter till ett enda jobb. 

Den här artikeln innehåller rikt linjer och kod exempel för att skicka in ett stort antal uppgifter med betydligt större data flöde till ett enda batch-jobb. När uppgifterna har skickats anger de batchkön för bearbetning i den pool som du anger för jobbet.

## <a name="use-task-collections"></a>Använd aktivitets samlingar

Batch-API: erna tillhandahåller metoder för att effektivt lägga till aktiviteter till ett jobb som en *samling*, förutom en i taget. När du lägger till ett stort antal uppgifter bör du använda lämpliga metoder eller överlagringar för att lägga till aktiviteter som en samling. I allmänhet skapar du en uppgifts samling genom att definiera aktiviteter när du itererar över en uppsättning indatafiler eller parametrar för jobbet.

Den maximala storleken på aktivitets samlingen som du kan lägga till i ett enda anrop beror på vilket batch-API du använder:

* Följande batch-API: er begränsar samlingen till **100 uppgifter**. Gränsen kan vara mindre beroende på aktiviteternas storlek, till exempel om aktiviteterna har ett stort antal resursfiler eller miljövariabler.

    * [REST-API](/rest/api/batchservice/task/addcollection)
    * [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js-API](/javascript/api/@azure/batch/task?view=azure-node-latest)

  När du använder dessa API: er måste du tillhandahålla logik för att dela antalet aktiviteter för att uppfylla samlings gränsen och för att hantera fel och nya försök om aktiviteterna inte kan utföras. Om en aktivitets samling är för stor för att kunna läggas till genererar begäran ett fel och bör försöka igen med färre uppgifter.

* Följande API: er stöder mycket större aktivitets samlingar – begränsas bara av RAM-tillgänglighet på den sändande klienten. De här API: erna hanterar transparent delning av aktivitets samlingen i "segment" för API: er på lägre nivå och nya försök om det inte går att lägga till aktiviteter.

    * [.NET-API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java-API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Azure Batch CLI-tillägg](batch-cli-templates.md) med batch CLI-mallar
    * [Python SDK-tillägg](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Öka genom strömning av uppgifts överföring

Det kan ta lite tid att lägga till en stor mängd aktiviteter i ett jobb – till exempel upp till 1 minut för att lägga till 20 000-aktiviteter via .NET-API: et. Beroende på batch-API och din arbets belastning kan du förbättra aktivitets data flödet genom att ändra ett eller flera av följande:

* **Aktivitets storlek** – det tar längre tid att lägga till stora aktiviteter än att lägga till mindre. Om du vill minska storleken på varje aktivitet i en samling kan du förenkla aktivitetens kommando rad, minska antalet miljövariabler eller hantera krav för aktivitets körningen mer effektivt. I stället för att använda ett stort antal resursfiler måste du till exempel installera aktivitets beroenden med en [Start aktivitet](jobs-and-tasks.md#start-task) i poolen eller använda ett [programpaket](batch-application-packages.md) eller en [Docker-behållare](batch-docker-container-workloads.md).

* **Antal parallella åtgärder** – beroende på batch-API: et ökar du data flödet genom att öka det maximala antalet samtidiga åtgärder av batch-klienten. Konfigurera den här inställningen med egenskapen [BatchClientParallelOptions. MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) i .NET-API: et eller `threads` parameter för metoder som [TaskOperations. ADD_COLLECTION](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) i batch python SDK-tillägget. (Den här egenskapen är inte tillgänglig i den inbyggda python SDK: n för batch.) Som standard är den här egenskapen inställd på 1, men Ställ in den högre för att förbättra genomflödet av åtgärder. Du förbrukar större data flöden genom att använda nätverks bandbredden och vissa CPU-prestanda. Uppgifts flödet ökar med upp till 100 gånger `MaxDegreeOfParallelism` eller `threads` . I praktiken bör du ställa in antalet samtidiga åtgärder under 100. 
 
  Azure Batch CLI-tillägget med batch-mallar ökar antalet samtidiga åtgärder automatiskt baserat på antalet tillgängliga kärnor, men den här egenskapen kan inte konfigureras i CLI. 

* **Begränsningar för HTTP-anslutning** – antalet samtidiga http-anslutningar kan begränsa prestanda för batch-klienten när ett stort antal aktiviteter läggs till. Antalet HTTP-anslutningar är begränsat med vissa API: er. När du utvecklar med .NET-API: t kan [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) -egenskapen ha värdet 2 som standard. Vi rekommenderar att du ökar värdet till ett tal nära eller större än antalet parallella åtgärder.

## <a name="example-batch-net"></a>Exempel: batch .NET

Följande C#-kodfragment visar inställningar som du kan konfigurera när du lägger till ett stort antal aktiviteter med hjälp av batch .NET-API: et.

Öka aktivitetens data flöde genom att öka värdet för egenskapen [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) för [metoden batchclient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Ett exempel:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Lägg till en aktivitets samling i jobbet med lämplig överlagring för metoden [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) eller [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) . Ett exempel:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Exempel: batch CLI-tillägg

Använd Azure Batch CLI-tillägg med [batch CLI-mallar](batch-cli-templates.md)för att skapa en JSON-fil för jobbmall som innehåller en [aktivitets fabrik](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Uppgifts fabriken konfigurerar en samling relaterade uppgifter för ett jobb från en enda aktivitets definition.  

Följande är en exempel jobb mal len för en endimensionell parameter rensnings jobb med ett stort antal uppgifter – i det här fallet 250 000. Aktivitetens kommando rad är ett enkelt `echo` kommando.

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
Information om hur du kör ett jobb med mallen finns i [använda Azure Batch CLI-mallar och fil överföring](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Exempel: batch python SDK-tillägg

Om du vill använda Azure Batch python SDK-tillägget måste du först installera python SDK och tillägget:

```
pip install azure-batch
pip install azure-batch-extensions
```

Konfigurera en `BatchExtensionsClient` som använder SDK-tillägget:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Skapa en samling uppgifter som ska läggas till i ett jobb. Ett exempel:


```python
tasks = list()
# Populate the list with your tasks
...
```

Lägg till aktivitets samlingen med [Task. add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Ange `threads` parametern för att öka antalet samtidiga åtgärder:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Batch python SDK-tillägget stöder också att du lägger till aktivitets parametrar till jobb med hjälp av en JSON-specifikation för en aktivitets fabrik. Konfigurera till exempel jobb parametrar för en parameter svep som liknar den i föregående exempel för batch CLI-mallen:

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

Lägg till jobb parametrarna till jobbet. Ange `threads` parametern för att öka antalet samtidiga åtgärder:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att använda Azure Batch CLI-tillägget med [batch CLI-mallar](batch-cli-templates.md).
* Läs mer om [batch python SDK-tillägget](https://pypi.org/project/azure-batch-extensions/).
