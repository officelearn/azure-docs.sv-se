---
title: Azure Service Bus ämnes filter | Microsoft Docs
description: Den här artikeln förklarar hur prenumeranter kan definiera vilka meddelanden som ska tas emot från ett ämne genom att ange filter.
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774502"
---
# <a name="topic-filters-and-actions"></a>Ämnesfilter och åtgärder

Prenumeranter kan definiera vilka meddelanden som de vill ta emot från ett ämne. Dessa meddelanden anges i form av en eller flera namngivna prenumerations regler. Varje regel består av ett villkor som väljer specifika meddelanden och en åtgärd som kommenterar det valda meddelandet. Prenumerationen skapar en kopia av meddelandet som får kommenteras på olika sätt för varje matchande regel för varje matchande regelvillkor.

Varje nyligen skapad ämnes prenumeration har en första standard prenumerations regel. Om du inte uttryckligen anger ett filter villkor för regeln, är det filter som används det **sanna** filtret som gör att alla meddelanden kan väljas i prenumerationen. Standard regeln har ingen associerad antecknings åtgärd.

Service Bus stöder tre filter villkor:

-   *Booleska filter* – **TrueFilter** och **FalseFilter** kan antingen orsaka att alla inkommande meddelanden (**True**) eller inget av de inkommande meddelandena (**falskt**) väljs för prenumerationen.

-   *SQL-filter* – en **SQLFILTER** innehåller ett SQL-liknande villkorligt uttryck som utvärderas i Service Broker mot de inkommande meddelanden som är användardefinierade egenskaper och system egenskaper. Alla system egenskaper måste föregås av `sys.` i villkors uttrycket. [SQL-språkets delmängd för filter villkor](service-bus-messaging-sql-filter.md) testar om det finns egenskaper (`EXISTS`), samt för null-värden (`IS NULL`), logiska not/och/eller, Relations operatorer, enkla numeriska aritmetiska och enkla text mönster som matchar `LIKE`.

-   *Korrelations filter* – en **CorrelationFilter** innehåller en uppsättning villkor som matchas mot ett eller flera av de inloggade meddelandets användar-och system egenskaper. Ett vanligt användnings sätt är att matcha mot egenskapen **correlationId** , men programmet kan också välja att matcha med **ContentType**, **etikett**, **messageid**, **ReplyTo**, **ReplyToSessionId**, **SessionID**, **till**och alla användardefinierade egenskaper. En matchning finns när värdet för en egenskap som anländer är lika med värdet som anges i korrelations filtret. Jämförelsen är Skift läges känslig för sträng uttryck. När du anger flera matchnings egenskaper kombinerar filtret dem som ett logiskt och villkor, vilket innebär att filtret ska matcha, alla villkor måste matcha.

Alla filter utvärderar meddelande egenskaper. Filter kan inte utvärdera meddelande texten.

Komplexa filter regler kräver bearbetnings kapacitet. I synnerhet resulterar användningen av SQL filter-regler i ett nedre övergripande meddelande genom strömning på prenumerations-, ämnes-och namn områdes nivå. När det är möjligt bör program välja korrelations filter över SQL-liknande filter, eftersom de är mycket mer effektiva i bearbetningen och därför har mindre påverkan på data flödet.

## <a name="actions"></a>Åtgärder

Med villkor för SQL-filter kan du definiera en åtgärd som kan kommentera meddelandet genom att lägga till, ta bort eller ersätta egenskaper och deras värden. Åtgärden [använder ett SQL-like-uttryck som har en](service-bus-messaging-sql-filter.md) felaktig Lean-syntax i SQL Update-uttrycket. Åtgärden utförs på meddelandet när den har matchats och innan meddelandet har marker ATS i prenumerationen. Ändringarna i meddelande egenskaperna är privata för det meddelande som kopieras till prenumerationen.

## <a name="usage-patterns"></a>Användnings mönster

Det enklaste användnings scenariot för ett ämne är att varje prenumeration får en kopia av varje meddelande som skickas till ett ämne, vilket möjliggör ett sändnings mönster.

Filter och åtgärder möjliggör två ytterligare grupper av mönster: partitionering och routning.

Partitionering använder filter för att distribuera meddelanden över flera befintliga ämnes prenumerationer på ett förutsägbart och ömsesidigt uteslutande sätt. Partitionerings mönstret används när ett system skalas ut för att hantera många olika kontexter i funktions identiska avdelningar som var och en innehåller en delmängd av de totala data. till exempel kund profil information. Med partitionering skickar en utgivare meddelandet till ett ämne utan att kräva någon kunskap om partitionerings modellen. Meddelandet flyttas sedan till rätt prenumeration från vilken det sedan kan hämtas av partitionens meddelande hanterare.

Routning använder filter för att distribuera meddelanden över ämnes prenumerationer på ett förutsägbart sätt, men inte nödvändigt vis exklusivt. I samband med funktionen för [automatisk vidarebefordring](service-bus-auto-forwarding.md) kan ämnes filter användas för att skapa komplexa vägvals diagram inom ett Service Bus namn område för meddelande distribution inom en Azure-region. Med Azure Functions eller Azure Logic Apps fungerar som en bro mellan Azure Service Bus namnrum kan du skapa komplexa globala topologier med direkt integrering i branschspecifika program.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [SQLFilter-syntax](service-bus-messaging-sql-filter.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)