---
title: Antal tillstånd för uppgifter och noder – Azure Batch | Microsoft-dokument
description: Räkna tillståndet för Azure Batch-uppgifter och beräkna noder för att hantera och övervaka batch-lösningar.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a7b58e96918d26851812aa96c18043121c081e94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023930"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Övervaka batchlösningar genom att räkna uppgifter och noder efter tillstånd

För att övervaka och hantera storskaliga Azure Batch-lösningar behöver du korrekta antal resurser i olika tillstånd. Azure Batch tillhandahåller effektiva åtgärder för att hämta dessa antal för *batch-uppgifter* och *beräkningsnoder*. Använd dessa åtgärder i stället för potentiellt tidskrävande listfrågor som returnerar detaljerad information om stora samlingar av uppgifter eller noder.

* [Hämta antal aktivitetsantal][rest_get_task_counts] får ett samlat antal aktiva, kör och slutförda aktiviteter i ett jobb och uppgifter som lyckades eller misslyckades. 

  Genom att räkna aktiviteter i varje tillstånd kan du lättare visa jobbförloppet för en användare eller upptäcka oväntade fördröjningar eller fel som kan påverka jobbet. Hämta aktivitetsantal är tillgängligt från och med Batch Service API version 2017-06-01.5.1 och relaterade SDK:er och verktyg.

* [List Pool Nod Counts][rest_get_node_counts] får antalet dedikerade och lågprioriterade beräkningsnoder i varje pool som finns i olika lägen: skapa, inaktiv, offline, föregripa, starta om, återskapa, starta och starta och andra. 

  Genom att räkna noder i varje tillstånd kan du bestämma när du har tillräckliga beräkningsresurser för att köra dina jobb och identifiera potentiella problem med dina pooler. Antal listpoolnoder är tillgängligt från och med Batch Service API-version 2018-03-01.6.1 och relaterade SDK:er och verktyg.

Om du använder en version av tjänsten som inte stöder åtgärder för antalet aktiviteter eller nodräkna åtgärder, använder du en listfråga i stället för att räkna dessa resurser. Använd också en listfråga för att hämta information om andra batchresurser, till exempel program, filer och jobb. Mer information om hur du använder filter på listfrågor finns i [Skapa frågor för att lista batchresurser effektivt](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Antal aktivitetstillstånd

Åtgärden Hämta aktivitetsräkn räknar aktiviteter enligt följande tillstånd:

- **Aktiv** - En aktivitet som står i kö och kan köras, men som för närvarande inte har tilldelats en beräkningsnod. En aktivitet `active` är också om den är [beroende av en överordnad aktivitet](batch-task-dependencies.md) som ännu inte har slutförts. 
- **Kör** - En aktivitet som har tilldelats en beräkningsnod, men som ännu inte har slutförts. En aktivitet räknas `running` som när `preparing` dess `running`tillstånd är antingen eller , som anges av [Hämta information om en aktivitetsåtgärd.][rest_get_task]
- **Slutförd** - En uppgift som inte längre kan köras, eftersom den antingen har slutförts eller slutförts utan framgång och även uttömt gränsen för återförsök. 
- **Lyckades** - En uppgift vars `success`resultat av körning av uppgiften är . Batch avgör om en aktivitet har lyckats `TaskExecutionResult` eller misslyckats genom att kontrollera egenskapen [för egenskapen executionInfo.][rest_get_exec_info]
- **Det gick inte att** En uppgift vars resultat `failure`av körning av uppgifter är .

Följande .NET-kodexempel visar hur du hämtar aktivitetsantal efter tillstånd: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Du kan använda ett liknande mönster för REST och andra språk som stöds för att få aktivitetsantal för ett jobb. 

> [!NOTE]
> Api-versioner för batchtjänst före 2018-08-01.7.0 returnerar också en `validationStatus` egenskap i svaret Hämta aktivitetsantal. Den här egenskapen anger om Batch kontrollerat tillståndsantalet för konsekvens med de tillstånd som rapporterats i API:et för listaktiviteter. Värdet anger `validated` endast att Batch kontrolleras efter konsekvens minst en gång för jobbet. Värdet för `validationStatus` egenskapen anger inte om antalet som hämta aktivitetsantal returnerar för närvarande är uppdaterade.
>

## <a name="node-state-counts"></a>Antal nodtillstånd

Åtgärden Lista poolnod räknar beräkningsnoder med följande tillstånd i varje pool. Separata aggregerade antal tillhandahålls för dedikerade noder och noder med låg prioritet i varje pool.

- **Skapa** - En Azure-allokerad virtuell dator som ännu inte har börjat ansluta till en pool.
- **Inaktiv** - En tillgänglig beräkningsnod som för närvarande inte kör en aktivitet.
- **LeavingPool** - En nod som lämnar poolen, antingen för att användaren uttryckligen tagit bort den eller för att poolen ändrar storlek eller ändrar ned automatiskt.
- **Offline** - En nod som batch inte kan använda för att schemalägga nya aktiviteter.
- **Preempted** - En nod med låg prioritet som togs bort från poolen eftersom Azure återtog den virtuella datorn. En `preempted` nod kan initieras igen när ersättningshögprioriterad VM-kapacitet är tillgänglig.
- **Omstart** - En nod som startar om.
- **Reimaging** - En nod som operativsystemet installeras om på.
- **Köra** - En nod som kör en eller flera aktiviteter (förutom startaktiviteten).
- **Start** - En nod som batch-tjänsten startar på. 
- **StartTaskFailed** - En nod där [startaktiviteten][rest_start_task] misslyckades och tog `waitForSuccess` ut alla återförsök och som anges på startaktiviteten. Noden kan inte kan köras för aktiviteter.
- **Okänd** - En nod som förlorat kontakten med batch-tjänsten och vars tillstånd inte är känt.
- **Oersättlig** - En nod som inte kan användas för körning av uppgifter på grund av fel.
- **WaitingForStartTask** - En nod där startaktiviteten `waitForSuccess` började köras, men är inställd och startaktiviteten har inte slutförts.

Följande C#-kodavsnitt visar hur du listar nodantal för alla pooler i det aktuella kontot:

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
Följande C#-kodavsnitt visar hur du listar nodantal för en viss pool i det aktuella kontot.

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
Du kan använda ett liknande mönster för REST och andra språk som stöds för att få nodantal för pooler.
 
## <a name="next-steps"></a>Nästa steg

* Mer information om begrepp och funktioner relaterade till Batch-tjänsten finns i [funktionsöversikten för Batch](batch-api-basics.md). I artikeln beskrivs de primära batchresurserna, till exempel pooler, beräkningsnoder, jobb och uppgifter, och en översikt över tjänstens funktioner.

* Information om hur du använder filter på frågor som listar batchresurser finns i [Skapa frågor för att lista batchresurser effektivt](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

