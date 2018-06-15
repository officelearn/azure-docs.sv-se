---
title: Köra uppgifter parallellt att använda beräkningsresurser effektivt - Azure Batch | Microsoft Docs
description: Öka effektiviteten och sänka kostnaderna genom att använda färre datornoder och kör samtidiga uppgifter på varje nod i ett Azure Batch-pool
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5106bbbb073908af7e7e8f045fa6fb60e8a306f4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316920"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Köra aktiviteter samtidigt för att maximera användningen av Batch-beräkningsnoder 

Genom att köra mer än en uppgift samtidigt på varje beräkningsnod i Azure Batch-pool, kan du maximera resursanvändning på ett mindre antal noder i poolen. Detta kan resultera i kortare jobbtider och den lägre kostnaden för vissa arbetsbelastningar.

När vissa scenarier utnyttja för en enskild uppgift trafikklass alla resurser för en nod, utnyttja flera situationer så att flera aktiviteter som ska dela dessa resurser:

* **Minimera dataöverföring** när en uppgift ska kunna dela data. I det här scenariot kan du kraftigt minska avgifter för överföring av data genom att kopiera delade data till ett mindre antal noder och aktiviteter körs parallellt på varje nod. Detta gäller särskilt om data som ska kopieras till varje nod måste överföras mellan geografiska områden.
* **Maximera minnesanvändning** när uppgifter kräver mycket minne, men endast under korta perioder tid och vid variabel tidpunkter under körningen. Du kan använda färre, men större, compute-noder med mer minne för att effektivt hantera sådana toppar. Dessa noder skulle ha flera aktiviteter som körs parallellt på varje nod, men varje uppgift vill utnyttja de noder stort minne vid olika tidpunkter.
* **Minimera antalet gränser för noden** när kommunikationen mellan noder krävs inom en pool. Programpooler som konfigurerats för kommunikationen mellan noder för närvarande begränsad till 50 compute-noder. Om varje nod i denna pool kan köra uppgifter parallellt, kan ett större antal uppgifter köras samtidigt.
* **Replikering av en lokal beräkningskluster**, till exempel när du först flytta en beräknings-miljö till Azure. Om din aktuella lösningen lokalt körs flera uppgifter per compute-nod, kan du öka det maximala antalet nod uppgifter för närmare spegling konfigurationen.

## <a name="example-scenario"></a>Exempel på ett scenario
Som exempel för att illustrera fördelarna med parallella uppgiftskörningen, anta att att tillämpningsprogrammet aktivitet har krav på processor och minne så att [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) noder är tillräckliga. Men 1 000 av dessa noder krävs för att slutföra jobbet inom den nödvändiga tidsperioden.

Istället för att använda Standard\_D1 noder som har 1 processorkärna och du kan använda [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) noder som har 16 kärnor och aktivera parallell aktivitetskörningen. Därför *16 gånger färre noder* kunde användas--i stället för 1 000 noder endast 63 skulle krävas. Dessutom om stora filer eller referensdata krävs för varje nod, bättre varaktighet för jobbet och effektivitet igen eftersom data kopieras till endast 63 noder.

## <a name="enable-parallel-task-execution"></a>Aktivera körning av parallell uppgift
Du kan konfigurera compute-noder för körning av parallell uppgift på pool-nivå. Med Batch .NET-biblioteket, ange den [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] egenskapen när du skapar en pool. Om du använder Batch REST API, ange den [maxTasksPerNode] [ rest_addpool] element i begärandetexten under skapande av poolen.

Azure Batch kan du ange högsta uppgifter per nod upp till fyra gånger (4 x) antal kärnor på noden. Till exempel om den har konfigurerats med noderna i storlek ”stor” (fyra kärnor) sedan `maxTasksPerNode` kan anges till 16. Mer information om antalet kärnor för varje nod storlekar finns [storlekar för molntjänster](../cloud-services/cloud-services-sizes-specs.md). Mer information om tjänsten begränsar finns [kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md).

> [!TIP]
> Se till att ta hänsyn till den `maxTasksPerNode` värdet när du skapar en [Autoskala formeln] [ enable_autoscaling] för din pool. Till exempel en formel som utvärderas `$RunningTasks` kraftigt kan påverkas av en ökning av uppgifter per nod. Se [automatiskt skala compute-noder i en Azure Batch-pool](batch-automatic-scaling.md) för mer information.
>
>

