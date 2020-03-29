---
title: Varaktiga orchestrator-kodbegränsningar - Azure-funktioner
description: Uppspelning av orchestration-funktion och kodbegränsningar för Azure Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562147"
---
# <a name="orchestrator-function-code-constraints"></a>Funktionskodvillkor för Orchestrator

Varaktiga funktioner är ett tillägg av [Azure Functions](../functions-overview.md) som gör att du kan skapa tillståndskänsliga appar. Du kan använda en [orchestrator-funktion](durable-functions-orchestrations.md) för att dirigera körningen av andra varaktiga funktioner i en funktionsapp. Orchestrator-funktioner är tillståndskänsliga, tillförlitliga och potentiellt långvariga.

## <a name="orchestrator-code-constraints"></a>Begränsningar för orkestrerarkod

Orchestrator-funktioner använder [händelsekällor](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) för att säkerställa tillförlitlig körning och för att upprätthålla lokalt variabeltillstånd. [Orkesterkods uppspelningsbeteende](durable-functions-orchestrations.md#reliability) skapar begränsningar för vilken typ av kod som du kan skriva i en orchestrator-funktion. Orchestrator-funktioner måste till exempel vara *deterministiska*: en orchestrator-funktion spelas upp flera gånger och måste ge samma resultat varje gång.

### <a name="using-deterministic-apis"></a>Använda deterministiska API:er

Det här avsnittet innehåller några enkla riktlinjer som säkerställer att koden är deterministisk.

Orchestrator-funktioner kan anropa vilket API som helst på sina målspråk. Det är dock viktigt att orchestrator funktioner kräver endast deterministiska API: er. Ett *deterministiskt API* är ett API som alltid returnerar samma värde som ges samma indata, oavsett när eller hur ofta det anropas.

I följande tabell visas exempel på API:er som du bör undvika eftersom de *inte* är deterministiska. Dessa begränsningar gäller endast orchestrator-funktioner. Andra funktionstyper har inte sådana begränsningar.

| API-kategori | Orsak | Lösning |
| ------------ | ------ | ---------- |
| Datum och tider  | API:er som returnerar aktuellt datum eller aktuell tid är inte deterministiska eftersom det returnerade värdet är olika för varje repris. | Använd`CurrentUtcDateTime` API:et i `currentUtcDateTime` .NET eller API:et i JavaScript, som är säkra för uppspelning. |
| GUID och UUIDs  | API:er som returnerar ett slumpmässigt GUID eller UUID är inte deterministiska eftersom det genererade värdet är olika för varje repris. | Använd `NewGuid` i .NET eller `newGuid` JavaScript för att på ett säkert sätt generera slumpmässiga GUID:er. |
| Slumpmässiga tal | API:er som returnerar slumptal är inte deterministiska eftersom det genererade värdet är olika för varje repris. | Använd en aktivitetsfunktion för att returnera slumptal till en orkestrering. Returvärdena för aktivitetsfunktioner är alltid säkra för repris. |
| Bindningar | Indata- och utdatabindningar gör vanligtvis I/O och är inte deterministiska. En orchestrator-funktion får inte direkt använda ens [orchestration-klienten](durable-functions-bindings.md#orchestration-client) och klientbindningarna för [entitet.](durable-functions-bindings.md#entity-client) | Använd in- och utdatabindningar i klient- eller aktivitetsfunktioner. |
| Nätverk | Nätverkssamtal involverar externa system och är icke-deterministiska. | Använd aktivitetsfunktioner för att ringa nätverkssamtal. Om du behöver ringa ett HTTP-anrop från orchestrator-funktionen kan du också använda de [varaktiga HTTP-API:erna](durable-functions-http-features.md#consuming-http-apis). |
| Blockera API:er | Blockera API:er som `Thread.Sleep` i .NET och liknande API:er kan orsaka prestanda- och skalningsproblem för orchestrator-funktioner och bör undvikas. I Azure Functions Consumption-planen kan de till och med resultera i onödiga körningsavgifter. | Använd alternativ för att blockera API:er när de är tillgängliga. Använd `CreateTimer` till exempel för att införa fördröjningar i orkestreringskörning. [Varaktiga](durable-functions-timers.md) timerfördröjningar räknas inte in i körningstiden för en orchestrator-funktion. |
| Async API:er | Orchestrator-kod får aldrig starta någon async-åtgärd förutom genom att använda `IDurableOrchestrationContext` API:et `context.df` eller objektets API. Du kan till exempel `Task.Run`inte `Task.Delay`använda `HttpClient.SendAsync` , och `setTimeout` `setInterval` i .NET eller i JavaScript. Durable Task Framework kör orchestrator-kod på en enda tråd. Det kan inte interagera med andra trådar som kan anropas av andra async API:er. | En orchestrator-funktion bör endast göra varaktiga asynkronanrop. Aktivitetsfunktioner bör göra andra async API-anrop. |
| Async JavaScript-funktioner | Du kan inte deklarera JavaScript `async` orchestrator-funktioner som eftersom nod.js-körningen inte garanterar att asynkrona funktioner är deterministiska. | Deklarera JavaScript orchestrator-funktioner som synkrona generatorfunktioner. |
| Api:er för trådtråd | Durable Task Framework kör orchestrator-kod på en enda tråd och kan inte interagera med andra trådar. Om du introducerar nya trådar i en orkestrerings körning kan det leda till icke-deterministisk körning eller dödlägen. | Orchestrator-funktioner bör nästan aldrig använda tråd-API:er. Undvik till exempel i .NET att använda `ConfigureAwait(continueOnCapturedContext: false)`; Detta säkerställer att aktivitetsfortsättningar körs på `SynchronizationContext`orchestrator-funktionens ursprungliga . Om sådana API:er är nödvändiga begränsar du deras användning till endast aktivitetsfunktioner. |
| Statiska variabler | Undvik att använda icke-innehållsstyrda statiska variabler i orchestrator-funktioner eftersom deras värden kan ändras med tiden, vilket resulterar i icke-deterministiskt körningsbeteende. | Använd konstanter eller begränsa användningen av statiska variabler till aktivitetsfunktioner. |
| Miljövariabler | Använd inte miljövariabler i orchestrator-funktioner. Deras värden kan ändras med tiden, vilket resulterar i icke-deterministiskt körningsbeteende. | Miljövariabler får endast refereras inifrån klientfunktioner eller aktivitetsfunktioner. |
| Oändliga slingor | Undvik oändliga loopar i orchestrator funktioner. Eftersom varaktigt aktivitetsramverk sparar körningshistorik allteftersom orkestreringsfunktionen fortskrider, kan en oändlig loop orsaka att en orchestrator-instans får på minne. | För oändliga loopscenarier `ContinueAsNew` använder du `continueAsNew` API:er som i .NET eller i JavaScript för att starta om funktionskörningen och ignorera tidigare körningshistorik. |

Även om tillämpningen av dessa begränsningar kan tyckas svårt i början, i praktiken de är lätta att följa.

Durable Task Framework försöker identifiera överträdelser av föregående regler. Om den hittar en överträdelse, kastar ramen en **NonDeterministicOrchestrationException** undantag. Det här identifieringsbeteendet fångar dock inte alla överträdelser och du bör inte vara beroende av det.

## <a name="versioning"></a>Versionshantering

En hållbar orkestrering kan köras kontinuerligt i dagar, månader, år, eller [ens evigt](durable-functions-eternal-orchestrations.md). Alla koduppdateringar som görs till varaktiga funktioner-appar som påverkar oavslutade orkestreringar kan bryta orkestreringarnas uppspelningsbeteende. Det är därför det är viktigt att planera noggrant när du gör uppdateringar av koden. En mer detaljerad beskrivning av hur du version av koden finns i [versionsartikeln](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Varaktiga uppgifter

> [!NOTE]
> I det här avsnittet beskrivs information om intern implementering av ramverket för varaktiga uppgifter. Du kan använda varaktiga funktioner utan att känna till den här informationen. Den är endast avsedd att hjälpa dig att förstå reprisbeteendet.

Uppgifter som säkert kan vänta i orchestrator-funktioner kallas ibland *varaktiga uppgifter*. Det varaktiga aktivitetsramverket skapar och hanterar dessa uppgifter. Exempel är de uppgifter som returneras av **callactivityAsync,** **WaitForExternalEvent**och **CreateTimer** i .NET orchestrator-funktioner.

Dessa varaktiga uppgifter hanteras internt `TaskCompletionSource` av en lista över objekt i .NET. Under uppspelning skapas dessa uppgifter som en del av orchestrator-kodkörning. De är färdiga när avsändaren räknar upp motsvarande historikhändelser.

Aktiviteterna utförs synkront med hjälp av en enda tråd tills all historik har spelats upp igen. Varaktiga uppgifter som inte är klara i slutet av historikreprisning har lämpliga åtgärder utförda. Ett meddelande kan till exempel vara enqueued att anropa en aktivitetsfunktion.

Det här avsnittets beskrivning av körningsbeteende bör hjälpa dig att `await` `yield` förstå varför en orchestrator-funktion inte kan använda eller i en icke-varaktig aktivitet. Det finns två orsaker: avsändaren tråden kan inte vänta på att aktiviteten ska slutföras, och alla motringning av den uppgiften kan potentiellt skada spårningstillståndet för orchestrator-funktionen. Vissa körningskontroller finns på plats för att identifiera dessa överträdelser.

Mer information om hur det varaktiga aktivitetsramverket kör orchestrator-funktioner läser du [källkoden för varaktig aktivitet på GitHub](https://github.com/Azure/durabletask). Se särskilt [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) och [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du anropar underorkestreringar](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Läs om hur du hanterar versionshantering](durable-functions-versioning.md)
