---
title: Antal tillstånd för aktiviteter och noder
description: Räkna antalet Azure Batch uppgifter och Compute-noder för att hantera och övervaka batch-lösningar.
ms.date: 09/07/2018
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: d75ff1806f61b54bd058926f95c7c53b66aaf243
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780159"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Övervaka batch-lösningar genom att räkna aktiviteter och noder efter tillstånd

För att övervaka och hantera storskaliga Azure Batch-lösningar måste du ha exakt räkning av resurser i olika tillstånd. Azure Batch tillhandahåller effektiva åtgärder för att få dessa antal för batch- *aktiviteter* och *Compute-noder*. Använd de här åtgärderna i stället för ibland tids krävande List frågor som returnerar detaljerad information om stora mängder av aktiviteter eller noder.

* [Hämta aktivitets antal][rest_get_task_counts] får ett sammanlagt antal aktiva, pågående och slutförda aktiviteter i ett jobb, samt uppgifter som har lyckats eller misslyckats. 

  Genom att räkna aktiviteter i varje tillstånd kan du enklare Visa jobb förloppet för en användare eller upptäcka oväntade fördröjningar eller fel som kan påverka jobbet. Hämta aktivitets antal är tillgängligt som batch service API-version 2017 -06-01.5.1 och relaterade SDK: er och verktyg.

* Antal noder i en [adresspool visar][rest_get_node_counts] antalet dedikerade och låg prioritets-datornoder i varje pool i olika tillstånd: skapa, inaktiv, offline, omstart, omstart, avbildning, start och andra. 

  Genom att räkna noder i varje tillstånd kan du fastställa när du har tillräckliga beräknings resurser för att köra dina jobb och identifiera eventuella problem med dina pooler. Antalet noder i en adresspool är tillgängligt som batch service API-version 2018 -03-01.6.1 och relaterade SDK: er och verktyg.

Om du använder en version av tjänsten som inte stöder åtgärderna antal aktiviteter eller antal noder, använder du en lista i stället för att räkna resurserna. Använd också en List fråga för att få information om andra batch-resurser, till exempel program, filer och jobb. Mer information om hur du använder filter för att lista frågor finns i [skapa frågor för att lista batch-resurser effektivt](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Antal uppgifts tillstånd

Åtgärden hämta antal aktiviteter räknas i följande tillstånd:

- **Aktiv** – en uppgift som är i kö och kan köras, men som för närvarande inte är kopplad till en Compute-nod. En aktivitet är också `active` [beroende av en överordnad uppgift](batch-task-dependencies.md) som ännu inte har slutförts. 
- **Körs** – en aktivitet som har tilldelats till en Compute-nod, men som ännu inte har slutförts. En aktivitet räknas som `running` när dess tillstånd är antingen `preparing` eller `running` , enligt vad som anges i [Hämta information om en uppgifts][rest_get_task] åtgärd.
- **Slutförd** – en aktivitet som inte längre är berättigad att köras, eftersom den antingen har slutförts eller slutat fungera och även förbrukat gränsen för återförsök. 
- **Lyckades** – en uppgift vars resultat av uppgifts körning är `success` . Batch avgör om en aktivitet har lyckats eller misslyckats genom att kontrol lera `TaskExecutionResult` egenskapen för egenskapen [executionInfo][rest_get_exec_info] .
- **Misslyckades** En aktivitet vars resultat av uppgifts körning är `failure` .

Följande exempel på .NET-kod visar hur du hämtar antal aktiviteter efter status: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Du kan använda ett liknande mönster för REST och andra språk som stöds för att hämta aktivitets antal för ett jobb. 

> [!NOTE]
> API-versioner för batch-tjänsten innan 2018 -08-01.7.0 returnerar även en `validationStatus` egenskap i svars antalet Hämta uppgift. Den här egenskapen anger om batch kontrollerade antalet tillstånd för konsekvens med de tillstånd som rapporteras i API: et för List aktiviteter. Värdet `validated` anger bara att den här gruppen har kontroller ATS för konsekvens minst en gång för jobbet. Värdet för `validationStatus` egenskapen anger inte om antalet som erhåller aktivitets antal returnerar är aktuellt.
>

## <a name="node-state-counts"></a>Antal Node-tillstånd

Antalet noder i noden lista räknar antalet beräknade noder efter följande tillstånd i varje pool. Separata mängd antal anges för dedikerade noder och noder med låg prioritet i varje pool.

- **Skapa** – en Azure-ALLOKERAD virtuell dator som ännu inte har börjat anslutas till en pool.
- **Inaktiv** – en tillgänglig Compute-nod som inte kör en aktivitet för tillfället.
- **LeavingPool** – en nod som lämnar poolen, antingen på grund av att användaren uttryckligen har tagit bort den eller eftersom poolen ändrar storlek eller skalar ned igen.
- **Offline** – en nod som batchen inte kan använda för att schemalägga nya aktiviteter.
- I **förväg –** en nod med låg prioritet som har tagits bort från poolen eftersom Azure frigjorde den virtuella datorn. En `preempted` nod kan återinitieras när den virtuella datorns kapacitet med låg prioritet är tillgänglig.
- Startar **om** en nod som startar om.
- **Reavbildning** – en nod där operativ systemet installeras om.
- **Kör** en nod som kör en eller flera aktiviteter (förutom start aktiviteten).
- **Starta** en nod där batch-tjänsten startas. 
- **StartTaskFailed** – en nod där [Start uppgiften][rest_start_task] misslyckades och förbrukade alla återförsök och på vilken som `waitForSuccess` anges på Start aktiviteten. Noden kan inte användas för att köra uppgifter.
- **Okänd** -en nod som tappade kontakten med batch-tjänsten och vars tillstånd inte är känt.
- **Oanvändbar** -en nod som inte kan användas för uppgifts körning på grund av fel.
- **WaitingForStartTask** – en nod där start aktiviteten började köras, men `waitForSuccess` har angetts och start aktiviteten inte har slutförts.

Följande C#-kodfragment visar hur man listar antal noder för alla pooler i det aktuella kontot:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Följande C#-kodfragment visar hur man listar antal noder för en specifik pool i det aktuella kontot.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

Du kan använda ett liknande mönster för REST och andra språk som stöds för att hämta antal noder för pooler.
 
## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
* Information om hur du använder filter för frågor som visar batch-resurser finns i [skapa frågor för att lista batch-resurser effektivt](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

