---
title: Azure Service Bus-ämnesfilter | Microsoft Docs
description: Filtrera Azure Service Bus-ämnen
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: 41af53dbfbb5c863007a332445a2f184fcbcbf81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332235"
---
# <a name="topic-filters-and-actions"></a>Ämnesfilter och åtgärder

Prenumeranter kan definiera vilka meddelanden som de vill ta emot från ett ämne. Dessa meddelanden har angetts i form av en eller flera prenumerationsregler för namngivna. Varje regel består av ett villkor som väljer specifika meddelanden och en åtgärd som annoterar det markerade meddelandet. Prenumerationen skapar en kopia av meddelandet som får kommenteras på olika sätt för varje matchande regel för varje matchande regelvillkor.

Varje nyligen skapade avsnittsprenumeration har en inledande Standardregeln för prenumerationen. Om du inte uttryckligen anger ett filtervillkor för regeln, använda filter är den **SANT** filter som gör att alla meddelanden som ska väljas i prenumerationen. Standardregeln har ingen associerad anteckning-åtgärd.

Service Bus stöder tre filtervillkor:

-   *Booleska filter* – **TrueFilter** och **FalseFilter** antingen orsaka alla inkommande meddelanden (**SANT**) eller inga av dessa data anländer meddelanden (**FALSKT**) som ska väljas för prenumerationen.

-   *SQL-filter* – en **SqlFilter** innehåller SQL-liknande villkorsuttryck som ska utvärderas i den asynkrona meddelandekön mot dessa data anländer meddelandena användardefinierade egenskaper och Systemegenskaper. Alla Systemegenskaper måste föregås `sys.` villkorlig uttrycket. Den [SQL-språket delmängd för filtervillkor](service-bus-messaging-sql-filter.md) tester förekomsten av egenskaper (`EXISTS`), och för null-värden (`IS NULL`), logiska inte/och/eller, relationsoperatorer, enkel numerisk aritmetiska, och enkel text mönstermatchning med `LIKE`.

-   *Korrelationsfilter* – en **CorrelationFilter** innehåller en uppsättning villkor som matchas mot en eller flera av meddelandet dessa data anländer användar- och egenskaper. Ett vanligt användningsområde är att matcha mot den **CorrelationId** egenskapen, men programmet kan också välja att matcha mot **ContentType**, **etikett**,  **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **till**, och alla användardefinierade Egenskaper. Det finns en matchning när meddelandet dessa data anländer värde för egenskapen är lika med värdet som anges i korrelationsfiltret. För uttryck för anslutningssträng är jämförelsen skiftlägeskänsliga. När du anger flera egenskaper för matchar filtret kombinerar dem som en logisk och-villkor, vilket innebär att för filtret för att matcha, alla villkor måste matcha.

Alla filter utvärdera meddelandeegenskaper. Filter kan inte utvärdera meddelandetexten.

Komplexa filterregler kräver bearbetningskapacitet. Användningen av SQL-filterregler resulterar i synnerhet i lägre övergripande meddelandedataflöde på prenumerationen, ämnet och namnområde. När det är möjligt bör program välja korrelationsfilter över SQL-liknande filter, eftersom de är mycket effektivare i bearbetningen och därför har mindre påverkan på dataflöde.

## <a name="actions"></a>Åtgärder

Med SQL-filtervillkor, kan du definiera en åtgärd som kan kommentera meddelandet genom att lägga till, ta bort eller ersätta egenskaper och deras värden. Åtgärden [använder ett SQL-liknande uttryck](service-bus-messaging-sql-filter.md) som löst leans på uppdatering för SQL-instruktionen syntax. Åtgärden utförs på meddelandet när den har matchats och innan meddelandet väljs i prenumerationen. Ändringar i meddelandeegenskaperna är privata för meddelandet som kopieras till prenumerationen.

## <a name="usage-patterns"></a>Användningsmönster

Det enklaste användningsscenariot där för ett ämne är att varje prenumeration får en kopia av varje meddelande som skickas till ett ämne, vilket gör att ett broadcast mönster.

Filter och åtgärder kan två ytterligare grupper av mönster: partitionering och routning.

Partitionering använder filter för att distribuera meddelanden över flera befintliga ämnesprenumerationer på ett förutsägbart och ömsesidigt uteslutande sätt. Partitionering mönstret används när ett system har skalats ut för att hantera många olika kontexter i funktionen identisk avdelningar som var och en innehåller en delmängd av övergripande uppgifterna. till exempel kund profilinformation. Med partitionering, skickar en utgivare meddelandet till ett ämne utan någon kunskap om partitionering modellen. Meddelandet flyttas sedan till rätt prenumeration där det kan sedan hämta meddelandehanterare för den partition.

Routing används filter för att distribuera meddelanden över ämnesprenumerationer på ett förutsägbart sätt, men inte nödvändigtvis exklusivt. Tillsammans med den [automatisk vidarebefordran](service-bus-auto-forwarding.md) funktion, avsnittet filter kan användas för att skapa komplex routning diagram i ett Service Bus-namnområde för distribution av meddelande i en Azure-region. Du kan skapa komplexa globala topologier med direkt integrering i line-of-business-program med Azure Functions eller Azure Logic Apps fungerar som en brygga mellan Azure Service Bus-namnområden.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [SQLFilter-syntax](service-bus-messaging-sql-filter.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)