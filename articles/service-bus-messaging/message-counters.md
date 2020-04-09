---
title: Azure Service Bus - antal meddelanden
description: Hämta antalet meddelanden som finns i köer och prenumerationer med hjälp av Azure Resource Manager och Azure Service Bus NamespaceManager API:er.
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
ms.date: 04/08/2020
ms.author: aschhab
ms.openlocfilehash: 8020b12ca892fbf7dec6fed6259526d958fb110f
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891772"
---
# <a name="message-counters"></a>Meddelanderäknare

Du kan hämta antalet meddelanden som finns i köer och prenumerationer med hjälp av Azure Resource Manager och Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API:er i .NET Framework SDK.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Med PowerShell kan du få räkningen enligt följande:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Information om antal meddelanden

Att känna till antalet aktiva meddelanden är användbart för att avgöra om en kö bygger upp en eftersläpning som kräver mer resurser för att bearbeta än vad som för närvarande har distribuerats. Följande räknardetaljer finns i klassen [MessageCountDetails:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails)

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): Meddelanden i kön eller prenumerationen som är i aktivt tillstånd och redo för leverans.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Meddelanden i kön för obeställbara brev.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Meddelanden i det schemalagda tillståndet.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): Meddelanden som misslyckades med överföringen till en annan kö eller ett annat ämne och har flyttats till kön för att skicka obeställbara meddelanden.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Meddelanden som väntar på överföring till en annan kö eller ett annat ämne.

Om ett program vill skala resurser baserat på köns längd bör det göra det med en uppmätt takt. Förvärv av meddelanderäknare är en dyr åtgärd inuti meddelandemäklaren och kör den ofta direkt och negativt påverkar entitetens prestanda.

> [!NOTE]
> De meddelanden som skickas till ett Service Bus-ämne vidarebefordras till prenumerationer för det avsnittet. Så är det aktiva meddelandet räkna med ämnet i sig 0, eftersom dessa meddelanden har överförts till prenumerationen. Hämta antalet meddelanden vid prenumerationen och kontrollera att det är större än 0. Även om du ser meddelanden på prenumerationen lagras de faktiskt i ett lagringsutrymme som ägs av ämnet. 

Om du tittar på prenumerationerna, då de skulle ha icke-noll meddelande antal (som lägger till upp till 323 MB utrymme för hela den här entiteten).

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
