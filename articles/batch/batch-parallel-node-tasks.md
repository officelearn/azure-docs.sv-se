---
title: Kör aktiviteter parallellt för att optimera beräknings resurser
description: Öka effektiviteten och sänk kostnaderna genom att använda färre Compute-noder och köra samtidiga aktiviteter på varje nod i en Azure Batch pool
ms.topic: how-to
ms.date: 04/17/2019
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: e4c98244755cae7a606ebe26cbadef53ca5fd922
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926294"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Kör uppgifter samtidigt för att maximera användningen av batch Compute-noder 

Genom att köra mer än en aktivitet samtidigt på varje Compute-nod i Azure Batch-poolen kan du maximera resursanvändningen på ett mindre antal noder i poolen. För vissa arbets belastningar kan detta resultera i kortare jobb tider och lägre kostnader.

Vissa scenarier drar nytta av att dedicera alla en nods resurser till en enda uppgift, men flera situationer drar nytta av att flera aktiviteter kan dela dessa resurser:

* **Minimera data överföring** när uppgifter kan dela data. I det här scenariot kan du drastiskt minska avgifterna för data överföring genom att kopiera delade data till ett mindre antal noder och köra aktiviteter parallellt på varje nod. Detta gäller särskilt om data som ska kopieras till varje nod måste överföras mellan geografiska regioner.
* **Maximera minnes användningen** när aktiviteter kräver stor mängd minne, men endast under korta tids perioder och vid varierande tidpunkter under körningen. Du kan använda färre, men större Compute-noder med mer minne för att effektivt hantera sådana toppar. De här noderna skulle ha flera aktiviteter som körs parallellt på varje nod, men varje aktivitet skulle dra nytta av nodernas plentiful minne vid olika tidpunkter.
* **Begränsa antalet nodnummer** vid kommunikation mellan noder i en pool. För närvarande är pooler som kon figurer ATS för kommunikation mellan noder begränsade till 50-datornoder. Om varje nod i en sådan pool kan köra aktiviteter parallellt, kan ett större antal uppgifter köras samtidigt.
* **Replikera ett lokalt beräknings kluster**, till exempel när du först flyttar en beräknings miljö till Azure. Om din aktuella lokala lösning kör flera aktiviteter per Compute-nod kan du öka det maximala antalet noder för att bättre kunna spegla konfigurationen.

## <a name="example-scenario"></a>Exempelscenario
Som exempel för att illustrera fördelarna med parallell aktivitets körningen ska vi säga att ditt aktivitets program har processor-och minnes krav, så att [standard \_ D1](../cloud-services/cloud-services-sizes-specs.md) -noderna är tillräckliga. Men för att slutföra jobbet inom den tid som krävs krävs 1 000 av de här noderna.

I stället för att använda standard \_ D1-noder som har 1 processor kärna kan du använda [standard \_ D14](../cloud-services/cloud-services-sizes-specs.md) -noder som har 16 kärnor och aktivera parallell körning av aktiviteter. Därför skulle *16 gånger färre noder* användas – i stället för 1 000-noder krävs bara 63. Om stora programfiler eller referens data krävs för varje nod förbättras dessutom jobbets varaktighet och effektivitet eftersom data kopieras till endast 63 noder.

## <a name="enable-parallel-task-execution"></a>Aktivera parallell körning av uppgift
Du konfigurerar Compute-noder för parallell körning av aktiviteter på Poolnivå. Med batch .NET-biblioteket ställer du in egenskapen [CloudPool. MaxTasksPerComputeNode][maxtasks_net] när du skapar en pool. Om du använder batch-REST API anger du [maxTasksPerNode][rest_addpool] -elementet i begär ande texten när du skapar en pool.

