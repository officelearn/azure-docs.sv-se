---
title: Automatisk vidarebefordring Azure Service Bus meddelandeentiteter | Microsoft Docs
description: "Hur du kopplar en Service Bus-kö eller en prenumeration på en annan kö eller ett ämne."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: be23d919b0c96d6c9b96ee328d1b18ad978a9dcc
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Länkning Service Bus-entiteter med automatisk vidarebefordring

Service Bus *automatisk vidarebefordring* funktionen kan du kopplar en kö eller en prenumeration på en annan kö eller ett ämne som ingår i samma namnområde. När automatisk vidarebefordring är aktiverat, tar bort meddelanden som placerats i den första kön eller prenumeration (källa) automatiskt i Service Bus och placerar dem i den andra kö eller ett ämne (mål). Observera att det är fortfarande möjligt att skicka ett meddelande direkt till enheten som mål. Du är inte möjligt att en underkö, till exempel en obeställbara meddelanden till en annan kö eller ett ämne.

## <a name="using-auto-forwarding"></a>Med hjälp av automatisk vidarebefordring

Du kan aktivera automatisk vidarebefordring genom att ange den [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] eller [SubscriptionDescription.ForwardTo] [ SubscriptionDescription.ForwardTo] Egenskaper för den [QueueDescription] [ QueueDescription] eller [SubscriptionDescription] [ SubscriptionDescription] objekt för datakällan, som i den följande exempel:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Entiteten målet måste finnas samtidigt källentiteten har skapats. Om mål-entiteten inte finns, returnerar ett undantag när du uppmanas att skapa källentiteten Service Bus.

Du kan använda automatisk vidarebefordring för att skala ut enskilda avsnitt. Service Bus-gränser i [antalet prenumerationer på ett visst ämne](service-bus-quotas.md) till 2 000. Du kan hantera ytterligare prenumerationer genom att skapa andra nivån avsnitt. Även om du inte är bunden av Service Bus-begränsning för antalet prenumerationer, kan lägga till en andra nivå av ämnen förbättra det totala genomflödet i ditt ämne.

![Automatisk vidarebefordring scenario][0]

Du kan också använda automatisk vidarebefordring för att frikoppla avsändare från mottagare. Tänk dig ett ERP-system som består av tre moduler: ordna bearbetning, lager och kunden relationer hantering. Var och en av dessa moduler genererar meddelanden i kö till en motsvarande ämne. Alice och Bob är säljare som är intresserade av alla meddelanden som relaterar till sina kunder. För att ta emot dessa meddelanden skapar Alice och Bob du en personlig kö och en prenumeration på varje ERP-avsnitt som automatiskt vidarebefordrar alla meddelanden till deras köomfång.

![Automatisk vidarebefordring scenario][1]

Om Alice går på semester, sitt personliga kön i stället ERP-avsnittet, fylls. I detta scenario eftersom en säljare inte har fått några meddelanden nå ingen ERP-avsnitt någonsin kvoten.

## <a name="auto-forwarding-considerations"></a>Automatisk vidarebefordring överväganden

Om mål-entiteten ackumulerar för många meddelanden och överskrider kvoten eller mål-entiteten är inaktiverad, källentiteten lägger till meddelandena till dess [kö med olevererade brev](service-bus-dead-letter-queues.md) tills det finns utrymme i målet (eller entiteten aktiveras på nytt). Dessa meddelandena ska behållas i kö med olevererade brev, så du måste uttryckligen får och behandlar dem från kö för obeställbara meddelanden.

När länkning ihop enskilda avsnitt för att erhålla ett sammansatt ämne med många prenumerationer, rekommenderas att du har ett Måttligt antal prenumerationer på första nivån avsnittet och många prenumerationer på andra nivån-avsnitt. Till exempel kan en första nivån avsnittet med 20 prenumerationer, var och en av dem att härleda till ett andra nivån avsnitt med 200 prenumerationer ger bättre genomströmning än en första nivån avsnittet med 200 prenumerationer varje sammankedjade till en andra nivå avsnittet med 20 prenumerationer.

Service Bus debiterar en åtgärd för varje vidarebefordrade meddelandet. Till exempel faktureras ett meddelande skickas till ett ämne med 20 prenumerationer, var och en av dem har konfigurerats för att automatiskt vidarebefordra meddelanden till en annan kö eller ett ämne, som 21 funktioner om alla prenumerationer på första nivån får en kopia av meddelandet.

Om du vill skapa en prenumeration som går att härleda till en annan kö eller ett ämne, skapare av prenumerationen måste ha **hantera** behörigheter på både käll- och mål-entiteten. Skicka meddelanden till avsnittet källa kräver endast **skicka** behörigheter på käll-avsnittet.

## <a name="next-steps"></a>Nästa steg

Detaljerad information om automatisk vidarebefordring finns i följande referensavsnitt:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Läs mer om Service Bus prestandaförbättringar i 

* [Metodtips för bättre prestanda med hjälp av Service Bus-meddelanden](service-bus-performance-improvements.md)
* [Partitionerade meddelandeentiteter][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
