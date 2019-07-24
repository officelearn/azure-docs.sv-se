---
title: Kör aktiviteter parallellt för att använda beräknings resurser effektivt – Azure Batch | Microsoft Docs
description: Öka effektiviteten och sänk kostnaderna genom att använda färre Compute-noder och köra samtidiga aktiviteter på varje nod i en Azure Batch pool
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cc6a607da2227ecf9acd6209e31b7aa0ef1c62d8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323367"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Kör uppgifter samtidigt för att maximera användningen av batch Compute-noder 

Genom att köra mer än en aktivitet samtidigt på varje Compute-nod i Azure Batch-poolen kan du maximera resursanvändningen på ett mindre antal noder i poolen. För vissa arbets belastningar kan detta resultera i kortare jobb tider och lägre kostnader.

Vissa scenarier drar nytta av att dedicera alla en nods resurser till en enda uppgift, men flera situationer drar nytta av att flera aktiviteter kan dela dessa resurser:

* **Minimera data överföring** när uppgifter kan dela data. I det här scenariot kan du drastiskt minska avgifterna för data överföring genom att kopiera delade data till ett mindre antal noder och köra aktiviteter parallellt på varje nod. Detta gäller särskilt om data som ska kopieras till varje nod måste överföras mellan geografiska regioner.
* **Maximera minnes användningen** när aktiviteter kräver stor mängd minne, men endast under korta tids perioder och vid varierande tidpunkter under körningen. Du kan använda färre, men större Compute-noder med mer minne för att effektivt hantera sådana toppar. De här noderna skulle ha flera aktiviteter som körs parallellt på varje nod, men varje aktivitet skulle dra nytta av nodernas plentiful minne vid olika tidpunkter.
* **Begränsa antalet nodnummer** vid kommunikation mellan noder i en pool. För närvarande är pooler som kon figurer ATS för kommunikation mellan noder begränsade till 50-datornoder. Om varje nod i en sådan pool kan köra aktiviteter parallellt, kan ett större antal uppgifter köras samtidigt.
* **Replikera ett lokalt beräknings kluster**, till exempel när du först flyttar en beräknings miljö till Azure. Om din aktuella lokala lösning kör flera aktiviteter per Compute-nod kan du öka det maximala antalet noder för att bättre kunna spegla konfigurationen.

## <a name="example-scenario"></a>Exempel på ett scenario
Som exempel för att illustrera fördelarna med parallell aktivitets körningen ska vi säga att ditt aktivitets program har processor-och minnes krav, så att [standard\_D1](../cloud-services/cloud-services-sizes-specs.md) -noderna är tillräckliga. Men för att slutföra jobbet inom den tid som krävs krävs 1 000 av de här noderna.

I stället för att\_använda standard D1-noder som har 1 processor kärna kan du använda [standard\_D14](../cloud-services/cloud-services-sizes-specs.md) -noder som har 16 kärnor och aktivera parallell körning av aktiviteter. Därför skulle *16 gånger färre noder* användas – i stället för 1 000-noder krävs bara 63. Om stora programfiler eller referens data krävs för varje nod förbättras dessutom jobbets varaktighet och effektivitet eftersom data kopieras till endast 63 noder.

## <a name="enable-parallel-task-execution"></a>Aktivera parallell körning av uppgift
Du konfigurerar Compute-noder för parallell körning av aktiviteter på Poolnivå. Med batch .net-biblioteket anger du elementet [CloudPool. MaxTasksPerComputeNode][maxtasks_net] property when you create a pool. If you are using the Batch REST API, set the [maxTasksPerNode][rest_addpool] i begär ande texten när du skapar en pool.

