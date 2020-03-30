---
title: Metodtips för Azure-funktioner
description: Lär dig metodtips och mönster för Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a41a5828a82d81c5e7e8749fee70cd15e17bb9d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277783"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimera säkerheten och tillförlitligheten för Azure Functions

Den här artikeln innehåller vägledning för att förbättra prestanda och tillförlitlighet för dina [serverlösa](https://azure.microsoft.com/solutions/serverless/) funktionsappar.  

## <a name="general-best-practices"></a>Allmän bästa praxis

Följande är metodtips för hur du skapar och skapar dina serverlösa lösningar med Hjälp av Azure Functions.

### <a name="avoid-long-running-functions"></a>Undvik tidskrävande funktioner

Stora, långvariga funktioner kan orsaka oväntade timeout-problem. Mer information om tidsgränsen för en viss värdplan finns i [tidsgränsen för funktionsappen](functions-scale.md#timeout). 

En funktion kan bli stor på grund av många Node.js-beroenden. Importerande beroenden kan också orsaka ökade inläsningstider som resulterar i oväntade tidsgränser. Beroenden läses in både uttryckligen och implicit. En enda modul som läses in av koden kan läsa in sina egna ytterligare moduler. 

När det är möjligt, återfaktare stora funktioner i mindre funktionsuppsättningar som fungerar tillsammans och returnera svar snabbt. En webhook- eller HTTP-utlösarfunktion kan till exempel kräva ett bekräftelsesvar inom en viss tid. det är vanligt att webhooks kräver ett omedelbart svar. Du kan skicka http-utlösarens nyttolast till en kö som ska bearbetas av en köutlösarefunktion. Med den här metoden kan du skjuta upp det faktiska arbetet och returnera ett omedelbart svar.


### <a name="cross-function-communication"></a>Kommunikation mellan funktioner

[Varaktiga funktioner](durable/durable-functions-overview.md) och [Azure Logic Apps](../logic-apps/logic-apps-overview.md) är byggda för att hantera tillståndsövergångar och kommunikation mellan flera funktioner.

Om du inte använder varaktiga funktioner eller logikappar för att integrera med flera funktioner är det bäst att använda lagringsköer för kommunikation mellan funktioner. Den främsta orsaken är att lagringsköer är billigare och mycket enklare att etablera än andra lagringsalternativ. 

Enskilda meddelanden i en lagringskö är begränsade till 64 kB. Om du behöver skicka större meddelanden mellan funktioner kan en Azure Service Bus-kö användas för att stödja meddelandestorlekar på upp till 256 kB på standardnivån och upp till 1 MB på Premium-nivån.

Service Bus-ämnen är användbara om du behöver meddelandefiltrering före bearbetning.

Händelsehubbar är användbara för att stödja kommunikation med stora volymer.


### <a name="write-functions-to-be-stateless"></a>Skriv funktioner som ska vara tillståndslösa 

Funktioner bör vara statslösa och idempotenta om möjligt. Associera all nödvändig tillståndsinformation med dina data. En order som bearbetas skulle till `state` exempel troligen ha en associerad medlem. En funktion kan bearbeta en order baserat på det tillståndet medan själva funktionen förblir tillståndslös. 

Idempotenta funktioner rekommenderas särskilt med timerutlösare. Om du till exempel har något som absolut måste köras en gång om dagen, skriv det så att det kan köras när som helst under dagen med samma resultat. Funktionen kan avslutas när det inte finns något arbete för en viss dag. Även om en tidigare körning misslyckades att slutföra, bör nästa körning fortsätta där den slutade.


### <a name="write-defensive-functions"></a>Skriv defensiva funktioner

Anta att din funktion kan stöta på ett undantag när som helst. Utforma dina funktioner med möjlighet att fortsätta från en tidigare felpunkt under nästa körning. Tänk dig ett scenario som kräver följande åtgärder:

1. Fråga efter 10 000 rader i en databas.
2. Skapa ett kömeddelande för var och en av dessa rader för att bearbeta längre ner på raden.
 
Beroende på hur komplext ditt system är kan du ha: involverar nedströmstjänster som beter sig dåligt, nätverksavbrott eller kvotgränser som uppnåtts osv. Alla dessa kan påverka din funktion när som helst. Du måste utforma dina funktioner för att vara förberedd för det.

Hur reagerar koden om ett fel inträffar efter att 5 000 av dessa objekt har infogats i en kö för bearbetning? Spåra objekt i en uppsättning som du har slutfört. Annars kan du infoga dem igen nästa gång. Denna dubbel-insättning kan ha en allvarlig inverkan på ditt arbetsflöde, så [gör dina funktioner idempotent](functions-idempotent.md). 

Om ett köobjekt redan har bearbetats kan du låta funktionen vara en no-op.

Dra nytta av defensiva åtgärder som redan finns för komponenter som du använder i Azure Functions-plattformen. Se till exempel **Hantera giftkömeddelanden** i dokumentationen för [Azure Storage Queue-utlösare och bindningar](functions-bindings-storage-queue-trigger.md#poison-messages). 

## <a name="scalability-best-practices"></a>Metodtips för skalbarhet

Det finns ett antal faktorer som påverkar hur instanser av din funktionsapp skalas. Detaljerna finns i dokumentationen för [funktionsskalning](functions-scale.md).  Följande är några metodtips för att säkerställa optimal skalbarhet för en funktionsapp.

### <a name="share-and-manage-connections"></a>Dela och hantera anslutningar

Återanvända anslutningar till externa resurser när det är möjligt. Se [hur du hanterar anslutningar i Azure Functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Undvik att dela lagringskonton

När du skapar en funktionsapp måste du associera den med ett lagringskonto. Lagringskontoanslutningen underhålls i [programinställningen AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Blanda inte test- och produktionskod i samma funktionsapp

Funktioner i en funktionsapp delar resurser. Minnet delas till exempel. Om du använder en funktionsapp i produktion ska du inte lägga till testrelaterade funktioner och resurser i den. Det kan orsaka oväntade omkostnader under körning av produktionskod.

Var försiktig med vad du läser in i dina produktionsfunktionsappar. Minnet är i genomsnitt för varje funktion i appen.

Om du har en delad sammansättning refererad i flera .NET-funktioner placerar du den i en gemensam delad mapp. Annars kan du av misstag distribuera flera versioner av samma binära som beter sig annorlunda mellan funktioner.

Använd inte utförlig loggning i produktionskod, vilket har en negativ prestandapåverkan.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Använd async-kod men undvik att blockera samtal

Asynkron programmering är en rekommenderad bästa praxis, särskilt när blockering av I/O-åtgärder är inblandade.

Undvik alltid att referera till egenskapen `Result` eller `Wait` anropa `Task` metoden för en instans i C#. Detta tillvägagångssätt kan leda till trådutmattning.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Använda flera arbetsprocesser

Som standard använder alla värdinstanser för Funktioner en enda arbetsprocess. För att förbättra prestanda, särskilt med runtimes med enkel tråd som Python, använder [du FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) för att öka antalet arbetsprocesser per värd (upp till 10). Azure Functions försöker sedan distribuera samtidiga funktionsarop mellan dessa arbetare. 

Den FUNCTIONS_WORKER_PROCESS_COUNT gäller för varje värd som Functions skapar när du skalar ut ditt program för att möta efterfrågan. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Ta emot meddelanden i batch när det är möjligt

Vissa utlösare som Event Hub gör det möjligt att ta emot en grupp meddelanden på en enda anrop.  Batching meddelanden har mycket bättre prestanda.  Du kan konfigurera max batchstorleken `host.json` i filen enligt beskrivningen i [referensdokumentationen host.json](functions-host-json.md)

För C#-funktioner kan du ändra typen till en starkt maskinskriven matris.  I stället `EventData sensorEvent` för metodsignaturen kan till exempel användas `EventData[] sensorEvent`.  För andra språk måste du uttryckligen ange kardinalitetsegenskapen i din `function.json` till `many` för att aktivera batchbearbetning som visas [här](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurera värdbeteenden för att bättre hantera samtidighet

Filen `host.json` i funktionsappen möjliggör konfiguration av värdkörnings- och utlösarbeteenden.  Förutom batchfunktioner kan du hantera samtidighet för ett antal utlösare. Ofta justera värdena i dessa alternativ kan hjälpa varje instans skala på lämpligt sätt för kraven i de anropade funktionerna.

Inställningarna i filen host.json gäller för alla funktioner i appen, inom en *enda instans* av funktionen. Om du till exempel hade en funktionsapp med två HTTP-funktioner och [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) begäranden 25, skulle en begäran till antingen HTTP-utlösaren räknas in i de delade 25 samtidiga begärandena.  När funktionsappen skalas till 10 instanser tillåter de två funktionerna effektivt 250 samtidiga begäranden (10 instanser * 25 samtidiga begäranden per instans). 

Andra konfigurationsalternativ för värd finns i [konfigurationsartikeln host.json](functions-host-json.md).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Hantera anslutningar i Azure Functions](manage-connections.md)
* [Metodtips för Azure App-tjänsten](../app-service/app-service-best-practices.md)
