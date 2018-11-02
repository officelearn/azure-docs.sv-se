---
title: Antal tillstånd för aktiviteter och noder – Azure Batch | Microsoft Docs
description: Räkna tillståndet för Azure Batch-aktiviteterna och beräkningsnoder för att hantera och övervaka Batch-lösningar.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: danlep
ms.openlocfilehash: 5c65ee3ecc851b289c32a480ecad8f2df3aaad95
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913894"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Övervaka Batch-lösningar genom att räkna aktiviteter och nod efter tillstånd

Om du vill övervaka och hantera lösningar för storskaliga Azure Batch, behöver du korrekt antal resurser i olika tillstånd. Azure Batch får du effektiv drift för att få detta antal för Batch *uppgifter* och *beräkningsnoder*. Använd åtgärderna i stället för tidsödande lista: frågor som returnerar detaljerad information om stora samlingar av uppgifter eller noder.

* [Hämta uppgift räknar] [ rest_get_task_counts] hämtar en sammanställd antal aktiva, pågående och slutförda uppgifter i ett jobb och uppgifter som har lyckats eller misslyckats. 

  Genom att räkna uppgifter i varje status kan du enklare visar förloppet för en användare eller identifiera oväntade fördröjningar eller fel som kan påverka jobbet. Hämta uppgift räknar blir tillgänglig Batch Service API-versionen 2017-06-01.5.1 och relaterade SDK: er och verktyg.

* [Lista över poolen noden räknar] [ rest_get_node_counts] hämtar antalet dedikerade och lågprioriterade beräkningsnoder i varje pool som är i olika lägen: skapa, inaktiv, offline, frånkopplas, omstart, avbildningen på, startar och andra. 

  Genom att räkna noder i varje tillstånd, kan du bestämma när du har tillräckligt med beräkningsresurser för att köra dina jobb och identifiera eventuella problem med dina pooler. Lista Pool noden räknar blir tillgänglig Batch Service API-versionen 2018-03-01.6.1 och relaterade SDK: er och verktyg.

Om du använder en version av tjänsten som inte stöder de antal eller nod antal åtgärderna kan du använda en listfråga i stället att räkna dessa resurser. Också använda en listfråga för att få information om andra Batch-resurser, till exempel program, filer och jobb. Mer information om hur du tillämpar filter för att lista: frågor finns i [skapa frågor för att visa Batch-resurser effektivt](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Status för aktivitet räknar

Åtgärden hämta uppgift räknar antalet aktiviteter efter följande tillstånd:

- **Aktiva** – en aktivitet som är i kö och kan köras, men inte är tilldelad till en beräkningsnod. En aktivitet är också `active` om det är [beroende på en överordnad aktivitet](batch-task-dependencies.md) som ännu inte har slutförts. 
- **Kör** – en aktivitet som har tilldelats till en beräkningsnod, men ännu inte har slutförts. En uppgift räknas som `running` när tillståndet är antingen `preparing` eller `running`, som anges av den [hämta information om en aktivitet] [ rest_get_task] igen.
- **Slutfört** – en aktivitet som inte längre kan användas för att köra, eftersom den antingen har slutförts, eller slutfört med fel och även förbrukat sin gräns för återförsök. 
- **Lyckades** – en aktivitet vars resultat av körning av aktiviteten är `success`. Batch avgör om en aktivitet har lyckats eller misslyckats genom att markera den `TaskExecutionResult` egenskapen för den [executionInfo] [ rest_get_exec_info] egenskapen.
- **Det gick inte** en aktivitet vars resultat av körning av aktiviteten är `failure`.

Följande .NET-kodexempel visar hur du hämtar uppgift antal per tillstånd: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Du kan använda ett liknande mönster för REST och andra språk som stöds för att hämta uppgiften antalet för ett jobb. 

> [!NOTE]
> Batch-tjänstens API-versioner innan 2018-08-01.7.0 returnerar också en `validationStatus` egenskapen hämta uppgift räknar-svar. Den här egenskapen anger om Batch markerat tillståndet antal för konsekvens med tillstånd som rapporteras i API: T för listan uppgifter. Värdet `validated` bara anger att Batch markerats för att få konsekvens minst en gång för jobbet. Värdet för den `validationStatus` egenskapen anger inte om de antal som hämta uppgift räknar returnerar är för närvarande uppdaterade.
>

## <a name="node-state-counts"></a>Nodtillstånd räknar

Lista Pool noden räknar åtgärden antalen beräkningsnoder av följande tillstånd i varje pool. Separata sammanlagda antal tillhandahålls för dedikerade noder och noder med låg prioritet i varje pool.

- **Skapa** -allokerats av en Azure virtuell dator som inte har startats att ansluta till en pool.
- **Inaktiva** – en tillgänglig compute-nod som inte kör en uppgift.
- **LeavingPool** – en nod som finns kvar i poolen, eftersom användaren bort explicit eller automatisk skalning eller ändrar storlek på poolen är nere.
- **Offline** – en nod som Batch kan inte använda för att schemalägga nya aktiviteter.
- **Allokeringar** -uzel med låg prioritet som har tagits bort från poolen eftersom Azure frigöra den virtuella datorn. En `preempted` noden kan ominitieras när VM-kapaciteten som ersättning med låg prioritet är tillgänglig.
- **Starta om** – en nod startas om.
- **Återställningen av avbildningen** – en nod där operativsystemet installeras.
- **Kör** – en nod som kör en eller flera aktiviteter (andra än startaktiviteten).
- **Startar** – en nod som Batch-tjänsten startas. 
- **StartTaskFailed** – en nod där den [startaktivitet] [ rest_start_task] misslyckades och uttömt alla återförsök, och där `waitForSuccess` har angetts för startaktiviteten. Noden kan inte användas för att köra uppgifter.
- **Okänd** – en nod som tappat kontakten med Batch-tjänsten och vars status är okänd.
- **Oanvändbara** – en nod som inte kan användas för körning av aktiviteten på grund av fel.
- **WaitingForStartTask** – en nod som startats startaktiviteten körs, men `waitForSuccess` har angetts och början har inte slutförts.

Följande C#-kodavsnitt visar hur du listar noden antal för alla pooler i det aktuella kontot:

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
Följande C#-kodavsnitt visar hur du listar noden antal för en viss pool i det aktuella kontot.

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
Du kan använda ett liknande mönster för REST och andra språk som stöds för att hämta noden antal för pooler.
 
## <a name="next-steps"></a>Nästa steg

* Mer information om begrepp och funktioner relaterade till Batch-tjänsten finns i [funktionsöversikten för Batch](batch-api-basics.md). Artikeln beskriver de viktigaste Batch-resurserna, till exempel pooler, beräkningsnoder, jobb och aktiviteter och ger en översikt över funktioner i tjänsten.

* Information om hur du tillämpar filter till frågor med Batch-resurser finns i [skapa frågor för att visa Batch-resurser effektivt](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get#executionInfo
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

