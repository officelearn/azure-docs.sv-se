---
title: Azure Service Bus identifiering av duplicerade meddelanden | Microsoft Docs
description: Identifiera dubbla Service Bus meddelanden
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: bee8c1d2a1cd313c7fe59d8e53379dc57554e98c
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618566"
---
# <a name="duplicate-detection"></a>Dubblettidentifiering

Om ett program Miss lyckas på grund av ett allvarligt fel omedelbart efter det att ett meddelande har skickats, och den omstartade program instansen inte anser att den tidigare meddelande leveransen inte har uppfyllts, kan en efterföljande sändning orsaka att samma meddelande visas i systemet två gånger.

Det är också möjligt att ett fel på klienten eller på nätverks nivå inträffar en stund och att ett meddelande som skickas skickas till kön, med bekräftelsen som inte har returnerats till klienten. Det här scenariot lämnar klienten tveksam om resultatet av åtgärden skicka.

Dubblettidentifiering tar bort tvivel från dessa situationer genom att göra det möjligt för avsändaren att skicka samma meddelande igen och kön eller avsnittet tar bort dubbletter av kopior.

Genom att aktivera dubblettidentifiering kan du hålla koll på det programstyrda *messageid* för alla meddelanden som skickas till en kö eller ett ämne under en angiven tids period. Om ett nytt meddelande skickas med *messageid* som loggades under tids perioden, rapporteras meddelandet som accepterat (sändnings åtgärden lyckas), men det nyligen skickade meddelandet ignoreras och tas bort omedelbart. Inga andra delar av meddelandet förutom *messageid* beaktas.

Program kontroll av identifieraren är nödvändig, eftersom endast det gör att programmet kan koppla *messageid* till en affärs process kontext som det kan förkonstrueras på ett förutsägbart sätt när ett fel uppstår.

För en affärs process där flera meddelanden skickas i samband med hantering av vissa program kontexter kan *messageid* vara en sammansatt av Sammanhangs identifieraren på program nivå, till exempel ett inköps order nummer och meddelandets ämne, för exempel, **12345.2017/betalning**.

*Messageid* kan alltid vara en del av GUID, men för att fästa identifieraren till affärs processen får du förutsägbar repeterbarhet, vilket är praktiskt om du vill använda funktionen för dubblettidentifiering på ett effektivt sätt.

> [!NOTE]
> Om dubblettidentifiering har Aktiver ATS och sessions-ID eller partitionsnyckel inte har angetts, används meddelande-ID: t som partitionsnyckel. Om meddelande-ID inte heller har angetts genererar .NET-och AMQP-bibliotek automatiskt ett meddelande-ID för meddelandet. Mer information finns i [använda partitionerings nycklar](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Aktivera dubblettidentifiering

I portalen aktive ras funktionen när entiteten skapas med kryss rutan **Aktivera dubblettidentifiering** , som är avstängd som standard. Inställningen för att skapa nya ämnen är likvärdig.

![][1]

> [!IMPORTANT]
> Du kan inte aktivera/inaktivera dubblettidentifiering när kön har skapats. Du kan bara göra det när du skapar kön. 

Program mässigt ställer du in flaggan med egenskapen [QueueDescription. requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) i hela Framework .NET-API: et. Med Azure Resource Manager API anges värdet med egenskapen [queueProperties. requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Tids historiken för dubblettidentifiering är som standard 30 sekunder för köer och ämnen, med ett maximalt sju dagar. Du kan ändra den här inställningen i fönstret för kö-och ämnes egenskaper i Azure Portal.

![][2]

Program mässigt kan du konfigurera storleken på det fönster för dubblettidentifiering där meddelande-ID: n bevaras med hjälp av egenskapen [QueueDescription. DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) med fullständig .NET Framework-API. Med Azure Resource Manager API anges värdet med egenskapen [queueProperties. duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Aktivering av dubblettidentifiering och storleken på fönstret påverkar data flödet i kön (och ämnet), eftersom alla inspelade meddelande-ID: n måste matchas mot den nyligen skickade meddelande identifieraren.

Att hålla nere fönstret innebär att färre meddelande-ID: n måste behållas och matchas, och data flödet påverkas mindre. För stora data flödes enheter som kräver dubblettidentifiering bör du ha fönstret så litet som möjligt.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
