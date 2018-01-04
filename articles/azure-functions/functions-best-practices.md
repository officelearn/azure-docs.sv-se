---
title: "Metodtips för Azure Functions | Microsoft Docs"
description: "Läs metodtips och mönster för Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, mönster, bästa praxis, funktioner, händelsebearbetning, webhooks, dynamiska beräkning, serverlösa arkitektur"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3ef75a2a157190b24c171309c4d5c39596b5045
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimera prestanda och tillförlitlighet i Azure Functions

Den här artikeln innehåller anvisningar för att förbättra prestanda och tillförlitlighet för din [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) fungerar appar. 

## <a name="general-best-practices"></a>Allmänna Metodtips

Nedan följer bästa praxis i hur du skapar och skapa din serverlösa lösningar med hjälp av Azure Functions.

### <a name="avoid-long-running-functions"></a>Undvika långvariga funktioner

Stora, långvariga funktioner kan orsaka oväntade timeout problem. En funktion kan bli stora på grund av många Node.js-beroenden. Importera beroenden kan också medföra ökad belastning gånger som resultera i oväntade timeout. Beroenden laddas både uttryckligen och implicit. En enda modul som lästs in av din kod kan läsa in en egen ytterligare moduler.  

När det är möjligt, flytta stora funktionerna i mindre funktionen anger som fungerar tillsammans och returnera svar snabbt. Till exempel kan en webhook eller HTTP-Utlösarfunktion kräva ett bekräftelse-svar inom en viss tid; Det är vanligt att webhooks kräver ett omedelbart svar. Du kan skicka HTTP-utlösaren nyttolast i en kö för att kunna bearbetas av en kö Utlösarfunktion. Den här metoden kan du skjuta upp det faktiska arbetet och returnera ett omedelbart svar.


### <a name="cross-function-communication"></a>Funktionen kommunikation mellan

[Beständiga funktioner](durable-functions-overview.md) och [Azure Logikappar](../logic-apps/logic-apps-what-are-logic-apps.md) har byggts för att hantera tillståndsövergångar och kommunikation mellan flera funktioner.

Om du inte använder beständiga funktioner eller Logic Apps kan integreras med flera funktioner, är det vanligtvis en bra idé att använda lagringsköer för mellan funktionen kommunikation.  Det främsta skälet är lagringsköer är billigare och enklare att etablera. 

Enskilda meddelanden i en kö för lagring är begränsade i storlek till 64 KB. Om du behöver skicka större meddelanden mellan funktioner, ett Azure Service Bus-kö kan användas för att stödja meddelande storlek upp till 256 KB på standardnivån, och upp till 1 MB i Premium-nivån.

Service Bus-ämnen är användbara om du behöver meddelande filtrering innan bearbetning.

Händelsehubbar är användbara för att stödja kommunikation med hög volym.


### <a name="write-functions-to-be-stateless"></a>Skriva funktioner ska tillståndslös 

Funktioner som ska vara tillståndslösa och idempotent om möjligt. Koppla statusinformation som krävs till dina data. Till exempel en order bearbetas skulle förmodligen ha en associerad `state` medlem. En funktion kan bearbeta en ordning som baseras på det aktuella tillståndet eftersom själva funktionen förblir tillståndslösa. 

Idempotent funktioner är särskilt användbart med timer-utlösare. Till exempel om du har något som verkligen måste köras en gång om dagen, skriva den så den kan köras när under dagen med samma resultat. Funktionen kan avsluta när det finns inget arbete för en viss dag. Även om en tidigare körning kunde inte slutföra bör nästa körning hämta där den avbröts.


### <a name="write-defensive-functions"></a>Skriva defensiva funktioner

Anta att din funktion kan uppstå ett undantag när som helst. Utforma dina funktioner med möjligheten att fortsätta från en tidigare punkt misslyckas vid nästa körning. Överväg ett scenario som kräver följande åtgärder:

1. Frågan för 10 000 rader i en databas.
2. Skapa ett kömeddelande för var och en av de rader som ska bearbetas ytterligare ned.
 
