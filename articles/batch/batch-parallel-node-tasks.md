---
title: Köra parallellt att använda beräkningsresurser effektivt – Azure Batch | Microsoft Docs
description: Öka effektiviteten och lägre kostnader genom att använda färre beräkningsnoder och köra uppgifter på varje nod i ett Azure Batch-pool
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5583ccb6076dae2f33e265b95387bcd35aa9fa4d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547290"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Köra aktiviteter samtidigt för att maximera användningen av Batch-beräkningsnoder 

Genom att köra fler än en uppgift samtidigt på varje beräkningsnod i Azure Batch-pool, kan du maximera resursanvändningen på ett mindre antal noder i poolen. För vissa arbetsbelastningar kan detta resultera i kortare jobbtiden och lägre kostnader.

Medan vissa scenarier utnyttja tilldela alla resurser för en nod till en enda aktivitet, utnyttja flera situationer så att flera aktiviteter dela dessa resurser:

* **Minimera dataöverföring** när en uppgift ska kunna dela data. I det här scenariot kan du avsevärt minska kostnaderna för dataöverföring genom att kopiera delade data till ett mindre antal noder och köra uppgifter parallellt på varje nod. Detta gäller särskilt om data ska kopieras till varje nod måste överföras mellan geografiska regioner.
* **Maximera minnesanvändning** när uppgifter kräver mycket minne, men endast under korta perioder tid, och på variabeln gånger under körning. Du kan använda färre, men större, compute-noder med mer minne för att effektivt hantera sådana toppar. Dessa noder skulle ha flera aktiviteter som körs parallellt på varje nod, men varje aktivitet skulle kunna utnyttja nodernas stort minne vid olika tidpunkter.
* **Minimera antalet begränsningar för noden** när kommunikation mellan noder krävs i en pool. Pooler som har konfigurerats för kommunikation mellan noder är för närvarande begränsade till 50 compute-noder. Om varje nod i en sådan pool kan köra uppgifter parallellt, kan ett större antal uppgifter köras samtidigt.
* **Replikera en lokala beräkningskluster**, till exempel när du först flytta en beräkningsmiljö till Azure. Om din aktuella lokala lösning körs flera aktiviteter per compute-nod kan öka du det maximala antalet nodaktiviteter för närmare spegling av konfigurationen.

## <a name="example-scenario"></a>Exempel på ett scenario
Som ett exempel som illustrerar fördelarna med att köra åtgärder parallellt kan anta att dina aktivitetsprogrammet har krav på processor och minne, så att [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) noder är tillräckliga. Men för att kunna slutföra arbetet i den begärda tiden, 1 000 av dessa noder krävs.

Istället för att använda Standard\_D1 noder som har 1 processorkärna, kan du använda [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) noder som har 16 kärnor och aktivera köra åtgärder parallellt. Därför *16 gånger färre noder* skulle kunna användas – i stället för 1 000 noder endast 63 skulle krävas. Även om stora filer eller referensdata krävs för varje nod, bättre varaktighet för jobb och effektivitet igen eftersom data kopieras till endast 63 noder.

## <a name="enable-parallel-task-execution"></a>Aktivera körning av parallella uppgifter
Du kan konfigurera beräkningsnoder för att köra åtgärder parallellt på pool-nivå. Med Batch .NET-biblioteket, ställa in den [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] egenskapen när du skapar en pool. Om du använder Batch REST-API, ange den [maxTasksPerNode] [ rest_addpool] element i begärandetexten när poolen skapas.

