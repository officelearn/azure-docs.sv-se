---
title: Kör aktiviteter parallellt för att optimera beräkningsresurser - Azure Batch
description: Öka effektiviteten och sänka kostnaderna genom att använda färre beräkningsnoder och köra samtidiga uppgifter på varje nod i en Azure Batch-pool
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5465771cb97ef9d8d5c451a6bafc61c4621d3c4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023641"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Kör aktiviteter samtidigt för att maximera användningen av batchberäkningsnoder 

Genom att köra mer än en aktivitet samtidigt på varje beräkningsnod i din Azure Batch-pool kan du maximera resursanvändningen på ett mindre antal noder i poolen. För vissa arbetsbelastningar kan detta resultera i kortare jobbtider och lägre kostnad.

Vissa scenarier kan dra nytta av att alla resurser för en nod avsätts till en enskild aktivitet, men flera situationer kan dra nytta av att flera aktiviteter kan dela dessa resurser:

* **Minimera dataöverföringen** när uppgifter kan dela data. I det här fallet kan du dramatiskt minska dataöverföringsavgifter genom att kopiera delade data till ett mindre antal noder och köra aktiviteter parallellt på varje nod. Detta gäller särskilt om de data som ska kopieras till varje nod måste överföras mellan geografiska regioner.
* **Maximera minnesanvändningen** när uppgifter kräver en stor mängd minne, men bara under korta tidsperioder och vid varierande tidpunkter under körningen. Du kan använda färre, men större, beräkningsnoder med mer minne för att effektivt hantera sådana toppar. Dessa noder skulle ha flera aktiviteter som körs parallellt på varje nod, men varje aktivitet skulle dra nytta av nodernas rikliga minne vid olika tidpunkter.
* **Begränsning av nodnummergränser** när kommunikation mellan nod krävs i en pool. För närvarande är pooler som konfigurerats för internodkommunikation begränsade till 50 beräkningsnoder. Om varje nod i en sådan pool kan utföra aktiviteter parallellt kan ett större antal aktiviteter utföras samtidigt.
* **Replikera ett lokalt beräkningskluster,** till exempel när du först flyttar en beräkningsmiljö till Azure. Om din aktuella lokala lösning kör flera uppgifter per beräkningsnod kan du öka det maximala antalet nodaktiviteter för att spegla konfigurationen närmare.

## <a name="example-scenario"></a>Exempel på ett scenario
Som ett exempel för att illustrera fördelarna med parallella uppgiftskörning, låt oss säga att ditt aktivitetsprogram har CPU- och minneskrav så att [Standard\_D1-noder](../cloud-services/cloud-services-sizes-specs.md) är tillräckliga. Men för att avsluta jobbet inom den tid som krävs behövs 1 000 av dessa noder.

I stället\_för att använda Standard D1-noder som har 1 CPU-kärna kan du använda [Standard\_D14-noder](../cloud-services/cloud-services-sizes-specs.md) som har 16 kärnor vardera och aktivera parallell körning av uppgifter. Därför kan *16 gånger färre noder* användas - i stället för 1 000 noder, skulle endast 63 krävas. Om stora programfiler eller referensdata krävs för varje nod förbättras dessutom jobbvaraktigheten och effektiviteten igen eftersom data kopieras till endast 63 noder.

## <a name="enable-parallel-task-execution"></a>Aktivera körning av parallella uppgifter
Du konfigurerar beräkningsnoder för parallella aktivitetskörning på poolnivå. Med batch .NET-biblioteket anger du egenskapen [CloudPool.MaxTasPerComputeNode][maxtasks_net] när du skapar en pool. Om du använder batch-REST API: et anger du elementet [maxTasksPerNode][rest_addpool] i begärandetexten när poolen skapas.

Med Azure Batch kan du ange uppgifter per nod upp till (4x) antalet kärnnoder. Om poolen till exempel är konfigurerad med noder av storleken "Large" (fyra kärnor) kan den `maxTasksPerNode` anges till 16. Oavsett hur många kärnor noden har kan du dock inte ha fler än 256 aktiviteter per nod. Mer information om antalet kärnor för var och en av nodstorlekarna finns i [Storlekar för molntjänster](../cloud-services/cloud-services-sizes-specs.md). Mer information om tjänstbegränsningar finns i [Kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md).

> [!TIP]
> Var noga med att `maxTasksPerNode` ta hänsyn till värdet när du skapar en [formel för automatisk skalning][enable_autoscaling] för din pool. En formel som utvärderas `$RunningTasks` kan till exempel påverkas dramatiskt av en ökning av aktiviteter per nod. Se [Skala beräkningsnoder automatiskt i en Azure Batch-pool](batch-automatic-scaling.md) för mer information.
>
>

