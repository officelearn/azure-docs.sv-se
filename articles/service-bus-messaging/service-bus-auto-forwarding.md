---
title: Vidarebefordring av Azure Service Bus-meddelandeentiteter
description: I den här artikeln beskrivs hur du kedjar en Azure Service Bus-kö eller prenumeration till en annan kö eller ett annat ämne.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8b8883b579233962de61e7247e6ac1cbcb2a6d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761057"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Kedja servicebussenheter med automatisk vidarebefordran

Med funktionen *Automatisk vidarebefordran av* Service Bus kan du kedja en kö eller prenumeration på en annan kö eller ett annat ämne som ingår i samma namnområde. När automatisk vidarebefordran är aktiverat tar Service Bus automatiskt bort meddelanden som placeras i den första kön eller prenumerationen (källa) och placerar dem i den andra kön eller det andra avsnittet (målet). Det är fortfarande möjligt att skicka ett meddelande till målentiteten direkt.

## <a name="using-autoforwarding"></a>Använda automatisk vidarebefordran

Du kan aktivera automatisk vidarebefordran genom att ange [egenskaperna QueueDescription.ForwardTo][QueueDescription.ForwardTo] eller [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] på [egenskaperna QueueDescription][QueueDescription] eller [SubscriptionDescription][SubscriptionDescription] för källan, som i följande exempel:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Målentiteten måste finnas vid den tidpunkt då källentiteten skapas. Om målentiteten inte finns returnerar Service Bus ett undantag när du uppmanas att skapa källentiteten.

Du kan använda automatisk vidarebefordran för att skala ut ett enskilt ämne. Service Bus begränsar [antalet prenumerationer på ett visst ämne](service-bus-quotas.md) till 2 000. Du kan hantera ytterligare prenumerationer genom att skapa ämnen på andra nivån. Även om du inte är bunden av Service Bus-begränsningen för antalet prenumerationer kan du förbättra det övergripande dataflödet för ditt ämne genom att lägga till en andra nivå av ämnen.

![Scenario för automatisk vidarebefordran][0]

Du kan också använda automatisk vidarebefordran för att frikoppla meddelandeavsändare från mottagare. Tänk dig till exempel ett affärssystem som består av tre moduler: orderbearbetning, lagerhantering och hantering av kundrelationer. Var och en av dessa moduler genererar meddelanden som är incheckuerade i ett motsvarande ämne. Alice och Bob är säljare som är intresserade av alla meddelanden som relaterar till sina kunder. För att ta emot dessa meddelanden skapar Alice och Bob var och en en personlig kö och en prenumeration på vart och ett av affärssystemavsnitten som automatiskt vidarebefordrar alla meddelanden till sin kö.

![Scenario för automatisk vidarebefordran][1]

Om Alice går på semester, hennes personliga kö, snarare än ERP-ämnet, fylls upp. I det här fallet, eftersom en säljare inte har fått några meddelanden, ingen av ERP ämnen någonsin nå kvot.

> [!NOTE]
> När automatisk vidarebefordran är inställd ställs värdet för AutoDeleteOnIdle på **både Källan och målet** automatiskt till det maximala värdet för datatypen.
> 
>   - På källsidan fungerar automatisk vidarebefordran som en mottagningsåtgärd. Så källan som har autoforwarding setup är aldrig riktigt "inaktiv".
>   - På målsidan görs detta för att säkerställa att det alltid finns en destination att vidarebefordra meddelandet till.

## <a name="autoforwarding-considerations"></a>Överväganden för automatisk vidarebefordran

Om målentiteten ackumulerar för många meddelanden och överskrider kvoten, eller om målentiteten är inaktiverad, lägger källentiteten till i kön för [obeställbara meddelanden](service-bus-dead-letter-queues.md) tills det finns utrymme i målet (eller omaktiveringen). Dessa meddelanden fortsätter att finnas kvar i kön för obeställbara meddelanden, så du måste uttryckligen ta emot och bearbeta dem från kön för obeställbara meddelanden.

När du kedjar ihop enskilda ämnen för att få ett sammansatt ämne med många prenumerationer rekommenderar vi att du har ett måttligt antal prenumerationer på det första ämnet och många prenumerationer på ämnena på andra nivån. Ett ämne på första nivån med 20 prenumerationer, var och en av dem kedjade till ett ämne på andra nivån med 200 prenumerationer, möjliggör till exempel högre dataflöde än ett ämne på första nivån med 200 prenumerationer, var och en kedjad till ett ämne på andra nivån med 20 prenumerationer.

Service Bus fakturerar en åtgärd för varje vidarebefordrat meddelande. Att till exempel skicka ett meddelande till ett ämne med 20 prenumerationer, var och en av dem som konfigurerats för att vidarebefordra meddelanden automatiskt till en annan kö eller ett annat ämne, faktureras som 21 åtgärder om alla prenumerationer på första nivån får en kopia av meddelandet.

Om du vill skapa en prenumeration som är fastkedjad vid en annan kö eller ett annat ämne måste prenumerationsskaparen ha **hantera** behörigheter för både källan och målentiteten. Att skicka meddelanden till källämnet kräver endast **skicka** behörigheter för källämnet.

## <a name="next-steps"></a>Nästa steg

Mer information om automatisk vidarebefordran finns i följande referensavsnitt:

* [FramåtTill][QueueDescription.ForwardTo]
* [KöBeskrivning][QueueDescription]
* [PrenumerationBeskrivning][SubscriptionDescription]

Mer information om prestandaförbättringar för Service Bus finns i 

* [Bra metoder för att öka prestanda med hjälp av meddelanden i Service Bus](service-bus-performance-improvements.md)
* [Partitionerade meddelandeenheter][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
