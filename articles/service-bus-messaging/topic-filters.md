---
title: Ämnesfilter för Azure Service Bus | Microsoft-dokument
description: I den här artikeln beskrivs hur prenumeranter kan definiera vilka meddelanden de vill ta emot från ett ämne genom att ange filter.
services: service-bus-messaging
documentationcenter: ''
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: spelluru
ms.openlocfilehash: fb6092b7ccb3d1a4214f8d26119d9dc50b0ed317
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482055"
---
# <a name="topic-filters-and-actions"></a>Ämnesfilter och åtgärder

Prenumeranter kan definiera vilka meddelanden som de vill ta emot från ett ämne. Dessa meddelanden anges i form av en eller flera namngivna prenumerationsregler. Varje regel består av ett villkor som markerar vissa meddelanden och en åtgärd som kommenterar det markerade meddelandet. Prenumerationen skapar en kopia av meddelandet som får kommenteras på olika sätt för varje matchande regel för varje matchande regelvillkor.

Varje nyskapade ämnesprenumeration har en första standardprenumerationsregel. Om du inte uttryckligen anger ett filtervillkor för regeln är det tillämpade filtret det **verkliga** filter som gör att alla meddelanden kan väljas i prenumerationen. Standardregeln har ingen associerad anteckningsåtgärd.

Service Bus stöder tre filterförhållanden:

-   *Booleska filter* - **TrueFilter** och **FalseFilter** antingen orsakar alla ankommande meddelanden (**sant)** eller ingen av de ankommande meddelanden (**falskt**) som ska väljas för prenumerationen.

-   *SQL-filter* - A **SqlFilter** innehåller ett SQL-liknande villkorsuttryck som utvärderas i mäklaren mot de ankommande meddelandenas användardefinierade egenskaper och systemegenskaper. Alla systemegenskaper måste föregås `sys.` av i villkorsuttrycket. [Den SQL-språkundergrupp för filtervillkorstester](service-bus-messaging-sql-filter.md) för`EXISTS`förekomsten av`IS NULL`egenskaper ( ), null-värden ( ), logisk NOT/OCH/ELLER, relationsoperatorer, enkel numerisk aritmetik och enkel textmönstermatchning med `LIKE`.

-   *Korrelationsfilter* - Ett **Korrelationsfilter** innehåller en uppsättning villkor som matchas mot en eller flera av ett ankommande meddelandes användar- och systemegenskaper. En vanlig användning är att matcha mot egenskapen **CorrelationId,** men programmet kan också välja att matcha mot följande egenskaper:

    - **Contenttype**
     - **Etikett**
     - **Messageid**
     - **ReplyTo**
     - **Svara TillSessionId**
     - **Sessionid** 
     - **Att**
     - användardefinierade egenskaper. 
     
     Det finns en matchning när ett ankommande meddelandes värde för en egenskap är lika med det värde som anges i korrelationsfiltret. För stränguttryck är jämförelsen skiftlägeskänslig. När du anger flera matchningsegenskaper kombinerar filtret dem som ett logiskt OCH-villkor, vilket innebär att filtret matchar, alla villkor måste matcha.

Alla filter utvärderar meddelandeegenskaper. Filter kan inte utvärdera meddelandetexten.

Komplexa filterregler kräver bearbetningskapacitet. I synnerhet orsakar användningen av SQL-filterregler lägre övergripande meddelandedataflöde på prenumerations-, ämnes- och namnområdesnivå. När det är möjligt bör program välja korrelationsfilter över SQL-liknande filter eftersom de är mycket effektivare i bearbetning och har mindre inverkan på dataflödet.

## <a name="actions"></a>Åtgärder

Med SQL-filtervillkor kan du definiera en åtgärd som kan kommentera meddelandet genom att lägga till, ta bort eller ersätta egenskaper och deras värden. Åtgärden [använder ett SQL-liknande uttryck som](service-bus-messaging-sql-filter.md) löst lutar åt SQL UPDATE-satssyntaxen. Åtgärden görs på meddelandet efter att det har matchats och innan meddelandet väljs till prenumerationen. Ändringarna i meddelandeegenskaperna är privata för meddelandet som kopieras till prenumerationen.

## <a name="usage-patterns"></a>Användningsmönster

Det enklaste användningsscenariot för ett ämne är att varje prenumeration får en kopia av varje meddelande som skickas till ett ämne, vilket möjliggör ett broadcast-mönster.

Filter och åtgärder möjliggör ytterligare två grupper av mönster: partitionering och routning.

Partitionering använder filter för att distribuera meddelanden över flera befintliga ämnesprenumerationer på ett förutsägbart och ömsesidigt uteslutande sätt. Partitioneringsmönstret används när ett system skalas ut för att hantera många olika kontexter i funktionellt identiska fack som var och en innehåller en delmängd av de övergripande data. till exempel kundprofilinformation. Med partitionering skickar en utgivare meddelandet till ett ämne utan att kräva någon kunskap om partitioneringsmodellen. Meddelandet flyttas sedan till rätt prenumeration som det sedan kan hämtas från av partitionens meddelandehanterare.

Routning använder filter för att distribuera meddelanden mellan ämnesprenumerationer på ett förutsägbart sätt, men inte nödvändigtvis exklusivt. Tillsammans med funktionen [för automatisk vidarebefordran](service-bus-auto-forwarding.md) kan ämnesfilter användas för att skapa komplexa routningsdiagram i ett servicebussnamnområde för meddelandedistribution inom en Azure-region. Med Azure Functions eller Azure Logic Apps som en brygga mellan Azure Service Bus-namnområden kan du skapa komplexa globala topologier med direkt integrering i affärsprogram.


> [!NOTE]
> Azure-portalen låter dig inte ange filterregler för prenumerationer. Du kan använda någon av de SDK:er eller Azure Resource Manager-mallarna som stöds för att definiera prenumerationsregler. 

## <a name="next-steps"></a>Nästa steg
Se följande exempel: 

- [.NET - Grundläggande skicka och ta emot handledning med filter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - Ämnesfilter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/javascript/advanced/topicFilters.js)
- [Skriv skript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/typescript/src/advanced/topicFilters.ts)
- [Azure Resource Manager-mall](https://docs.microsoft.com/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)


