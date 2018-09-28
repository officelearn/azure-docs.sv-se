---
title: Antal för Azure Service Bus-meddelanden | Microsoft Docs
description: Hämta antalet Azure Service Bus-meddelanden.
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
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: bd9d1b813c90be09301eeff1de7580f22c15500b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407594"
---
# <a name="message-counters"></a>Meddelanderäknare

Du kan hämta antal meddelanden som ligger i köer och prenumerationer med hjälp av Azure Resource Manager och Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API: er i .NET Framework SDK.

Med PowerShell kan hämta du antalet på följande sätt:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Information om antal

Att känna till antal aktiva meddelanden är användbar för att fastställa om en kö skapar kvarvarande uppgifter som kräver mer resurser för att behandla än vad för närvarande har distribuerats. Information om följande prestandaräknare är tillgängliga i den [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) klass:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): meddelanden i kö eller prenumeration som är aktiva tillstånd och är redo för leverans.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): meddelanden i kö för obeställbara meddelanden.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): meddelanden i schemalagda tillstånd.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): meddelanden som har misslyckats överföring till en annan kö eller ett ämne och har flyttats till kön för obeställbara överföring.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): meddelanden väntar på överföring till en annan kö eller ämne.

Om ett program vill skala resurser baserat på längden på kön, det bör göra det med en mycket uppmätta takt. Förvärvet av meddelanderäknare är en kostsam åtgärd inuti meddelandekön och det körs ofta direkt och negativt påverkar prestanda för entiteten.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)