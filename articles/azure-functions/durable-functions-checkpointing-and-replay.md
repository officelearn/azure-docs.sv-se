---
title: Kontrollpunkter och återuppspelning i varaktiga funktioner – Azure
description: Lär dig hur kontrollpunkter och svar fungerar i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 90860759e8a20bca03d3eb74e4859d0b26d14da1
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091874"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Kontrollpunkter och återuppspelning i varaktiga funktioner (Azure Functions)

En av viktiga punkter för varaktiga funktioner är **arbetsflödesprocesserna**. Orchestrator-funktioner och Aktivitetsfunktioner kan köras på olika virtuella datorer inom ett datacenter och de virtuella datorerna eller den underliggande nätverksinfrastrukturen är inte 100% pålitlig.

Trots detta säkerställer varaktiga funktioner arbetsflödesprocesserna orkestreringar. Detta sker med hjälp av lagringsköer till enheten funktionsanrop och regelbundet kontrollpunkter körningstiden till storage-tabeller (med en designmönster för molnet som kallas [händelsekällor](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Ändringar sedan spelas upp för att automatiskt återskapa InMemory-tillståndet för en orchestrator-funktion.

## <a name="orchestration-history"></a>Orchestration-historik

Anta att du har följande orchestrator-funktion:

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const output = [];
    output.push(yield context.df.callActivityAsync("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivityAsync("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivityAsync("E1_SayHello", "London"));

    return output;
});
```

På varje `await` (C#) eller `yield` (JavaScript)-instruktionen, hållbar uppgift Framework kontrollpunkter funktionen i tabellagring körningstillstånd. Det här tillståndet är vad kallas den *orchestration historik*.

## <a name="history-table"></a>Historiktabellen

Generellt sett gör ramen varaktiga uppgiften följande på varje kontrollpunkt:

1. Sparar körningshistorik i Azure Storage-tabeller.
2. Placerar det i kö meddelanden för funktioner i orchestrator vill anropa.
3. Placerar det i kö meddelanden för dirigering du själva &mdash; exempelvis varaktiga timer meddelanden.

När kontrollpunkten har slutförts, kostar orchestrator-funktion som ska tas bort från minnet tills det är mer arbete för att det ska göra.

> [!NOTE]
> Azure Storage ger inte alla transaktionella garantier mellan sparar data i table storage och köer. Om du vill hantera fel lagringsprovidern varaktiga funktioner använder *eventuell konsekvens* mönster. Dessa mönster se till att ingen data går förlorade om det finns en krasch eller förlust av anslutning mitt i en kontrollpunkt.

När åtgärden har slutförts ut ungefär så här i Azure Table Storage (förkortat för tydlighetens skull) i historiken för funktionen som visades tidigare:

| PartitionKey (InstanceId)                     | Händelsetyp             | Tidsstämpel               | Indata | Namn             | Resultat                                                    | Status | 
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|---------------------| 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     | 
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | Null  | E1_HelloSequence |                                                           |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | ”” ”Hello Tokyo”! ””                                        |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | ”” ”Hello Seattle”! ””                                      |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | ”” ”Hello London”! ””                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | ”[” ”Hello Tokyo”! ”,” ”Hello Seattle”! ”,” ”Hello London”! ”]” | Slutfört           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Några anmärkningar om kolumnvärdena:
* **PartitionKey**: innehåller instans-ID för dirigering.
* **Händelsetyp**: representerar typ av händelsen. Kan vara något av följande typer:
    * **OrchestrationStarted**: orchestrator-funktion återupptas från ett await eller körs för första gången. Den `Timestamp` kolumnen används för att fylla i deterministisk värdet för den [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API.
    * **ExecutionStarted**: orchestrator-funktion startats för första gången. Den här händelsen innehåller även funktionen indata i den `Input` kolumn.
    * **TaskScheduled**: en funktion för aktiviteten har schemalagts. Namnet på funktionen aktivitet har tagits emot under de `Name` kolumn.
    * **TaskCompleted**: en funktion för aktiviteten som slutförd. Resultatet av funktionen är i den `Result` kolumn.
    * **TimerCreated**: en hållbar timer har skapats. Den `FireAt` kolumnen innehåller den schemalagda UTC-tiden då timern upphör att gälla.
    * **TimerFired**: utlöstes av en beständig timer.
    * **EventRaised**: en extern händelse har skickats till orchestration-instans. Den `Name` kolumnen fångar händelsens namn och `Input` kolumnen fångar nyttolasten för händelsen.
    * **OrchestratorCompleted**: orchestrator-funktion som har slutförts.
    * **ContinueAsNew**: orchestrator-funktion har slutförts och startas om sig själv med nya statusen. Den `Result` kolumnen innehåller värdet som används som indata i den startats om-instansen.
    * **ExecutionCompleted**: orchestrator-funktion körde slutförandet (eller misslyckade). Utdata för funktionen eller felinformationen lagras i den `Result` kolumn.
* **Tidsstämpel**: The UTC-tidsstämpel för historikhändelsen.
* **Namn på**: namnet på den funktion som anropas.
* **Inkommande**: JSON-formaterade indata för funktionen.
* **Resultatet**: resultatet av funktionen, det vill säga dess returvärde.

> [!WARNING]
> Det är användbart som felsökningsverktyg, inte ta alla beroenden på den här tabellen. Det kan ändras när tillägget varaktiga funktioner utvecklas.

Varje gång som funktionen återgår från en `await`, hållbar uppgift ramverket produktmiljö orchestrator-funktion från grunden. Placera på varje kör den konsultationer körningshistorik för att avgöra om den aktuella async-åtgärden har tagit.  Om åtgärden ägde rum, ramverket spelar upp resultatet av åtgärden omedelbart och går vidare till nästa `await`. Den här processen fortsätter tills hela historiken har varit återupprepas, då alla lokala variabler i orchestrator-funktion återställs till sina föregående värden.

## <a name="orchestrator-code-constraints"></a>Begränsningar för orchestrator-kod

Beteendet repetitionsattacker skapar begränsningar på vilken typ av kod som kan skrivas i en orchestrator-funktion:

* Orchestrator-koden måste vara **deterministisk**. Den ska återupprepas flera gånger, och du måste ge samma resultat varje gång. Till exempel ingen direct-anrop till hämta aktuellt datum och tid, hämta slumptal, generera slumpmässig GUID eller anropa remote slutpunkter.

  Om orchestrator-koden måste hämta aktuellt datum och tid, bör den använda den [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API, som är säker för repetitionsattacker.

  Icke-deterministisk åtgärder måste göras i Aktivitetsfunktioner. Detta inkluderar en interaktion med andra inkommande eller utgående bindningar. Detta säkerställer att alla icke-deterministisk värden genereras en gång på den första körningen och sparas i historiken för körning. Efterföljande körningar använder sedan det sparade värdet automatiskt.

* Orchestrator-kod ska vara **icke-blockerande**. Till exempel det innebär att inga i/o och inga anrop till `Thread.Sleep` eller motsvarande API: er.

  Om en initierare måste fördröjning, den kan använda den [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) API.

* Orchestrator-kod måste **initieras aldrig asynkrona åtgärder** förutom med hjälp av den [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API. Till exempel Nej `Task.Run`, `Task.Delay` eller `HttpClient.SendAsync`. Ramverket varaktiga uppgift kör orchestrator-kod på en enda tråd och samverka inte med andra trådar kan schemaläggas med andra async API: er.

* **Oändlig slingor bör undvikas** i orchestrator-kod. Eftersom varaktiga uppgift ramverket sparar körningshistorik som utvecklas för orchestration-funktionen, kan en oändlig loop orsaka en orchestrator-instans som ska få slut på minne. Oändlig loop scenarier kan du använda API: er som [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) att starta om körning funktion och ta bort tidigare körningshistorik.

Även om dessa begränsningar kan verka skrämmande på först i praktiken som de inte är svårt att följa. Hållbar uppgift ramverket försöker identifiera brott mot av ovanstående och genererar en `NonDeterministicOrchestrationException`. Men detta identifiering är mån, och du bör inte lita på den.

> [!NOTE]
> Alla dessa regler gäller endast för funktioner som utlöses av den `orchestrationTrigger` bindning. Aktivitetsfunktioner utlöses av den `activityTrigger` bindnings- och funktioner som använder den `orchestrationClient` bindning, har inga sådana begränsningar.

## <a name="durable-tasks"></a>Beständiga uppgifter

> [!NOTE]
> Det här avsnittet beskrivs interna implementeringsdetaljer för ramen varaktiga uppgift. Du kan använda varaktiga funktioner utan att känna till den här informationen. Det är endast avsedd att hjälpa dig att förstå beteendet repetitionsattacker.

Uppgifter som kan vara på ett säkert sätt slutförts i orchestrator-funktioner ibland kallas *varaktiga uppgifter*. Detta är aktiviteter som skapas och hanteras av ramverket varaktiga uppgift. Exempel är de uppgifter som returneras av `CallActivityAsync`, `WaitForExternalEvent`, och `CreateTimer`.

Dessa *varaktiga uppgifter* hanteras internt med hjälp av en lista över `TaskCompletionSource` objekt. Under repetitionsattacker, dessa uppgifter skapas som en del av orchestrator kodkörning och slutförs som avsändaren räknar upp motsvarande historik-händelser. Detta görs alla synkront med en enda tråd tills all historik har varit återupprepas. Alla hållbara uppgifter som inte är slutförda i slutet av historik repetitionsattacker har åtgärder utförs. Till exempel kanske ett meddelande i kö för att anropa en funktion för aktiviteten.

Körningsbeteende som beskrivs här hjälper dig att förstå varför orchestrator function-koden måste aldrig `await` en icke-varaktiga uppgift: dispatcher-tråden inte vill vänta tills den är klar och alla återanrop av aktiviteten kan potentiellt skadat spårnings tillståndet för orchestrator-funktion. Vissa runtime-kontroller är uppfyllda att förhindra detta.

Om du vill ha mer information om hur ramen varaktiga uppgift kör orchestrator-funktioner, det bästa du ska göra är att läsa den [varaktiga uppgift källkod på GitHub](https://github.com/Azure/durabletask). I synnerhet Se [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) och [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om instanshantering](durable-functions-instance-management.md)
