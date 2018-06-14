---
title: Azure Service Bus dubbla meddelanden identifiering | Microsoft Docs
description: Hitta dubblerade Service Bus-meddelanden
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
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: efc5608d4812edbb3f477dffbc2b495b331bd787
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
ms.locfileid: "28198561"
---
# <a name="duplicate-detection"></a>Identifiering av dubbletter

Om ett program råkar ut för ett allvarligt fel omedelbart efter den skickar ett meddelande och startas om programinstansen tror felaktigt att den tidigare meddelandeleverans inte förekommer, gör att en efterföljande skicka samma meddelande visas två gånger i systemet.

Det är också möjligt för ett fel på klienten eller nätverket ska ske en kort stund tidigare och för ett meddelande som skickades till allokeras till kön med bekräftelsen gick inte att returneras till klienten. Det här scenariot lämnar klienten tveksam om resultatet av send-åtgärden.

Identifiering av dubbletter tar osäker utanför dessa situationer genom att aktivera den avsändaren att skicka samma meddelande och kön eller avsnittet ignoreras eventuella dubbletter.

Aktivera identifiering av dubbletter hjälper dig att hålla reda på program-kontrollerade *MessageId* av alla meddelanden som skickas till en kö eller ett ämne under ett angivet tidsintervall. Om några nya meddelande skickas med en *MessageId* som har redan loggats under tidsfönstret, meddelandet rapporteras som accepteras (send-åtgärden lyckas), men det nyligen skickade meddelandet ignoreras och tas bort omedelbart. Inga andra delar av meddelandet än den *MessageId* anses.

Programkontrollen av identifieraren är viktigt, eftersom endast som gör det möjligt för program att koppla den *MessageId* till en business process kontext där den förutsägbart rekonstrueras om ett fel uppstår.

För en process där flera meddelanden skickas under hantering av vissa programkontexten den *MessageId* kan vara en kombination av programnivå kontext-ID, till exempel ett inköpsordernummer och ämne för meddelandet. till exempel **12345.2017/betalning**.

Den *MessageId* kan alltid vissa GUID, men inställningen identifierare till business-processen ger förutsägbar repeterbarhet som behövs för att kunna utnyttja funktionen för dubblettidentifiering effektivt.

## <a name="enable-duplicate-detection"></a>Aktivera dubblettidentifiering

I portalen funktionen är aktiverad när entiteten skapas med den **aktivera dubblettidentifiering** kryssrutan som är inaktiverat som standard. Inställningen för att skapa nya avsnitt är likvärdiga.

![][1]

Anger programmässigt, flaggan med den [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) egenskapen på fullständig framework .NET-API. Med Azure Resource Manager API är värdet med den [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) egenskapen.

Som standard den dubblettidentifiering historik över 30 sekunder för köer och ämnen, med ett maximalt värde för 7 dagar. Du kan ändra inställningen i fönstret kön, ämnet och egenskaper i Azure-portalen.

![][2]

Programmässigt, du kan konfigurera storleken på fönstret dubblettidentifiering då bevaras meddelande-ID, den [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) egenskap med fullständig .NET Framework-API . Med Azure Resource Manager API är värdet med den [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) egenskapen.

Observera att aktivera identifiering av dubbletter och storleken på fönstret direkt påverka kön (och avsnittet) dataflöde, eftersom alla inspelade meddelande-ID: n måste matchas mot nyligen skickade meddelandet identifierare.

Att hålla den fönstret små innebär att färre meddelande-ID: n måste vara kvar och matchade och dataflöde är påverkas mindre. För högt genomflöde entiteter som kräver identifiering av dubbletter, bör du behålla fönstret så mycket som möjligt.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
