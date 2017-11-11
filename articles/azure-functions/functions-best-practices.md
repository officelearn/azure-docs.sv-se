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
ms.openlocfilehash: d59ef16de433ac9691f6996eab2bf56f056feb88
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimera prestanda och tillförlitlighet i Azure Functions

Den här artikeln innehåller anvisningar för att förbättra prestanda och tillförlitlighet för din [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) fungerar appar. 


## <a name="avoid-long-running-functions"></a>Undvika långvariga funktioner

Stora, långvariga funktioner kan orsaka oväntade timeout problem. En funktion kan bli stora på grund av många Node.js-beroenden. Importera beroenden kan också medföra ökad belastning gånger som resultera i oväntade timeout. Beroenden laddas både uttryckligen och implicit. En enda modul som lästs in av din kod kan läsa in en egen ytterligare moduler.  

När det är möjligt, flytta stora funktionerna i mindre funktionen anger som fungerar tillsammans och returnera svar snabbt. Till exempel kan en webhook eller HTTP-Utlösarfunktion kräva ett bekräftelse-svar inom en viss tid; Det är vanligt att webhooks kräver ett omedelbart svar. Du kan skicka HTTP-utlösaren nyttolast i en kö för att kunna bearbetas av en kö Utlösarfunktion. Den här metoden kan du skjuta upp det faktiska arbetet och returnera ett omedelbart svar.


## <a name="cross-function-communication"></a>Funktionen kommunikation mellan

När du integrerar flera funktioner, men det är vanligtvis en bra idé att använda lagringsköer för mellan funktionen kommunikation.  Det främsta skälet är lagringsköer är billigare och enklare att etablera. 

Enskilda meddelanden i en kö för lagring är begränsade i storlek till 64 KB. Om du behöver skicka större meddelanden mellan funktioner storlek en Azure Service Bus-kö kan användas för att stödja meddelande upp till 256 KB.

Service Bus-ämnen är användbara om du behöver meddelande filtrering innan bearbetning.

Händelsehubbar är användbara för att stödja kommunikation med hög volym.


## <a name="write-functions-to-be-stateless"></a>Skriva funktioner ska tillståndslös 

Funktioner som ska vara tillståndslösa och idempotent om möjligt. Koppla statusinformation som krävs till dina data. Till exempel en order bearbetas skulle förmodligen ha en associerad `state` medlem. En funktion kan bearbeta en ordning som baseras på det aktuella tillståndet eftersom själva funktionen förblir tillståndslösa. 

Idempotent funktioner är särskilt användbart med timer-utlösare. Till exempel om du har något som verkligen måste köras en gång om dagen, skriva den så den kan köras när under dagen med samma resultat. Funktionen kan avsluta när det finns inget arbete för en viss dag. Även om en tidigare körning kunde inte slutföra bör nästa körning hämta där den avbröts.


## <a name="write-defensive-functions"></a>Skriva defensiva funktioner

Anta att din funktion kan uppstå ett undantag när som helst. Utforma dina funktioner med möjligheten att fortsätta från en tidigare punkt misslyckas vid nästa körning. Överväg ett scenario som kräver följande åtgärder:

1. Frågan för 10 000 rader i en databas.
2. Skapa ett kömeddelande för var och en av de rader som ska bearbetas ytterligare ned.
 
Beroende på hur komplexa datorn är kanske: ingår nedströms tjänster fungerar felaktigt, nätverk avbrott eller kvot begränsar nått, etc. Alla dessa kan påverka funktionen när som helst. Måste du utformar dina funktioner att vara förberedd för den.

Hur koden reagera om ett fel uppstår när du har infogat 5 000 av dessa objekt i en kö för bearbetning? Spåra objekt i en samling som du har slutfört. Annars kan kan du lägga till dem igen nästa gång. Detta kan ha en betydande del av ditt arbetsflöde. 

Tillåt att funktionen ska vara en no-op om ett köobjekt redan hade bearbetats.

Dra nytta av försvarsåtgärder som redan ges för komponenter som du använder i Azure Functions-plattformen. Se exempelvis **hantering av skadligt Kömeddelanden** i dokumentationen för [Azure Storage-utlösare och bindningar](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Blanda inte test- och koden i appen med samma funktion

Funktioner i en funktionsapp dela resurser. Till exempel delas minne. Om du använder en funktionsapp i produktion, Lägg inte till test-relaterade funktioner och resurser till den. Det kan orsaka oväntade kostnader under kodkörning för produktion.

Var noga med att du laddar i dina appar för produktion-funktionen. Minne beräknas för varje funktion i appen.

Om du har en delad sammansättning som refereras i flera .net-funktioner kan du placera den i en gemensam delad mapp. Referera till en sammansättning med en instruktion som liknar följande exempel: 

    #r "..\Shared\MyAssembly.dll". 

Annars är det enkelt att av misstag distribuerar flera testversioner av samma binära som uppför sig annorlunda mellan funktioner.

Använd inte utförlig loggning i kod. Den har en negativ inverkan på prestanda.



## <a name="use-async-code-but-avoid-blocking-calls"></a>Använda async-kod men Undvik blockerar anrop

Asynkron programmering är en rekommenderad metod. Dock alltid undvika refererar till den `Result` egenskap eller anropa `Wait` metod i en `Task` instans. Den här metoden kan leda till uttömning tråd.


[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

Eftersom Azure Functions använder Azure App Service, bör du också vara medveten om Apptjänst riktlinjer.
* [Patterns and Practices HTTP prestandaoptimering](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)
