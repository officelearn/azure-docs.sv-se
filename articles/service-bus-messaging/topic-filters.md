---
title: Ämnesfilter för Azure Service Bus | Microsoft-dokument
description: I den här artikeln beskrivs hur prenumeranter kan definiera vilka meddelanden de vill ta emot från ett ämne genom att ange filter.
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
ms.date: 01/27/2020
ms.author: spelluru
ms.openlocfilehash: b8ffbb16763bfe6485ebf2ab770f4537ddbc8569
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774502"
---
# <a name="topic-filters-and-actions"></a>Ämnesfilter och åtgärder

Prenumeranter kan definiera vilka meddelanden som de vill ta emot från ett ämne. Dessa meddelanden anges i form av en eller flera namngivna prenumerationsregler. Varje regel består av ett villkor som markerar vissa meddelanden och en åtgärd som kommenterar det markerade meddelandet. Prenumerationen skapar en kopia av meddelandet som får kommenteras på olika sätt för varje matchande regel för varje matchande regelvillkor.

Varje nyskapade ämnesprenumeration har en första standardprenumerationsregel. Om du inte uttryckligen anger ett filtervillkor för regeln är det tillämpade filtret det **verkliga** filter som gör att alla meddelanden kan väljas i prenumerationen. Standardregeln har ingen associerad anteckningsåtgärd.

Service Bus stöder tre filterförhållanden:

-   *Booleska filter* - **TrueFilter** och **FalseFilter** antingen orsakar alla ankommande meddelanden (**sant)** eller ingen av de ankommande meddelanden (**falskt**) som ska väljas för prenumerationen.

-   *SQL-filter* - A **SqlFilter** innehåller ett SQL-liknande villkorsuttryck som utvärderas i mäklaren mot de ankommande meddelandenas användardefinierade egenskaper och systemegenskaper. Alla systemegenskaper måste föregås `sys.` av i villkorsuttrycket. [Den SQL-språka delmängden för filtervillkorstester](service-bus-messaging-sql-filter.md) för förekomsten av egenskaper (`EXISTS`), samt för null-värden (`IS NULL`), logisk NOT/OCH/ELLER, `LIKE`relationsoperatorer, enkel numerisk aritmetik och enkel textmönster som matchar med .

-   *Korrelationsfilter* - Ett **Korrelationsfilter** innehåller en uppsättning villkor som matchas mot en eller flera av ett ankommande meddelandes användar- och systemegenskaper. En vanlig användning är att matcha mot egenskapen **CorrelationId,** men programmet kan också välja att matcha mot **ContentType**, **Label**, **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **Till**och alla användardefinierade egenskaper. Det finns en matchning när ett ankommande meddelandes värde för en egenskap är lika med det värde som anges i korrelationsfiltret. För stränguttryck är jämförelsen skiftlägeskänslig. När du anger flera matchningsegenskaper kombinerar filtret dem som ett logiskt OCH-villkor, vilket innebär att filtret matchar, alla villkor måste matcha.

Alla filter utvärderar meddelandeegenskaper. Filter kan inte utvärdera meddelandetexten.

Komplexa filterregler kräver bearbetningskapacitet. I synnerhet resulterar användningen av SQL-filterregler i lägre övergripande meddelandedataflöde på prenumerations-, ämnes- och namnområdesnivå. När det är möjligt bör program välja korrelationsfilter över SQL-liknande filter, eftersom de är mycket effektivare i bearbetning och därför har mindre inverkan på dataflödet.

## <a name="actions"></a>Åtgärder

Med SQL-filtervillkor kan du definiera en åtgärd som kan kommentera meddelandet genom att lägga till, ta bort eller ersätta egenskaper och deras värden. Åtgärden [använder ett SQL-liknande uttryck som](service-bus-messaging-sql-filter.md) löst lutar åt SQL UPDATE-satssyntaxen. Åtgärden utförs på meddelandet efter att det har matchats och innan meddelandet väljs till prenumerationen. Ändringarna i meddelandeegenskaperna är privata för meddelandet som kopieras till prenumerationen.

## <a name="usage-patterns"></a>Användningsmönster

Det enklaste användningsscenariot för ett ämne är att varje prenumeration får en kopia av varje meddelande som skickas till ett ämne, vilket möjliggör ett broadcast-mönster.

Filter och åtgärder möjliggör ytterligare två grupper av mönster: partitionering och routning.

Partitionering använder filter för att distribuera meddelanden över flera befintliga ämnesprenumerationer på ett förutsägbart och ömsesidigt uteslutande sätt. Partitioneringsmönstret används när ett system skalas ut för att hantera många olika kontexter i funktionellt identiska fack som var och en innehåller en delmängd av de övergripande data. till exempel kundprofilinformation. Med partitionering skickar en utgivare meddelandet till ett ämne utan att kräva någon kunskap om partitioneringsmodellen. Meddelandet flyttas sedan till rätt prenumeration som det sedan kan hämtas från av partitionens meddelandehanterare.

Routning använder filter för att distribuera meddelanden mellan ämnesprenumerationer på ett förutsägbart sätt, men inte nödvändigtvis exklusivt. Tillsammans med funktionen [för automatisk vidarebefordran](service-bus-auto-forwarding.md) kan ämnesfilter användas för att skapa komplexa routningsdiagram i ett servicebussnamnområde för meddelandedistribution inom en Azure-region. Med Azure Functions eller Azure Logic Apps som en brygga mellan Azure Service Bus-namnområden kan du skapa komplexa globala topologier med direkt integrering i affärsprogram.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [SQLFilter-syntax](service-bus-messaging-sql-filter.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)