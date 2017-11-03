---
title: Antalet med Azure Service Bus-meddelanden | Microsoft Docs
description: "Hämta antalet Azure Service Bus-meddelanden."
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: sethm
ms.openlocfilehash: f5d2aa551bbe77a66459907cf5cd1313bb907981
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="message-counters"></a>Meddelanderäknare

Du kan hämta antalet meddelanden som lagras i köer och -prenumerationer med hjälp av Azure Resource Manager och Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API: er i .NET Framework SDK.

Med PowerShell kan hämta du antalet på följande sätt:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Antal meddelandeinformation

Att veta antalet aktiva meddelanden är användbar för att avgöra om en kö skapar en eftersläpning som kräver mer resurser för att behandla än vad som har distribuerats. Följande information om prestandaräknare är tillgängliga i den [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) klass:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): meddelanden i kön eller prenumerationen som är i aktivt tillstånd och redo för leverans.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): meddelanden i kö för obeställbara meddelanden.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): meddelanden i schemalagda tillstånd.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): meddelanden som har misslyckats överföring till en annan kö eller ett ämne och har flyttats till överföringen obeställbara meddelanden.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): väntande överföring till en annan kö eller ett ämne meddelanden.

Om ett program vill skala resurser baserat på längden på kön, det bör göra det med en mycket uppmätta takt. Förvärv av räknarna visas är en kostsam åtgärd i meddelandet Service broker och kör den ofta direkt och negativt påverkar prestanda för entiteten.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)