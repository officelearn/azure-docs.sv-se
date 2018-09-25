---
title: Metodtips för Azure Functions | Microsoft Docs
description: Lär dig metodtips och mönster för Azure Functions.
services: functions
documentationcenter: na
author: wesmc7777
manager: jeconnoc
keywords: Azure funktioner, mönster, bästa praxis, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dbb21e63a25d6e357287f2f91181f2326850a898
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952859"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimera prestanda och tillförlitlighet i Azure Functions

Den här artikeln innehåller vägledning för att förbättra säkerheten och tillförlitligheten för dina [serverlös](https://azure.microsoft.com/overview/serverless-computing/) funktionsappar. 

## <a name="general-best-practices"></a>Allmänna Metodtips

Här följer bästa praxis i hur du skapar och skapa dina serverlösa lösningar med Azure Functions.

### <a name="avoid-long-running-functions"></a>Undvika tidskrävande funktioner

Stora, långvariga funktioner kan orsaka oväntade timeout-problem. En funktion kan bli stora på grund av många Node.js-beroenden. Importera beroenden kan också medföra ökad belastning gånger som resultera i oväntade tidsgränser. Beroenden läses både specifikt och implicit. En enda modul som lästs in av din kod kan läsa in sin egen ytterligare moduler.  

När det är möjligt, omstrukturera stora funktionerna i mindre funktionen anger som fungerar tillsammans och returnera svar snabbt. Till exempel kan en webhook- eller HTTP-Utlösarfunktion kräva en bekräftelse svar inom en viss tid gräns. Det är vanligt att webhooks kräver omedelbara åtgärder. Du kan skicka HTTP-utlösaren nyttolasten i en kö som ska bearbetas av en funktion för kö-utlösare. Den här metoden kan du skjuta upp det faktiska arbetet och returnera ett direkt svar.


### <a name="cross-function-communication"></a>Mellan funktionen kommunikation

[Varaktiga funktioner](durable-functions-overview.md) och [Azure Logic Apps](../logic-apps/logic-apps-overview.md) har skapats för att hantera tillståndsövergångar och kommunikation mellan flera funktioner.

Om inte använder varaktiga funktioner eller Logic Apps för att integrera med flera funktioner, men det är vanligtvis en bra idé att använda storage-köer för mellan funktionen kommunikation.  Det främsta skälet är storage-köer är billigare och enklare att etablera. 

Enskilda meddelanden i en lagringskö är begränsade i storlek till 64 KB. Om du vill skicka större meddelanden mellan functions, en Azure Service Bus-kö som kan användas för att stödja meddelande storlekar upp till 256 KB på Standard-nivån och upp till 1 MB på Premium-nivån.

Service Bus-ämnen är användbara om du behöver meddelande filtrering innan bearbetning.

Händelsehubbar är användbara för att stödja kommunikation med hög volym.


### <a name="write-functions-to-be-stateless"></a>Skriva funktioner för att vara tillståndslösa 

Funktioner vara tillståndslösa och idempotenta om möjligt. Associera statusinformation som krävs med dina data. Till exempel en beställning bearbetas skulle förmodligen ha en associerad `state` medlem. En funktion kan bearbeta en ordning baserat på det aktuella tillståndet, medan själva funktionen är tillståndslösa. 

Idempotent funktioner rekommenderas särskilt med timerutlösare. Till exempel om du har något som är absolut måste köras en gång om dagen, skriva så att den kan köras som helst under dagen med samma resultat. Funktionen kan avsluta när det finns inget arbete för en viss dag. Även om ett tidigare kör kunde inte slutföra bör nästa körning ta vid där den slutade.


### <a name="write-defensive-functions"></a>Skriva skydden funktioner

Anta att din funktion kan stöta på ett undantag när som helst. Utforma dina funktioner med möjlighet att fortsätta från en tidigare tidpunkt misslyckas under nästa körning. Tänk dig ett scenario som kräver följande åtgärder:

1. Fråga för 10 000 rader i en databas.
2. Skapa ett kömeddelande för var och en av de rader som ska bearbeta ytterligare ned.
 
Beroende på hur komplex din system som du kan ha: ingår nedströms tjänster fungerar felaktigt, networking avbrott eller kvot begränsar nått, etc. Alla dessa kan påverka din funktion när som helst. Du måste du utforma dina funktioner förberedas för den.

Hur din kod reagera om ett fel inträffar när du har infogat 5 000 av dessa objekt i en kö för bearbetning? Spåra objekt i en uppsättning som du har slutfört. Annars kan kan du infoga dem igen nästa gång. Detta kan ha en betydande del av ditt arbetsflöde. 

Tillåt att funktionen ska vara en icke-alternativ om ett objekt redan har bearbetats.

Dra nytta av försvarsåtgärder som redan ges för komponenter som du använder i Azure Functions-plattformen. Se exempelvis **hantering av skadliga Kömeddelanden** i dokumentationen för [Azure Storage-kö-utlösare och bindningar](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Metodtips för skalbarhet

Det finns ett antal faktorer som påverkar hur instanser av din funktionsapp skala. Information finns i dokumentationen för [funktionen skalning](functions-scale.md).  Här följer några rekommendationer för att säkerställa optimal skalbarheten i en funktionsapp.

### <a name="share-and-manage-connections"></a>Dela och hantera anslutningar

Återanvända anslutningar till externa resurser när det är möjligt.  Se [hur du hanterar anslutningar i Azure Functions](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Blanda inte koden för testning och produktion i samma funktionsapp

Funktioner i en funktionsapp delar resurser. Till exempel delas minne. Om du använder en funktionsapp i produktion kan du inte lägga till test-relaterade funktioner och resurser till den. Det kan orsaka oväntade kostnader under kodkörning för produktion.

Var noga med vad du läser in i dina funktionsappar i produktion. Minne är ett genomsnitt över varje funktion i appen.

Om du har en delad sammansättning som refereras i flera .net-funktioner kan du placera den i en vanlig delad mapp. Om du använder C#-skript (.csx) referera till sammansättningen med en instruktion som liknar följande exempel: 

    #r "..\Shared\MyAssembly.dll". 

Annars är det enkelt att av misstag distribuerar flera testversioner av de binära som fungerar annorlunda mot mellan funktioner.

Använd inte utförlig loggning i produktionskoden. Den har sämre prestanda.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Använda async-kod men undvika blockerar anrop

Asynkron programmering är en rekommenderad metod. Dock alltid undvika refererar till den `Result` egenskap eller anropa `Wait` metod på en `Task` instans. Den här metoden kan leda till tråd överbelastning.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Ta emot meddelanden i batch när det är möjligt

Vissa utlösare som Event Hub kan ta emot en grupp med meddelanden på ett enda anrop.  Batchbearbetning meddelanden har mycket bättre prestanda.  Du kan konfigurera max batchstorlek i den `host.json` filen som beskrivs i den [host.json-referensdokumentation](functions-host-json.md)

Du kan ändra typen till en starkt typbestämd för C#-funktioner.  Till exempel i stället för `EventData sensorEvent` Metodsignaturen kan vara `EventData[] sensorEvent`.  För övriga språk måste du uttryckligen ställa in egenskapen cardinality hos din `function.json` till `many` för att aktivera Batchbearbetning [som visas här](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurera värd beteenden för att bättre hantera samtidighet

Den `host.json` -fil i funktionsappen tillåter konfiguration av värd körning och utlösare beteenden.  Du kan hantera samtidighet för ett antal utlösare förutom batchbearbetning beteenden.  Ofta justera värdena i de här alternativen kan varje instans skala på lämpligt sätt för kraven från de anropade funktionerna.

Inställningar i hosts-filen gäller över alla funktioner i appen, i en *instans* för funktionen. Till exempel om du hade en funktionsapp med 2 HTTP-funktioner och 25 samtidiga begäranden, skulle en begäran om att antingen HTTP-utlösare filterantalet mot delade 25 samtidiga begäranden.  Om den funktionsappen skalas till 10 instanser, skulle funktionerna 2 kan 250 samtidiga begäranden (10 instanser * 25 samtidiga begäranden per instans).

**HTTP-samtidighet värdalternativ**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Andra värden konfigurationsalternativ finns [i konfigurationsdokumentet värden](functions-host-json.md).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Hur du hanterar anslutningar i Azure Functions](manage-connections.md)
* [Metodtips för Azure App Service](../app-service/app-service-best-practices.md)
