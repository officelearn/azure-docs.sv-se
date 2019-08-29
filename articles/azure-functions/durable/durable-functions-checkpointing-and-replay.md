---
title: Kontroll punkter och uppspelning i Durable Functions – Azure
description: Lär dig hur kontroll punkter och svar fungerar i Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 79cb276f121c351a9954994038d9d826819edf5d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087457"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Kontroll punkter och uppspelning i Durable Functions (Azure Functions)

Ett av de viktigaste attributen för Durable Functions är **tillförlitlig körning**. Funktionerna i Orchestrator-funktioner och-aktiviteter kan köras på olika virtuella datorer i ett Data Center, och de virtuella datorerna eller den underliggande nätverks infrastrukturen är inte 100% Reliable.

Trots detta säkerställer Durable Functions tillförlitlig körning av dirigering. Det gör det genom att använda lagrings köer för att köra funktions anrop och genom att regelbundet kontrol lera körnings historiken i lagrings tabeller (med ett design mönster för molnet som kallas [händelse källa](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Historiken kan sedan upprepas för att automatiskt återskapa InMemory-statusen för en Orchestrator-funktion.

## <a name="orchestration-history"></a>Orchestration-historik

Anta att du har följande Orchestrator-funktion:

### <a name="c"></a>C#

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

I varje `await` (C#)- `yield` eller (JavaScript)-instruktion, kontroll ramverket för det varaktiga aktivitets ramverket körnings status för funktionen i Table Storage. Det här är det tillstånd som kallas för *Orchestration*-historiken.

## <a name="history-table"></a>Historik tabell

I allmänhet är det ständiga aktivitets ramverket följande vid varje kontroll punkt:

1. Sparar körnings historik i Azure Storage tabeller.
2. Köa meddelanden för funktioner som Orchestrator vill anropa.
3. Köa meddelanden för själva &mdash; Orchestrator till exempel meddelanden med varaktig timer.

När kontroll punkten har slutförts är Orchestrator-funktionen kostnads fri att tas bort från minnet tills det finns mer arbete att göra.

> [!NOTE]
> Azure Storage tillhandahåller inte några transaktions garantier mellan att spara data i tabell lagring och köer. För att hantera fel använder Durable Functions lagringsprovider de *slutliga konsekvens* mönstren. Dessa mönster ser till att inga data går förlorade om anslutningen bryts eller bryts i mitten av en kontroll punkt.

Vid slut för ande ser historiken för funktionen som visas tidigare ut ungefär så här i Azure Table Storage (förkortat för illustration):

| PartitionKey (InstanceId)                     | Händelsetyp             | Timestamp               | Indata | Name             | Resultat                                                    | State |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852 Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Slutfört           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Några anmärkningar om kolumn värden:

* **PartitionKey**: Innehåller dirigeringens instans-ID.
* **EventType**: Representerar händelsens typ. Kan vara en av följande typer:
  * **OrchestrationStarted**: Orchestrator-funktionen återupptogs från en await eller körs för första gången. Kolumnen används för att fylla på det deterministiska värdet för CurrentUtcDateTime-API: et. [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) `Timestamp`
  * **ExecutionStarted**: Orchestrator-funktionen startade körning för första gången. Den här händelsen innehåller också funktions ingången `Input` i kolumnen.
  * **TaskScheduled**: En aktivitets funktion har schemalagts. Namnet på aktivitets funktionen samlas in i `Name` kolumnen.
  * **TaskCompleted**: En aktivitets funktion har slutförts. Resultatet av funktionen finns i `Result` kolumnen.
  * **TimerCreated**: En varaktig timer skapades. `FireAt` Kolumnen innehåller den schemalagda UTC-tid då timern upphör att gälla.
  * **TimerFired**: En varaktig timer har utlösts.
  * **Händelse aktive rad**: En extern händelse skickades till Orchestration-instansen. Kolumnen samlar in namnet på händelsen `Input` och kolumnen fångar in händelsens nytto Last. `Name`
  * **OrchestratorCompleted**: Orchestrator-funktionen förväntades.
  * **ContinueAsNew**: Orchestrator-funktionen har slutförts och startats om automatiskt med det nya läget. `Result` Kolumnen innehåller värdet, som används som indatamängden i den omstartade instansen.
  * **ExecutionCompleted**: Orchestrator-funktionen kördes (eller misslyckades). Utdata från funktionen eller fel informationen lagras i `Result` kolumnen.
* **Tidsstämpel**: UTC-tidsstämpeln för historik händelsen.
* **Namn på**: Namnet på den funktion som anropades.
* Inmatade: Den JSON-formaterade indatamängden för funktionen.
* **Resultat**: Resultatet av funktionen. det vill säga dess retur värde.

> [!WARNING]
> Även om det är användbart som ett fel söknings verktyg ska du inte göra något beroende av den här tabellen. Den kan ändras när Durable Functions tillägget utvecklas.

Varje gång funktionen återupptas från en `await` (C#) eller `yield` (Java Script), kör det ständiga aktivitets ramverket om Orchestrator-funktionen från grunden. Vid varje körnings försök kontaktas körnings historiken för att avgöra om den aktuella asynkrona åtgärden har ägt rum.  Om åtgärden utfördes, spelar ramverket om resultatet av åtgärden direkt och fortsätter med nästa `await` (C#) eller `yield` (Java Script). Den här processen fortsätter tills hela historiken har spelats upp, där alla lokala variabler i Orchestrator-funktionen återställs till sina tidigare värden.

## <a name="orchestrator-code-constraints"></a>Begränsningar för Orchestrator-kod

Uppspelnings beteendet skapar begränsningar för den typ av kod som kan skrivas i en Orchestrator-funktion:

* Orchestrator-koden måste vara **deterministisk**. Den kommer att spelas upp flera gånger och måste ge samma resultat varje gång. Till exempel, inga direkta anrop för att hämta aktuellt datum/tid, Hämta slumpmässiga nummer, generera slumpmässiga GUID eller anropa till Fjärrslutpunkter.

  Om Orchestrator-koden behöver hämta aktuellt datum/tid bör den använda [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.net) eller `currentUtcDateTime` (Java Script) API, som är säkert för uppspelning.

  Om Orchestrator-koden behöver generera ett slumpmässigt GUID bör den använda [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.net)-API: et, som är säkert för repetition eller delegera GUID-generering till en aktivitets funktion (Java Script), som i det här exemplet:

  ```javascript
  const uuid = require("uuid/v1");

  module.exports = async function(context) {
    return uuid();
  }
  ```

  Icke-deterministiska åtgärder måste utföras i aktivitets funktioner. Detta omfattar alla interaktioner med andra indata-eller utdata-bindningar. Detta säkerställer att alla icke-deterministiska värden genereras en gång vid den första körningen och sparas i körnings historiken. Efterföljande körningar kommer sedan att använda det sparade värdet automatiskt.

* Orchestrator-koden ska vara **icke-blockerande**. Det innebär exempelvis inga I/O och inga anrop till `Thread.Sleep` (.net) eller motsvarande API: er.

  Om en Orchestrator behöver fördröjning kan den använda API: et för [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) (.net) `createTimer` eller (Java Script).

* Orchestrator-kod får **aldrig initiera en asynkron åtgärd** förutom att använda API: et `context.df` för [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) eller Object. Till `Task.Run`exempel Nej `HttpClient.SendAsync` `setInterval()` eller i .net, eller `setTimeout()` och `Task.Delay` i Java Script. Det tåliga aktivitets ramverket Kör Orchestrator-kod på en enskild tråd och kan inte samverka med andra trådar som kan schemaläggas av andra asynkrona API: er. Detta inträffar om detta `InvalidOperationException` inträffar, genereras ett undantag.

* **Oändliga slingor bör undvikas** i Orchestrator-kod. Eftersom den varaktiga aktivitets ramverket sparar körnings historiken när Orchestration-funktionen fortskrider, kan en oändlig loop orsaka att en Orchestrator-instans tar slut på minne. För oändliga upprepnings scenarier använder du API: er som [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.net `continueAsNew` ) eller (Java Script) för att starta om funktionen och ta bort tidigare körnings historik.

* JavaScript Orchestrator-funktioner kan `async`inte vara. De måste deklareras som synkrona Generator funktioner.

Dessa begränsningar kan verka avskräckande i första, i praktiken att de inte är svåra att följa. Det beständiga aktivitets ramverket försöker identifiera överträdelser av ovanstående regler och returnerar en `NonDeterministicOrchestrationException`. Detta identifierings beteende är dock bästa och du bör inte vara beroende av den.

> [!NOTE]
> Alla dessa regler gäller endast för funktioner som utlöses av `orchestrationTrigger` bindningen. Aktivitets funktioner som utlöses av `activityTrigger` bindningen och funktioner som `orchestrationClient` använder bindningen har inga sådana begränsningar.

## <a name="durable-tasks-net"></a>Varaktiga uppgifter (.NET)

> [!NOTE]
> I det här avsnittet beskrivs interna implementerings Detaljer för det varaktiga aktivitets ramverket. Du kan använda Durable Functions utan att känna till den här informationen. Den är endast avsedd för att hjälpa dig att förstå repetitions beteendet.

Aktiviteter som kan förväntas på ett säkert sätt i Orchestrator-funktioner kallas ibland för *varaktiga uppgifter*. Detta är uppgifter som skapas och hanteras av det varaktiga aktivitets ramverket. Exempel är de uppgifter som returneras `CallActivityAsync`av `WaitForExternalEvent`, och `CreateTimer`.

Dessa *varaktiga uppgifter* hanteras internt med hjälp av en lista `TaskCompletionSource` med objekt. Under uppspelningen skapas de här aktiviteterna som en del av Orchestrator Code Execution och slutförs eftersom Dispatchern räknar upp motsvarande historik händelser. Detta görs synkront med en enda tråd tills all historik har spelats upp. Eventuella varaktiga uppgifter som inte slutförs i slutet av historik uppspelningen har lämpliga åtgärder som utförs. Ett meddelande kan till exempel vara placerat i kö för att anropa en aktivitets funktion.

Det körnings beteende som beskrivs här bör hjälpa dig att förstå varför Orchestrator- `await` funktions koden aldrig får innehålla en icke-beständig uppgift: dispatcher-tråden kan inte vänta på att den ska slutföras och återanrop från den aktiviteten kan eventuellt skada spårningen status för Orchestrator-funktionen. Vissa körnings kontroller är på plats för att förhindra detta.

Om du vill ha mer information om hur det varaktiga aktivitets ramverket Kör Orchestrator-funktioner, är det bästa sättet att se den [varaktiga aktivitets käll koden på GitHub](https://github.com/Azure/durabletask). I synnerhet, se [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) och [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om instans hantering](durable-functions-instance-management.md)