## <a name="distribution-of-tasks"></a>Fördelning av uppgifter
När beräkningsnoderna i en pool kan utföra aktiviteter samtidigt är det viktigt att ange hur du vill att aktiviteterna ska fördelas över noderna i poolen.

Med egenskapen [CloudPool.TaskSchedulingPolicy][task_schedule] kan du ange att uppgifter ska tilldelas jämnt mellan alla noder i poolen ("spridning"). Du kan också ange att så många aktiviteter som möjligt ska tilldelas varje nod innan aktiviteter tilldelas en annan nod i poolen ("packning").

Som ett exempel på hur den här funktionen är värdefull bör du tänka på poolen med [Standard\_D14-noder](../cloud-services/cloud-services-sizes-specs.md) (i exemplet ovan) som är konfigurerad med ett [CloudPool.MaxTasksPerComputeNode-värde][maxtasks_net] på 16. Om [CloudPool.TaskSchedulingPolicy][task_schedule] har konfigurerats med en [ComputeNodeFillType][fill_type] of *Pack*maximerar den användningen av alla 16 kärnor för varje nod och gör det möjligt för en [automatisk skalningspool](batch-automatic-scaling.md) att beskära oanvända noder från poolen (noder utan några tilldelade uppgifter). Detta minimerar resursanvändningen och sparar pengar.

## <a name="batch-net-example"></a>Exempel på batch .NET
Den här [batchen .NET][api_net] API-kodavsnitt visar en begäran om att skapa en pool som innehåller fyra noder med högst fyra aktiviteter per nod. Den anger en princip för schemaläggning av aktiviteter som ska fylla varje nod med aktiviteter innan aktiviteter tilldelas till en annan nod i poolen. Mer information om hur du lägger till pooler med hjälp av BATCH .NET API finns i [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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

## <a name="batch-rest-example"></a>Exempel på batch-REST
Det här API-kodavsnittet för [batch-REST][api_rest] visar en begäran om att skapa en pool som innehåller två stora noder med högst fyra aktiviteter per nod. Mer information om hur du lägger till pooler med REST API finns i [Lägga till en pool i ett konto][rest_addpool].

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
> Du kan `maxTasksPerNode` bara ange egenskapen element och [MaxTasPerComputeNode][maxtasks_net] när poolen skapas. De kan inte ändras när en pool redan har skapats.
>
>

## <a name="code-sample"></a>Kodexempel
[Projektet ParallelNodeTasks][parallel_tasks_sample] på GitHub illustrerar användningen av egenskapen [CloudPool.MaxTasPerComputeNode.][maxtasks_net]

Det här C#-konsolprogrammet använder [batch-NET-biblioteket][api_net] för att skapa en pool med en eller flera beräkningsnoder. Det körs ett konfigurerbart antal aktiviteter på dessa noder för att simulera variabel belastning. Utdata från programmet anger vilka noder som utfördes varje aktivitet. Programmet innehåller också en sammanfattning av jobbparametrarna och varaktigheten. Den sammanfattande delen av utdata från två olika körningar av exempelprogrammet visas nedan.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Den första körningen av exempelprogrammet visar att med en enda nod i poolen och standardinställningen för en aktivitet per nod är jobbvaraktigheten över 30 minuter.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Den andra körningen av exemplet visar en signifikant minskning av jobbvaraktigheten. Detta beror på att poolen har konfigurerats med fyra aktiviteter per nod, vilket gör att parallella aktivitetskörningar kan slutföra jobbet i nästan en fjärdedel av tiden.

> [!NOTE]
> Jobbvaraktigheterna i sammanfattningarna ovan inkluderar inte tid för att skapa pool. Var och en av jobben ovan skickades till tidigare skapade pooler vars beräkningsnoder var i *inaktivt* tillstånd vid inlämningstillfället.
>
>

## <a name="next-steps"></a>Nästa steg
### <a name="batch-explorer-heat-map"></a>Uppvärmningskarta för batchutforskare
[Batch Explorer][batch_labs] är ett kostnadsfritt, utsålt, fristående klientverktyg som hjälper dig att skapa, felsöka och övervaka Azure Batch-program. Batch Explorer innehåller en *värmekartfunktion* som ger visualisering av körning av uppgifter. När du kör [exempelprogrammet ParallelTasks][parallel_tasks_sample] kan du använda funktionen Värmekarta för att enkelt visualisera körningen av parallella uppgifter på varje nod.


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