Med Azure Batch kan du ange aktiviteter per nod upp till (4x) Antalet Core-noder. Om poolen till exempel har kon figurer ATS med noder med storleken "stor" (fyra kärnor) `maxTasksPerNode` kan du ange 16. Men oavsett hur många kärnor noden har kan du inte ha fler än 256 aktiviteter per nod. Mer information om antalet kärnor för varje nods storlek finns i [storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Mer information om tjänst begränsningar finns i [kvoter och begränsningar för tjänsten Azure Batch](batch-quota-limit.md).

> [!TIP]
> Se till att ta hänsyn till `maxTasksPerNode` värdet när du skapar en [autoskalning-formel][enable_autoscaling] för poolen. En formel som utvärderar kan till exempel `$RunningTasks` dramatiskt påverkas av en ökning av aktiviteter per nod. Mer information finns i [skala Compute-noder automatiskt i en Azure Batch-pool](batch-automatic-scaling.md) .
>
>

## <a name="distribution-of-tasks"></a>Distribution av uppgifter
När Compute-noderna i en pool kan köra uppgifter samtidigt är det viktigt att ange hur du vill att aktiviteterna ska distribueras mellan noderna i poolen.

Genom att använda egenskapen [CloudPool. TaskSchedulingPolicy][task_schedule] kan du ange att aktiviteterna ska tilldelas jämnt över alla noder i poolen ("spridning"). Du kan också ange att så många aktiviteter som möjligt ska tilldelas varje nod innan aktiviteter tilldelas till en annan nod i poolen ("packning").

Som ett exempel på hur den här funktionen är värdefull, bör du överväga poolen med [standard \_ D14](../cloud-services/cloud-services-sizes-specs.md) -noder (i exemplet ovan) som är konfigurerade med ett [CloudPool. MaxTasksPerComputeNode][maxtasks_net] -värde på 16. Om [CloudPool. TaskSchedulingPolicy][task_schedule] har kon figurer ATS med [ComputeNodeFillType][fill_type] ett ComputeNodeFillType *paket*maximerar det användningen av alla 16 kärnor i varje nod och tillåter att en pool för automatisk [skalning](batch-automatic-scaling.md) rensar oanvända noder från poolen (noder utan tilldelade aktiviteter). Detta minimerar resursanvändningen och sparar pengar.

## <a name="batch-net-example"></a>Batch .NET-exempel
I det här [batch .net][api_net] API-kodfragmentet visas en begäran om att skapa en pool som innehåller fyra noder med högst fyra aktiviteter per nod. Den specificerar en schemaläggnings princip för aktiviteter som fyller varje nod med aktiviteter innan aktiviteter tilldelas till en annan nod i poolen. Mer information om hur du lägger till pooler med hjälp av batch .NET-API: et finns i [metoden batchclient. PoolOperations. CreatePool][poolcreate_net].

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
Den här [batch rest][api_rest] API-kodfragmentet visar en begäran om att skapa en pool som innehåller två stora noder med högst fyra aktiviteter per nod. Mer information om hur du lägger till pooler med hjälp av REST API finns i [lägga till en pool till ett konto][rest_addpool].

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
[ParallelNodeTasks][parallel_tasks_sample] -projektet på GitHub illustrerar användningen av egenskapen [CloudPool. MaxTasksPerComputeNode][maxtasks_net] .

Det här C#-konsol programmet använder [batch .net][api_net] -biblioteket för att skapa en pool med en eller flera Compute-noder. Den kör ett konfigurerbart antal aktiviteter på noderna för att simulera variabel belastning. Utdata från programmet anger vilka noder som utförde varje aktivitet. Programmet innehåller också en sammanfattning av jobb parametrarna och varaktigheten. Sammanfattnings delen av utdata från två olika körningar av exempel programmet visas nedan.

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
> Jobbets varaktigheter i sammanfattningarna ovan omfattar inte skapande tiden för poolen. Varje jobb ovan skickades till tidigare skapade pooler vars datornoder var i *inaktivt* läge vid överförings tillfället.
>
>

## <a name="next-steps"></a>Nästa steg
### <a name="batch-explorer-heat-map"></a>Batch Explorer termisk karta
[Batch Explorer][batch_labs] är ett kostnads fritt, fristående klient verktyg med omfattande funktioner som hjälper dig att skapa, felsöka och övervaka Azure Batch program. Batch Explorer innehåller en *termisk kart* funktion som tillhandahåller visualisering av uppgifts körning. När du kör exempel programmet [ParallelTasks][parallel_tasks_sample] kan du använda funktionen termisk karta för att enkelt visualisera körningen av parallella aktiviteter på varje nod.


[api_net]: /dotnet/api/microsoft.azure.batch
[api_rest]: /rest/api/batchservice/
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[enable_autoscaling]: /rest/api/batchservice/pool/enableautoscale
[fill_type]: /dotnet/api/microsoft.azure.batch.common.computenodefilltype
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: /dotnet/api/microsoft.azure.batch.cloudpool
[rest_addpool]: /rest/api/batchservice/pool/add
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: /dotnet/api/microsoft.azure.batch.pooloperations
[task_schedule]: /dotnet/api/microsoft.azure.batch.cloudpool

