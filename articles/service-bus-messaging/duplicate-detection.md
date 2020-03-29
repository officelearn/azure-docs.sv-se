---
title: Identifiering av azure servicebuss-dubblettmeddelande | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan identifiera dubbletter i Azure Service Bus-meddelanden. Dubblettmeddelandet kan ignoreras och tas bort.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: c109b9fd310a09e5eb4c6d18cc3536e4d8069c0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760376"
---
# <a name="duplicate-detection"></a>Dubblettidentifiering

Om ett program misslyckas på grund av ett allvarligt fel omedelbart efter att det har skickat ett meddelande och den omstartade programinstansen felaktigt anser att leveransen av tidigare meddelanden inte inträffade, gör en efterföljande sändning att samma meddelande visas i systemet två gånger.

Det är också möjligt att ett fel på klient- eller nätverksnivå inträffar en stund tidigare och att ett skickat meddelande ska bekräftas i kön, och bekräftelsen inte har returnerats till klienten. Det här scenariot gör att klienten är osäker på resultatet av sändningsåtgärden.

Dubblettidentifiering tar tvivel ur dessa situationer genom att göra det möjligt för avsändaren att skicka samma meddelande igen, och kön eller ämnet ignorerar alla dubblettkopior.

Om du aktiverar dubblettidentifiering håller du reda på det programkontrollerade *MessageId* för alla meddelanden som skickas till en kö eller ett ämne under ett angivet tidsfönster. Om ett nytt meddelande skickas med *MessageId* som loggades under tidsfönstret rapporteras meddelandet som accepterat (skicka-åtgärden lyckas), men det nyligen skickade meddelandet ignoreras och tas bort direkt. Inga andra delar av meddelandet än *MessageId* beaktas.

Programkontroll av identifieraren är nödvändig, eftersom endast det tillåter programmet att binda *MessageId* till en affärsprocesskontext som den kan rekonstrueras från förutsägbart när ett fel inträffar.

För en affärsprocess där flera meddelanden skickas i samband med hantering av vissa programkontexter kan *MessageId* vara en sammansatt av kontextidentifieraren på programnivå, till exempel ett inköpsordernummer och meddelandets ämne, till exempel **12345.2017/betalning**.

*MessageId* kan alltid vara något GUID, men att förankra identifieraren för affärsprocessen ger förutsägbar repeterbarhet, vilket önskas för att utnyttja dubblettidentifieringsfunktionen effektivt.

> [!NOTE]
> Om dubblettidentifieringen är aktiverad och sessions-ID eller partitionsnyckel inte har angetts används meddelande-ID som partitionsnyckel. Om meddelande-ID:n inte heller har angetts genererar .NET- och AMQP-bibliotek automatiskt ett meddelande-ID för meddelandet. Mer information finns i [Använda partitionsnycklar](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Aktivera dubblettidentifiering

I portalen aktiveras funktionen när entiteten skapas med kryssrutan **Aktivera dubblettidentifiering,** som är inaktiverad som standard. Inställningen för att skapa nya ämnen är likvärdig.

![][1]

> [!IMPORTANT]
> Du kan inte aktivera/inaktivera dubblettidentifiering när kön har skapats. Du kan bara göra det när du skapar kön. 

Programmässigt anger du flaggan med egenskapen [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) på hela ramverket .NET API. Med Azure Resource Manager API anges värdet med egenskapen [queueProperties.requiresDuplicateDetection.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

Den dubbla identifieringstidshistoriken är som standard 30 sekunder för köer och ämnen, med ett maximalt värde på sju dagar. Du kan ändra den här inställningen i fönstret för kö- och ämnesegenskaper i Azure-portalen.

![][2]

Programmässigt kan du konfigurera storleken på det dubblettidentifieringsfönster under vilket meddelande-id:er behålls med egenskapen [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) med det fullständiga .NET Framework-API:et. Med Azure Resource Manager API anges värdet med egenskapen [queueProperties.duplicateDetectionHistoryTimeWindow.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

Om du aktiverar dubblettidentifiering och fönstrets storlek påverkar direkt ködataflödet (och ämnets) dataflöde, eftersom alla inspelade meddelande-ID:er måste matchas mot den nyligen skickade meddelandeidentifieraren.

Att hålla fönstret litet innebär att färre meddelande-id:er måste behållas och matchas och dataflödet påverkas mindre. För enheter med högt dataflöde som kräver dubblettidentifiering bör du hålla fönstret så litet som möjligt.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

I scenarier där klientkoden inte kan skicka ett meddelande med samma *MessageId* som tidigare är det viktigt att utforma meddelanden som kan bearbetas på ett säkert sätt. Detta [blogginlägg om idempotence](https://particular.net/blog/what-does-idempotent-mean) beskriver olika tekniker för hur man gör det.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
