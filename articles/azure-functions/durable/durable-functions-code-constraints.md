---
title: Tåliga Orchestrator Code-begränsningar – Azure Functions
description: Orchestration-funktionen repetitions-och kod begränsningar för Azure Durable Functions.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935773"
---
# <a name="orchestrator-function-code-constraints"></a>Begränsningar för Orchestrator-funktions kod

Durable Functions är en utökning av [Azure Functions](../functions-overview.md) som gör att du kan bygga tillstånds känsliga program. Du kan använda en [Orchestrator-funktion](durable-functions-orchestrations.md) för att dirigera körningen av andra varaktiga funktioner i en Function-app. Orchestrator-funktioner är tillstånds känsliga, pålitliga och potentiellt långvariga.

## <a name="orchestrator-code-constraints"></a>Begränsningar för Orchestrator-kod

Orchestrator-funktioner använder [händelse källor](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) för att säkerställa tillförlitlig körning och upprätthålla lokal variabel status. [Repetitions beteendet](durable-functions-orchestrations.md#reliability) i Orchestrator Code skapar begränsningar för den typ av kod som du kan skriva i en Orchestrator-funktion. Till exempel måste Orchestrator-kod vara *deterministisk*.  Orchestrator-funktioner kommer att spelas upp flera gånger och det måste ge samma resultat varje gång.

Följande avsnitt innehåller några enkla rikt linjer för att se till att koden är deterministisk.

### <a name="using-deterministic-apis"></a>Använda deterministiska API: er

Orchestrator-funktioner är kostnads fria att anropa alla API: er som de vill ha på mål språket. Det är dock viktigt att Orchestrator-funktioner endast anropar *deterministiska* API: er. Ett *deterministiskt API* är en API som alltid returnerar exakt samma värde med samma ineffekt, oavsett när eller hur ofta det anropas.

Följande är några exempel på API: er som bör undvikas eftersom de *inte* är deterministiska. Dessa begränsningar gäller endast för Orchestrator-funktioner. Andra funktions typer har inte sådana begränsningar.

| API-kategori | Reason | Lösning: |
| ------------ | ------ | ---------- |
| Datum/tider  | API: er som returnerar _Aktuellt_ datum eller tid är icke-deterministiska eftersom de returnerade värdet är olika för varje omuppspelning. | Använd [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.net) eller `currentUtcDateTime` (Java Script) API, som är säkert för uppspelning. |
| GUID/UUID  | API: er som returnerar ett _slumpmässigt_ GUID/UUID är icke-deterministiska eftersom det genererade värdet kommer att vara olika för varje omuppspelning. | Använd [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.net) eller `newGuid` (Java Script) för att på ett säkert sätt generera slumpmässiga GUID. |
| Slumpmässiga siffror | API: er som returnerar slumpmässiga tal är icke-deterministiska eftersom det genererade värdet kommer att vara olika för varje omuppspelning. | Använd en aktivitets funktion för att returnera slumptal till ett Orchestration-tal. De returnerade värdena för aktivitets funktioner är alltid säkra för uppspelning. |
| Bindningar | Bindningar för indata och utdata är vanligt vis I/O och är icke-deterministiska. Även om [Dirigerings klienten](durable-functions-bindings.md#orchestration-client) och [enhets klient](durable-functions-bindings.md#entity-client) bindningarna inte får användas direkt av en Orchestrator-funktion. | Använda indata och utgående bindningar i klient-eller aktivitets funktioner. |
| Nätverk | Nätverks anrop omfattar externa system och är icke-deterministiska. | Använd aktivitets funktioner för att göra nätverks anrop. Om du behöver göra ett HTTP-anrop från din Orchestrator-funktion, kan du också välja att använda de [tåliga http API: erna](durable-functions-http-features.md#consuming-http-apis). |
| Blockera API: er | Blockering av API: `Thread.Sleep` er som (.net) eller andra liknande API: er kan orsaka prestanda-och skalnings problem för Orchestrator-funktioner och bör undvikas. I Azure Functions förbruknings planen kan de även leda till onödiga körnings tids avgifter. | Använd alternativ för att blockera API: er när de `CreateTimer` är tillgängliga, till exempel för att införa fördröjningar i Orchestration-körningen. Antalet [varaktiga timer](durable-functions-timers.md) -fördröjningar räknas inte till körnings tiden för en Orchestrator-funktion. |
| Asynkrona API: er | Orchestrator-kod får **aldrig initiera en asynkron åtgärd** förutom att använda API: et `context.df` för [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) eller Object. Till `Task.Run`exempel Nej `HttpClient.SendAsync` `setInterval()` eller i .net, eller `setTimeout()` och `Task.Delay` i Java Script. Det tåliga aktivitets ramverket Kör Orchestrator-kod på en enskild tråd och kan inte samverka med andra trådar som kan schemaläggas av andra asynkrona API: er. | Endast *varaktiga* asynkrona anrop bör göras av en Orchestrator-funktion. Alla andra asynkrona API-anrop bör göras från aktivitets funktioner. |
| Asynkrona JavaScript-funktioner | JavaScript Orchestrator-funktioner kan `async` inte användas eftersom Node. js-körningsmiljön inte garanterar att async-funktioner är deterministiska. | JavaScript Orchestrator-funktioner måste deklareras som synkrona Generator funktioner. |
| Tråd kopplings-API: er | Det tåliga aktivitets ramverket Kör Orchestrator-kod på en enskild tråd och kan inte samverka med andra trådar. Att introducera nya trådar i en Dirigerings körning kan resultera i icke-deterministisk körning eller död lägen. | Tråd-API: er ska nästan aldrig användas i Orchestrator-funktioner. Om de behövs bör de begränsas till aktivitets funktioner. |
| Statiska variabler | Icke-konstanta statiska variabler bör undvikas i Orchestrator-funktioner eftersom deras värden kan ändras över tid, vilket resulterar i icke-deterministiska körnings beteenden. | Använd konstanter eller begränsa användningen av statiska variabler till aktivitets funktioner. |
| Miljövariabler | Använd inte miljövariabler i Orchestrator-funktioner. Deras värden kan ändras över tid, vilket resulterar i icke-deterministiska körnings beteenden. | Miljövariabler får endast refereras från i klient funktioner eller aktivitets funktioner. |
| Oändliga slingor | Oändliga slingor bör undvikas i Orchestrator-funktioner. Det varaktiga aktivitets ramverket sparar körnings historiken eftersom Orchestration-funktionen går vidare, så en oändlig loop kan orsaka att en Orchestrator-instans tar slut på minne. | För oändliga upprepnings scenarier använder du API: er som [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.net `continueAsNew` ) eller (Java Script) för att starta om funktionen och ta bort tidigare körnings historik. |

Dessa begränsningar kan verka avskräckande i första, i praktiken att de inte är svåra att följa. Det beständiga aktivitets ramverket försöker identifiera överträdelser av ovanstående regler och returnerar en `NonDeterministicOrchestrationException`. Detta identifierings beteende är dock bästa och du bör inte vara beroende av den.

## <a name="versioning"></a>Versionshantering

En varaktig dirigering kan köras kontinuerligt i dagar, månader, år eller till och med [Eternally](durable-functions-eternal-orchestrations.md). Alla kod uppdateringar som görs i Durable Functions-program som påverkar icke-slutförda dirigeringar kan bryta uppspelnings beteendet. Det är därför viktigt att planera noggrant när du gör uppdateringar av kod. En mer detaljerad beskrivning av hur du använder version av koden finns i artikeln [versions hantering](durable-functions-versioning.md) .

## <a name="durable-tasks"></a>Varaktiga uppgifter

> [!NOTE]
> I det här avsnittet beskrivs interna implementerings Detaljer för det varaktiga aktivitets ramverket. Du kan använda Durable Functions utan att känna till den här informationen. Den är endast avsedd för att hjälpa dig att förstå repetitions beteendet.

Aktiviteter som kan förväntas på ett säkert sätt i Orchestrator-funktioner kallas ibland för *varaktiga uppgifter*. De här uppgifterna skapas och hanteras av det varaktiga aktivitets ramverket. Exempel är de uppgifter som returneras `CallActivityAsync`av `WaitForExternalEvent`, och `CreateTimer` i .net Orchestrator-funktioner.

Dessa *varaktiga uppgifter* hanteras internt med hjälp av en lista `TaskCompletionSource` med objekt i .net. Under uppspelningen skapas de här aktiviteterna som en del av Orchestrator Code Execution och slutförs eftersom Dispatchern räknar upp motsvarande historik händelser. Körningen görs synkront med en enda tråd tills all historik har spelats upp. Eventuella varaktiga aktiviteter som inte slutförs i slutet av historiken har genomgått lämpliga åtgärder. Ett meddelande kan till exempel vara placerat i kö för att anropa en aktivitets funktion.

Det körnings beteende som beskrivs här bör hjälpa dig att förstå varför Orchestrator- `await` funktions `yield` koden får aldrig eller en icke-beständig uppgift: dispatcher-tråden kan inte vänta tills den har slutförts och alla återanrop från den aktiviteten kan eventuellt skadat spårnings status för Orchestrator-funktionen. Vissa körnings kontroller är på plats för att försöka identifiera dessa överträdelser.

Om du vill ha mer information om hur det varaktiga aktivitets ramverket Kör Orchestrator-funktioner, är det bästa sättet att se den [varaktiga aktivitets käll koden på GitHub](https://github.com/Azure/durabletask). I synnerhet, se [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) och [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs om hur du anropar under-Orchestration](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar versions hantering](durable-functions-versioning.md)