Med Azure Batch kan du ange aktiviteter per nod upp till (4x) Antalet Core-noder. Om poolen till exempel har kon figurer ATS med noder med storleken "stor" (fyra kärnor) `maxTasksPerNode` kan du ange 16. Men oavsett hur många kärnor noden har kan du inte ha fler än 256 aktiviteter per nod. Mer information om antalet kärnor för varje nods storlek finns i [storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Mer information om tjänst begränsningar finns i [kvoter och begränsningar för tjänsten Azure Batch](batch-quota-limit.md).

> [!TIP]
> Se till att ta `maxTasksPerNode` hänsyn till värdet när du skapar en autoskalning- [formel][enable_autoscaling] för poolen. En formel som utvärderar `$RunningTasks` kan till exempel dramatiskt påverkas av en ökning av aktiviteter per nod. Mer information finns i [skala Compute-noder automatiskt i en Azure Batch-pool](batch-automatic-scaling.md) .
>
>

## <a name="distribution-of-tasks"></a>Distribution av uppgifter
När Compute-noderna i en pool kan köra uppgifter samtidigt är det viktigt att ange hur du vill att aktiviteterna ska distribueras mellan noderna i poolen.

Genom att använda egenskapen [CloudPool. TaskSchedulingPolicy][task_schedule] kan du ange att aktiviteterna ska tilldelas jämnt över alla noder i poolen ("spridning"). Du kan också ange att så många aktiviteter som möjligt ska tilldelas varje nod innan aktiviteter tilldelas till en annan nod i poolen ("packning").

Som ett exempel på hur den här funktionen är värdefull bör du överväga poolen [med\_standard D14](../cloud-services/cloud-services-sizes-specs.md) -noder (i exemplet ovan) som är konfigurerad med en [CloudPool. MaxTasksPerComputeNode][maxtasks_net] value of 16. If the [CloudPool.TaskSchedulingPolicy][task_schedule] konfigureras med en [ ComputeNodeFillType][Fill_type] of *Pack*skulle maximera användningen av alla 16 kärnor i varje nod och tillåta en pool för automatisk [skalning](batch-automatic-scaling.md) att rensa oanvända noder från poolen (noder utan tilldelade aktiviteter). Detta minimerar resursanvändningen och sparar pengar.

## <a name="batch-net-example"></a>Batch .NET-exempel
Den här [batchen .net][api_net] API code snippet shows a request to create a pool that contains four nodes with a maximum of four tasks per node. It specifies a task scheduling policy that will fill each node with tasks prior to assigning tasks to another node in the pool. For more information on adding pools by using the Batch .NET API, see [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Batch-REST-exempel
Den här [batchen rest][api_rest] API snippet shows a request to create a pool that contains two large nodes with a maximum of four tasks per node. For more information on adding pools by using the REST API, see [Add a pool to an account][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> Du kan endast ange `maxTasksPerNode` element-och [MaxTasksPerComputeNode][maxtasks_net] -egenskapen när du skapar en pool. De kan inte ändras efter att en pool redan har skapats.
>
>

## <a name="code-sample"></a>Kodexempel
Egenskapen [ParallelNodeTasks][parallel_tasks_sample] project on GitHub illustrates the use of the [CloudPool.MaxTasksPerComputeNode][maxtasks_net] .

Det C# här konsol programmet använder [batch .net][api_net] -biblioteket för att skapa en pool med en eller flera Compute-noder. Den kör ett konfigurerbart antal aktiviteter på noderna för att simulera variabel belastning. Utdata från programmet anger vilka noder som utförde varje aktivitet. Programmet innehåller också en sammanfattning av jobb parametrarna och varaktigheten. Sammanfattnings delen av utdata från två olika körningar av exempel programmet visas nedan.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Den första körningen av exempel programmet visar att med en enda nod i poolen och standardinställningen för en aktivitet per nod, är jobbets varaktighet över 30 minuter.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Den andra körningen av exemplet visar en betydande minskning av jobbets varaktighet. Detta beror på att poolen har kon figurer ATS med fyra aktiviteter per nod, vilket möjliggör körning av parallell aktivitet för att slutföra jobbet i nästan en fjärdedel av tiden.

> [!NOTE]
> Jobbets varaktigheter i sammanfattningarna ovan omfattar inte skapande tiden för poolen. Varje jobb ovan skickades till tidigare skapade pooler vars datornoder var i inaktivt läge vid  överförings tillfället.
>
>

## <a name="next-steps"></a>Nästa steg
### <a name="batch-explorer-heat-map"></a>Batch Explorer termisk karta
[Batch Explorer][batch_labs] är ett kostnads fritt, fristående klient verktyg med omfattande funktioner som hjälper dig att skapa, felsöka och övervaka Azure Batch program. Batch Explorer innehåller en *termisk kart* funktion som tillhandahåller visualisering av uppgifts körning. När du kör exempel programmet [ParallelTasks][parallel_tasks_sample] kan du använda funktionen termisk karta för att enkelt visualisera körningen av parallella aktiviteter på varje nod.


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

