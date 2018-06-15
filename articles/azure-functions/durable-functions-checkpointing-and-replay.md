---
title: Kontrollpunkter och replay i varaktiga funktioner – Azure
description: Lär dig hur kontrollpunkter och svar fungerar i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 39cdb9b2c6eae9a3176aedc64b8d187e298fdfdd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764578"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Kontrollpunkter och replay i varaktiga funktioner (Azure-funktioner)

En av nyckelattribut varaktiga funktioner är **tillförlitlig körning**. Orchestrator-funktioner och funktioner för aktiviteten kan köras på olika virtuella datorer i ett datacenter, och dessa virtuella datorer eller underliggande nätverksinfrastrukturen är inte 100% tillförlitlig.

Trots detta garanterar varaktiga funktioner tillförlitlig körning av orkestreringarna. Detta sker med hjälp av lagringsköer till enheten funktionsanrop och regelbundet kontrollpunkter körningstiden till storage-tabeller (med hjälp av en molndesignmönstret kallas [händelse källa](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Ändringar sedan spelas upp för att automatiskt återskapa minnestillståndet för en orchestrator-funktion.

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

På varje `await` (C#) eller `yield` (JavaScript)-instruktionen, beständiga aktiviteten Framework kontrollpunkter körningstillstånd för funktionen i tabellagring. Det här tillståndet är vad kallas den *orchestration historik*.

## <a name="history-table"></a>Historiktabellen

Generellt sett gör beständiga aktiviteten Framework följande på varje kontrollpunkt:

1. Sparar körningstiden i Azure Storage-tabeller.
2. Enqueues meddelanden för funktioner i orchestrator vill anropa.
3. Enqueues meddelanden för orchestrator själva &mdash; exempelvis varaktiga timer meddelanden.

Orchestrator-funktionen är gratis ska tas bort från minnet tills det finns mer arbete att göra när kontrollpunkten har slutförts.

> [!NOTE]
> Azure Storage tillhandahåller inte någon transaktionella garantier mellan sparar data till tabellagring och köer. Om du vill hantera fel lagringsprovidern varaktiga funktioner använder *slutliga konsekvensen* mönster. Dessa mönster se till att ingen data går förlorade om det finns en krasch eller anslutningsproblem mitt i en kontrollpunkt.

När åtgärden har slutförts ser något som liknar följande i Azure Table Storage (förkortat för illustration) i historiken för funktionen som visades tidigare:

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
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | ”[” ”Hello Tokyo”! ”,” ”Hello Seattle”! ”,” ”Hello London”! ”]” | Slutförd           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Några anmärkningar på kolumnvärdena:
* **PartitionKey**: innehåller instans-ID för orchestration.
* **EventType**: representerar typen av händelse. Kan vara något av följande typer:
    * **OrchestrationStarted**: funktionen orchestrator återupptas från ett await eller körs för första gången. Den `Timestamp` kolumnen används för att fylla deterministiska värdet för den [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API.
    * **ExecutionStarted**: funktionen orchestrator startats för första gången. Den här händelsen innehåller även funktionen indata i den `Input` kolumn.
    * **TaskScheduled**: funktionen för en aktivitet har schemalagts. Namnet på funktionen aktiviteten hämtas i den `Name` kolumn.
    * **TaskCompleted**: en aktivitet funktionen slutfördes. Resultatet av funktionen är i den `Result` kolumn.
    * **TimerCreated**: en beständig timer har skapats. Den `FireAt` kolumnen innehåller schemalagda UTC-tid då timern upphör att gälla.
    * **TimerFired**: utlöses av en beständig timer.
    * **Händelse aktiverad**: en extern händelse skickades till orchestration-instans. Den `Name` kolumnen samlar in namnet på händelsen och `Input` kolumnen fångar nyttolasten för händelsen.
    * **OrchestratorCompleted**: funktionen orchestrator inväntas.
    * **ContinueAsNew**: funktionen orchestrator slutförts och startas om sig själv med nya tillstånd. Den `Result` kolumn innehåller det värde som används som indata i instansen startas om.
    * **ExecutionCompleted**: funktionen orchestrator kördes till slutförande (eller misslyckades). Utdata för funktionen eller felinformationen lagras i den `Result` kolumn.
* **Tidsstämpel**: I UTC-tidsstämpel för den tidigare händelsen.
* **Namnet**: namnet på den funktion som anropades.
* **Inkommande**: JSON-formaterade indata för funktionen.
* **Resultatet**: resultatet av funktionen; som är dess returvärde.

> [!WARNING]
> Det är praktiskt som felsökningsverktyg, inte ta eventuella beroenden i den här tabellen. Den kan ändras när tillägget varaktiga funktioner utvecklas.

Varje gång funktionen återställs från en `await`, ramverket beständiga aktiviteten kör funktionen orchestrator från början. Placera på varje kör den tittar körningstiden för att avgöra om den aktuella async-åtgärden har vidtagit.  Om åtgärden ägde rum, ramen spelar upp resultatet av åtgärden omedelbart och flyttar till nästa `await`. Den här processen fortsätter tills hela historiken har tagits spelas, då alla lokala variabler i orchestrator-funktionen återställs till sina föregående värden.

## <a name="orchestrator-code-constraints"></a>Begränsningar för orchestrator-kod

Beteendet replay skapar begränsningar på vilken typ av kod som kan skrivas i ett orchestrator-funktion:

* Orchestrator-koden måste vara **deterministisk**. Den ska återupprepas flera gånger, och du måste ge samma resultat varje gång. Till exempel anropar ingen direkt hämta aktuellt datum och tid, hämta slumptal, generera en slumpmässig GUID eller fjärr-slutpunkter som anropar.

  Om orchestrator måste du hämta aktuellt datum och tid, ska det använda den [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API, vilket är säkra för repetitionsattacker.

  Icke-deterministiska åtgärder måste göras i aktiviteten funktioner. Detta inkluderar alla interaktion med andra inkommande eller utgående bindningar. Detta säkerställer att alla icke-deterministiska värden genereras en gång på den första körningen och sparas i historiken för körning. Efterföljande körningar använder sedan det sparade värdet automatiskt.

* Orchestrator-kod ska vara **inte blockerar**. Till exempel som innebär att ingen i/o och inga anrop till `Thread.Sleep` eller motsvarande API: er.

  Om en orchestrator måste fördröjning, den kan använda den [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) API.

* Orchestrator-koden måste **aldrig initiera en asynkron åtgärd** förutom med hjälp av den [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API. Till exempel ingen `Task.Run`, `Task.Delay` eller `HttpClient.SendAsync`. Beständiga aktiviteten Framework kör orchestrator-kod på en enkel tråd och samverka inte med andra trådar kan schemaläggas med andra asynkrona API: er.

* **Oändlig slingor bör undvikas** i orchestrator-kod. Eftersom beständiga aktiviteten Framework sparar körningstiden som orchestration funktionen fortlöper, orsaka en oändlig loop en orchestrator-instans till slut på minne. Oändlig loop scenarier använder API: er som [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) att starta om funktionen körning och ta bort den tidigare körningen historik.

När dessa villkor kan verka avskräckande på först i praktiken de inte är svåra att följa. Beständiga aktiviteten Framework försöker identifiera överträdelser av reglerna ovan och genererar en `NonDeterministicOrchestrationException`. Det här beteendet för identifiering är bästa dock och du bör inte beroende av den.

> [!NOTE]
> Alla dessa regler gäller endast för funktioner som utlösts av den `orchestrationTrigger` bindning. Funktioner för aktiviteten utlöses av den `activityTrigger` bindnings- och funktioner som använder den `orchestrationClient` bindning, har inga sådana begränsningar.

## <a name="durable-tasks"></a>Beständiga aktiviteter

> [!NOTE]
> Det här avsnittet beskrivs interna implementeringsinformation om beständiga aktiviteten ramen. Du kan använda varaktiga utan att känna till informationen. Den är avsedd endast för att hjälpa dig förstå beteendet repetitionsattacker.

Uppgifter som på ett säkert sätt kan inväntas i orchestrator-funktioner ibland kallas *beständiga aktiviteter*. Detta är aktiviteter som skapas och hanteras av beständiga aktiviteten ramen. Exempel är de uppgifter som returneras av `CallActivityAsync`, `WaitForExternalEvent`, och `CreateTimer`.

Dessa *beständiga aktiviteter* hanteras internt med hjälp av en lista över `TaskCompletionSource` objekt. Dessa uppgifter skapas som en del av orchestrator kodkörning under replay och slutförs som dispatcher räknar motsvarande tidigare händelser. Detta görs alla synkront med en enskild tråd tills alla tidigare har varit spelas. Alla beständiga uppgifter som inte är slutförda i slutet av historik replay har lämpliga åtgärder utförs. Till exempel kanske ett meddelande i kö för att anropa en funktion för aktiviteten.

Körningsbeteende som beskrivs här hjälper dig att förstå varför orchestrator Funktionskoden måste aldrig `await` en icke-beständiga uppgift: dispatcher-tråden inte kan vänta på att den är klar och eventuella återanrop av aktiviteten potentiellt kan skada spårning status för orchestrator-funktionen. Vissa runtime-kontrollerna är på plats att förhindra detta.

Om du vill ha mer information om hur ramverket beständiga aktiviteten kör orchestrator-funktioner, bästa du behöver göra är att kontakta den [beständiga aktiviteten källkoden på GitHub](https://github.com/Azure/durabletask). I synnerhet finns [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) och [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om instanshantering av](durable-functions-instance-management.md)