Azure Batch kan du ange maximal aktiviteter per nod upp till fyra gånger (4 x) antalet kärnor för noden. Till exempel om poolen har konfigurerats med noder i storleken för ”stora” (fyra kärnor), `maxTasksPerNode` kan anges till 16. Mer information om antalet kärnor för varje nodstorlekar finns [storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Mer information om tjänstbegränsningar finns [kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md).

> [!TIP]
> Se till att ta hänsyn till den `maxTasksPerNode` värdet när du skapar en [autoskalningsformel] [ enable_autoscaling] för poolen. Exempelvis kan en formel som utvärderar `$RunningTasks` avsevärt kan påverkas av en ökning av aktiviteter per nod. Se [skala beräkningsnoder automatiskt i en Azure Batch-pool](batch-automatic-scaling.md) för mer information.
>
>

## <a name="distribution-of-tasks"></a>Distribution av uppgifter
När compute-noder i poolen kan köra uppgifter samtidigt, är det viktigt att ange hur du vill att uppgifterna som ska distribueras mellan noderna i poolen.

Med hjälp av den [CloudPool.TaskSchedulingPolicy] [ task_schedule] egenskapen, kan du ange att aktiviteter ska tilldelas jämnt över alla noder i poolen (”sprida”). Du kan också ange att så många aktiviteter som möjligt ska tilldelas till varje nod innan aktiviteter tilldelas till en annan nod i poolen (”paketering av statistik”).

Ett exempel på hur den här funktionen är användbar, bör du poolen med [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) noder (i exemplet ovan) som har konfigurerats med en [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] värdet för 16. Om den [CloudPool.TaskSchedulingPolicy] [ task_schedule] har konfigurerats med en [ComputeNodeFillType] [ fill_type] av *Pack*, den kommer att maximera användningen av alla 16 kärnor för varje nod och tillåta en [autoskalning pool](batch-automatic-scaling.md) att Rensa oanvända noder i poolen (noder utan inga tilldelade aktiviteter). Detta minskar resursanvändningen och sparar du pengar.

## <a name="batch-net-example"></a>Batch .NET-exempel
Detta [Batch .NET] [ api_net] API kodfragmentet visar en begäran om att skapa en pool som innehåller fyra noder med högst fyra aktiviteter per nod. Den anger en princip som fylls i varje nod med uppgifter innan aktiviteter tilldelas till en annan nod i poolen för schemaläggning. Läs mer om att lägga till pooler med hjälp av Batch .NET-API, [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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

## <a name="batch-rest-example"></a>Batch REST-exempel
Detta [Batch REST] [ api_rest] API-fragment visas en begäran att skapa en pool som innehåller två stora noder med högst fyra aktiviteter per nod. Läs mer om att lägga till pooler med hjälp av REST-API, [lägga till en pool till ett konto][rest_addpool].

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
> Du kan ange den `maxTasksPerNode` element och [MaxTasksPerComputeNode] [ maxtasks_net] egenskapen endast när poolen skapas. De kan inte ändras när en pool har redan skapats.
>
>

## <a name="code-sample"></a>Kodexempel
Den [ParallelNodeTasks] [ parallel_tasks_sample] projekt på GitHub illustrerar användningen av den [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] egenskapen.

Den här C#-konsolprogram som använder den [Batch .NET] [ api_net] biblioteket för att skapa en pool med beräkningsnoder i en eller flera. Ett konfigurerbart antal uppgifter körs på dessa noder för att simulera varierande belastning. Utdata från programmet anger vilka noder som körs varje aktivitet. Programmet innehåller också en sammanfattning av jobbparametrar och varaktighet. Sammanfattning av delen av utdata från två olika körningar av exempelprogrammet som visas nedan.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Den första körningen i exempelprogrammet visar att med en enda nod i poolen och standardinställningen för en aktivitet per nod, varaktighet för jobb är under 30 minuter.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Andra körning av exemplet visar en betydande minskning i varaktighet för jobb. Det beror på att poolen har konfigurerats med fyra aktiviteter per nod som kan köra åtgärder parallellt och slutför jobbet i nästan en fjärdedel av tiden.

> [!NOTE]
> Jobbet varaktigheter i sammanfattningar ovan omfattar inte tiden för skapandet av poolen. Var och en av jobb som ovan skickades till tidigare skapade pooler vars beräkningsnoder som fanns i den *inaktiv* tillstånd när bidrag.
>
>

## <a name="next-steps"></a>Nästa steg
### <a name="batch-explorer-heat-map"></a>Batch Explorer termisk karta
[Batch Explorer] [ batch_labs] är ett kostnadsfritt, omfattande funktioner, fristående klientverktyg för att skapa, felsöka och övervaka Azure Batch-program. Batch Explorer innehåller en *termisk karta* funktion som innehåller visualisering av körning av aktiviteten. När du kör den [ParallelTasks] [ parallel_tasks_sample] exempelprogrammet, du kan använda funktionen termisk karta för att enkelt visualisera körning av parallella uppgifter på varje nod.


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