## <a name="distribution-of-tasks"></a>Distribution av uppgifter
När compute-noder i en pool kan köra aktiviteter samtidigt, är det viktigt att du anger du hur aktiviteter ska distribueras mellan noderna i poolen.

Med hjälp av den [CloudPool.TaskSchedulingPolicy] [ task_schedule] egenskap, kan du ange att aktiviteter ska tilldelas jämnt över alla noder i poolen (”spridning”). Eller så kan du ange att så många aktiviteter som möjligt ska tilldelas till varje nod innan uppgifterna har tilldelats en annan nod i poolen (”packkorg”).

Ett exempel på hur den här funktionen är viktig, bör du poolen med [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) noder (i exemplet ovan) som har konfigurerats med en [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] värde på 16. Om den [CloudPool.TaskSchedulingPolicy] [ task_schedule] har konfigurerats med en [ComputeNodeFillType] [ fill_type] av *Pack*, den kommer att maximera användningen av alla 16 kärnor för varje nod och tillåta en [autoskalning poolen](batch-automatic-scaling.md) att Rensa oanvända noder från poolen (noder utan inga tilldelade aktiviteter). Detta minimerar Resursanvändning och sparar pengar.

## <a name="batch-net-example"></a>Batch .NET-exempel
Detta [Batch .NET] [ api_net] API kodavsnitt visar en begäran om att skapa en pool som innehåller fyra stora noder med högst fyra uppgifter per nod. Det anger en schemaläggningstjänsten princip som fylls i varje nod med uppgifter före tilldela uppgifter till en annan nod i poolen. Mer information om att lägga till pooler med Batch .NET API finns [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Batch REST-exempel
Detta [Batch REST] [ api_rest] API utdrag visar en begäran om att skapa en pool som innehåller två stora noder med högst fyra uppgifter per nod. Mer information om att lägga till pooler med hjälp av REST-API finns [lägga till en pool med ett konto][rest_addpool].

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
> Du kan ange den `maxTasksPerNode` element och [MaxTasksPerComputeNode] [ maxtasks_net] egenskapen endast vid tidpunkten för skapandet av poolen. De kan inte ändras efter att poolen har redan skapats.
>
>

## <a name="code-sample"></a>Kodexempel
Den [ParallelNodeTasks] [ parallel_tasks_sample] projektet på GitHub illustrerar hur de [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] egenskapen.

Den här C#-konsolprogram använder den [Batch .NET] [ api_net] bibliotek för att skapa en pool med en eller flera compute-noder. En konfigurerbar antalet aktiviteter som körs på dessa noder för att simulera variabeln belastning. Utdata från programmet anger vilka noder utförs varje aktivitet. Programmet innehåller också en sammanfattning av jobbparametrar och varaktighet. Översikt över delen av utdata från två olika körningar av exempelprogrammet visas nedan.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Den första körningen av exempelprogrammet visar att med en enda nod i poolen och standardinställningen för en aktivitet per nod, varaktighet för jobbet är över 30 minuter.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Andra kör av exemplet visar en betydande minskning i jobbet varaktighet. Det beror på att poolen har konfigurerats med fyra uppgifter per nod som möjliggör körning av parallell uppgift och slutför jobbet i nästan ett kvartal i tid.

> [!NOTE]
> Jobbet varaktigheter i sammanfattningar ovan omfattar inte tiden för skapandet av poolen. Varje jobb ovan skickades till tidigare skapade pooler vars datornoderna fanns i den *inaktivt* tillstånd skicka för närvarande.
>
>

## <a name="next-steps"></a>Nästa steg
### <a name="batchlabs-heat-map"></a>BatchLabs termisk karta
[BatchLabs][batch_labs] är ett kostnadsfritt, fristående klientverktyg med omfattande funktioner för att skapa, felsöka och övervaka Azure Batch-program. BatchLabs innehåller en *termisk karta* funktion som tillhandahåller visualisering av körning av aktiviteten. När du kör den [ParallelTasks] [ parallel_tasks_sample] exempelprogrammet, kan du använda funktionen termisk karta enkelt visualisera körningen av parallella aktiviteter på varje nod.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