Beroende på hur komplexa datorn är kanske: ingår nedströms tjänster fungerar felaktigt, nätverk avbrott eller kvot begränsar nått, etc. Alla dessa kan påverka funktionen när som helst. Måste du utformar dina funktioner att vara förberedd för den.

Hur koden reagera om ett fel uppstår när du har infogat 5 000 av dessa objekt i en kö för bearbetning? Spåra objekt i en samling som du har slutfört. Annars kan kan du lägga till dem igen nästa gång. Detta kan ha en betydande del av ditt arbetsflöde. 

Tillåt att funktionen ska vara en no-op om ett köobjekt redan hade bearbetats.

Dra nytta av försvarsåtgärder som redan ges för komponenter som du använder i Azure Functions-plattformen. Se exempelvis **hantering av skadligt Kömeddelanden** i dokumentationen för [Azure Storage-utlösare och bindningar](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Metodtips för skalbarhet

Det finns ett antal faktorer som påverkar hur instanser av appen funktionen skala. Information finns i dokumentationen för [funktionen skalning](functions-scale.md).  Här följer några rekommendationer för att säkerställa optimala skalbarheten för en funktionsapp.

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Blanda inte test- och koden i appen med samma funktion

Funktioner i en funktionsapp dela resurser. Till exempel delas minne. Om du använder en funktionsapp i produktion, Lägg inte till test-relaterade funktioner och resurser till den. Det kan orsaka oväntade kostnader under kodkörning för produktion.

Var noga med att du laddar i dina appar för produktion-funktionen. Minne beräknas för varje funktion i appen.

Om du har en delad sammansättning som refereras i flera .net-funktioner kan du placera den i en gemensam delad mapp. Referera till en sammansättning med en instruktion som liknar följande exempel om med hjälp av C# skript (.csx): 

    #r "..\Shared\MyAssembly.dll". 

Annars är det enkelt att av misstag distribuerar flera testversioner av samma binära som uppför sig annorlunda mellan funktioner.

Använd inte utförlig loggning i kod. Den har en negativ inverkan på prestanda.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Använda async-kod men Undvik blockerar anrop

Asynkron programmering är en rekommenderad metod. Dock alltid undvika refererar till den `Result` egenskap eller anropa `Wait` metod i en `Task` instans. Den här metoden kan leda till uttömning tråd.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Ta emot meddelanden i batch när det är möjligt

Vissa utlösare som Event Hub aktivera tar emot en grupp med meddelanden på ett enda anrop.  Batchbearbetning meddelanden har mycket bättre prestanda.  Du kan konfigurera högsta batch-storlek i den `functions.json` filen enligt anvisningarna i den [host.json referensdokumentationen](functions-host-json.md)

Du kan ändra typen till en matris med strikt typkontroll för C#-funktioner.  Till exempel i stället för `EventData sensorEvent` Metodsignaturen kunde `EventData[] sensorEvent`.  För andra språk måste du uttryckligen ställa in egenskapen cardinality din `function.json` till `many` för att aktivera Batchbearbetning [som visas här](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurera värd beteenden för att hantera bättre samtidighet

Den `host.json` filen i funktionen appen kan för värden runtime och utlösare beteenden.  Du kan hantera samtidighet för ett antal utlösare förutom batchbearbetning beteenden.  Ofta Justera värden i de här alternativen kan hjälpa varje instans skala passar behoven hos de anropade funktionerna.

Inställningar i värdfilen gäller över alla funktioner i appen, inom en *instans* för funktionen. Om du har en funktionsapp med 2 HTTP-funktioner och 25 samtidiga förfrågningar, exempelvis räknas en begäran om att antingen HTTP-utlösaren mot delade 25 samtidiga begäranden.  Om appen funktionen skalas till 10 instanser, 2 funktioner effektivt skulle låta 250 samtidiga begäranden (10 instanser * 25 samtidiga förfrågningar per instans).

**Alternativ för HTTP samtidighet värden**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Andra värden konfigurationsalternativ finns [i värden configuration dokumentet](functions-host-json.md).

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

Eftersom Azure Functions använder Azure App Service, bör du också vara medveten om Apptjänst riktlinjer.
* [Patterns and Practices HTTP prestandaoptimering](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)
