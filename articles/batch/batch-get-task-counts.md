---
title: "Övervaka förloppet för ett jobb av räknar uppgifter efter status - Azure Batch | Microsoft Docs"
description: "Övervaka förloppet för ett jobb genom att anropa åtgärden hämta uppgift räknar antalet aktiviteter för ett projekt. Du kan hämta ett antal aktiva, körs och slutförda uppgifter och aktiviteter som har lyckades eller misslyckades."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: tamram
ms.openlocfilehash: ceff59d7063b60a1344a47489d3d73e0e8ee07df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>Antal aktiviteter efter status för att övervaka jobbförloppet (förhandsgranskning)

Azure Batch tillhandahåller ett effektivt sätt att övervaka förloppet för ett jobb som körs tillhörande uppgifter. Du kan anropa den [hämta uppgift räknar] [ rest_get_task_counts] åtgärd för att ta reda på hur många aktiviteter är i tillståndet active, körs, eller slutfördes och hur många har har lyckats eller misslyckats. Genom att räkna antalet aktiviteter i varje du enklare visa jobbets status till en användare eller identifiera oväntade fördröjningar eller fel som kan påverka projektet.

> [!IMPORTANT]
> Åtgärden hämta uppgift räknar är för närvarande i förhandsvisning och finns inte ännu i Azure Government, Azure Kina och Azure Tyskland. 
>
>

## <a name="how-tasks-are-counted"></a>Hur åtgärder ska räknas

Åtgärden hämta uppgift räknar antal aktiviteter efter tillstånd, enligt följande:

- En uppgift räknas som **active** när den är i kö och kan köras, men inte är tilldelad till en beräkningsnod. En uppgift också räknas som **active** om det är beroende av en överordnad aktivitet som inte har slutförts ännu. Mer information om aktiviteten beroenden finns [Skapa uppgift beroenden för att köra uppgifter som är beroende av andra aktiviteter](batch-task-dependencies.md). 
- En uppgift räknas som **kör** när den har tilldelats en beräkningsnod, men har inte slutförts ännu. En uppgift räknas som **kör** när dess tillstånd är antingen `preparing` eller `running`, som anges av den [hämta information om en aktivitet] [ rest_get_task] igen.
- En uppgift räknas som **slutförts** när den inte längre kan köras. En uppgift räknas som **slutförts** har vanligtvis antingen har slutförts, har slutförts utan fel eller också har uttömt gränsen för återförsök. 

Åtgärden hämta uppgift räknar rapporterar också hur många aktiviteter har har lyckats eller misslyckats. Batch avgör om en aktivitet har lyckats eller misslyckats genom att kontrollera den **resultatet** -egenskapen för egenskapen [executionInfo] [https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo]:

    - En uppgift räknas som **lyckades** om resultatet av körning av aktiviteten är `success`.
    - En uppgift räknas som **misslyckades** om resultatet av körning av aktiviteten är `failure`.

Mer information om uppgiften tillstånd finns [hämta information om en aktivitet][rest_get_task].

Följande kodexempel för .NET visar hur du hämtar aktivitet antal per tillstånd: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> Du kan använda ett liknande mönster för REST och andra språk som stöds för att få fram aktivitet för ett jobb. 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>Konsekvenskontrollen för aktiviteten antal

Batch-tjänsten aggregerar antal aktiviteten genom att samla in data från flera delar av en asynkron distribuerade system. Om du vill säkerställa att aktiviteten är rätt Batch tillhandahåller ytterligare verifiering för antal tillstånd genom att utföra konsekvenskontroll kontrollerar mot flera komponenter i systemet. Batch utför dessa konsekvenskontroller så länge det finns färre än 200 000 uppgifter i jobbet. Det osannolika att konsekvenskontrollen hittar fel korrigeras Batch resultatet av åtgärden hämta räknar aktiviteter baserat på resultatet av en konsekvenskontroll. Konsekvenskontroll är en extra mått så att kunder som förlitar sig på åtgärden hämta uppgift räknar får rätt information de behöver för lösningen.

Den **validationStatus** i svaret anger om Batch har utfört en konsekvenskontroll. Om gruppen inte har möjlighet att kontrollera tillstånd räknas av mot de faktiska tillstånd lagras i systemet, sedan **validationStatus** egenskap är inställd på `unvalidated`. Av prestandaskäl Batch kommer inte att utföra en konsekvenskontroll om jobbet innehåller fler än 200 000 uppgifter, så den **validationStatus** egenskapen kan anges till `unvalidated` i det här fallet. Antal uppgifter är dock inte nödvändigtvis fel i det här fallet som högst osannolikt även en mycket begränsad dataförlust. 

När status ändras i en aktivitet bearbetar aggregering pipeline ändringen inom några sekunder. Åtgärden hämta uppgift räknar visar uppdaterade uppgiften antal inom denna period. Dock om aggregering pipeline missar en ändring i en aktivitet tillstånd, registreras sedan ändringen inte förrän nästa validering pass. Antal aktiviteten kan vara något fel på grund av saknade händelsen under denna tid, men de korrigerade på nästa validering pass.

## <a name="best-practices-for-counting-a-jobs-tasks"></a>Metodtips för inventering uppgifter för ett jobb

Anropar åtgärden hämta uppgift räknar är det mest effektiva sättet att returnera en grundläggande antalet aktiviteter i ett jobb med tillstånd. Om du använder Batch-tjänstversion 2017-06-01.5.1, rekommenderar vi skrivning eller uppdatera din kod för att använda hämta uppgift räknar.

Hämta uppgift räknar-åtgärden är inte tillgänglig i Batch-tjänsten-versioner tidigare än 2017-06-01.5.1. Om du använder en äldre version av tjänsten använder du en fråga för att räkna antalet aktiviteter i ett projekt i stället. Mer information finns i [skapa frågor för att visa en lista med Batch-resurser effektivt](batch-efficient-list-queries.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om begrepp och funktioner relaterade till Batch-tjänsten finns i [funktionsöversikten för Batch](batch-api-basics.md). Artikeln beskriver de primära Batch resurserna, till exempel pooler, compute-noder, jobb och uppgifter och ger en översikt över tjänstens funktioner.
* Lär dig hur du utvecklar ett enkelt Batch-aktiverat program med hjälp av [Batch .NET-klientbiblioteket](batch-dotnet-get-started.md) eller [Python](batch-python-tutorial.md). De här inledande artiklarna leder dig igenom ett fungerande program som använder Batch-tjänsten för att köra en arbetsbelastning på flera compute-noder.


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
