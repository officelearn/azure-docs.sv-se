---
title: Azure Service Bus avsnittet filter | Microsoft Docs
description: "Filtrera Azure Service Bus-ämnen"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: sethm
ms.openlocfilehash: b3fe467b7d6ae9b207956ece4980bf558a69761f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="topic-filters-and-actions"></a>Avsnittet Filter och åtgärder

Prenumeranter kan definiera vilka meddelanden som ska ta emot från ett ämne. Dessa meddelanden har angetts i form av en eller flera namngivna prenumerationsregler. Varje regel består av ett villkor som väljer viss meddelanden och en åtgärd som annoterar det markerade meddelandet. Prenumerationen skapas en kopia av meddelandet som får kommenteras på olika sätt för varje matchande regel för varje matchande regelvillkor.

Varje nyskapade avsnittet prenumerationen har en inledande Standardregeln för prenumerationen. Om du inte uttryckligen anger ett filtervillkor för regeln, är filtret används den **SANT** filter som gör att alla meddelanden kan väljas i prenumerationen. Standardregeln har ingen associerad anteckningen åtgärd.

Service Bus stöder tre filtervillkor:

-   *Booleska filter* – **TrueFilter** och **FalseFilter** antingen orsaka alla meddelanden (**SANT**) eller inget av de inkommande meddelandena (**FALSKT**) väljas för prenumerationen.

-   *SQL-filter* – en **SqlFilter** innehåller SQL-liknande villkorsuttryck som utvärderas i broker mot inkommande meddelanden användardefinierade egenskaper och Systemegenskaper. Alla Systemegenskaper måste föregås av `sys.` i villkorsuttrycket. Den [SQL-språket delmängd för filtervillkor](service-bus-messaging-sql-filter.md) tester förekomsten av egenskaper (EXISTS), samt för null-värden (är NULL), logiska inte-AND-OR, relationella operatorer, enkel Numerisk aritmetik och enkel textmönstret matchande med liknande.

-   *Korrelation filter* – en **CorrelationFilter** innehåller en uppsättning villkor som matchas mot en eller flera av användar- och egenskaper i ett inkommande meddelande. Används ofta för matchning mot den **CorrelationId** egenskap, men programmet kan också välja att matcha mot **ContentType**, **etikett**,  **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **till**, och alla användardefinierade Egenskaper. Det finns en matchning när ett inkommande meddelande värde för egenskapen är lika med värdet som angetts i filtret korrelation. För uttryck för anslutningssträng är jämförelse skiftlägeskänsliga. När du anger flera egenskaper för matchar filtret kombinera dem som ett logiskt och-villkor, vilket innebär att filtret för att matcha, alla villkor måste matcha.

Alla filter utvärdera meddelandeegenskaper. Filter kan inte utvärdera brödtext.

Komplexa filterregler kräver bearbetningskapacitet. I synnerhet resulterar användning av SQL-filterregler i lägre totala genomströmningen på nivån prenumeration, ämnet och namnområde. När det är möjligt bör program välja korrelation filter över SQL-liknande filter, eftersom de är mycket effektivare i bearbetningen och därför har mindre påverkan på genomflöde.

## <a name="actions"></a>Åtgärder

Med SQL filtervillkor, och endast de, kan du definiera en åtgärd som kan kommentera meddelandet genom att lägga till, ta bort eller ersätta egenskaper och deras värden. Åtgärden [använder ett SQL-liknande uttryck](service-bus-messaging-sql-filter.md) som löst leans på SQL-UPPDATERINGEN instruktionen syntax. Åtgärden utförs på meddelandet när den har har matchats och innan meddelandet är markerad i avsnittet. Ändringarna i egenskaperna för meddelandet är privat för meddelandet kopieras till prenumerationen.

## <a name="usage-patterns"></a>Användningsmönster

Det enklaste användningsscenariot som för ett ämne är att varje prenumeration får en kopia av varje meddelande som skickas till ett ämne, vilket gör att ett broadcast mönster.

Filter och åtgärder gör att två ytterligare grupper av mönster: partitionering och routning.

Partitionering använder filter distribuerar meddelanden över flera befintliga ämnesprenumerationer förutsägbar och ömsesidigt uteslutande. Partitionering mönstret används när ett system skalas ut för att hantera många olika kontexter i funktionen identisk avdelningar som var och en innehåller en delmängd av den övergripande data. till exempel profil kundinformation. Med partitionering, skickar en utgivare meddelandet till ett ämne utan kännedom om partitionering modellen. Meddelandet flyttas sedan till korrekt prenumeration som den sedan kan hämtas av den partitionen meddelandehanteraren.

Filter routing används för att distribuera meddelanden i avsnittet prenumerationer på ett förutsägbart sätt, men inte nödvändigtvis samtidigt. Tillsammans med den [automatisk vidarebefordran](service-bus-auto-forwarding.md) funktionen avsnittet filter som kan användas för att skapa komplex routning diagram i ett Service Bus-namnområde för meddelande-distribution i en Azure-region. Du kan skapa komplexa globala topologier med direkta affärsprogram-integrering med Azure Functions eller Azure Logic Apps fungerar som en brygga mellan Azure Service Bus-namnområden.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [SQLFilter-syntax](service-bus-messaging-sql-filter.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)