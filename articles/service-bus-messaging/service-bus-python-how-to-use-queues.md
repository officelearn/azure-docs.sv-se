---
title: Hur du använder Azure Service Bus-köer med Python | Microsoft Docs
description: Lär dig hur du använder Azure Service Bus-köer från Python.
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: aa0f243f4a5bc3d84c580b950bcf0ed7a78362e7
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312492"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Hur du använder Service Bus-köer med Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Den här artikeln beskriver hur du använder Service Bus-köer. Exemplen är skrivna i Python och Använd den [Python Azure Service Bus-paketet][Python Azure Service Bus package]. Scenarier som tas upp inkluderar **skapa köer, skicka och ta emot meddelanden**, och **tar bort köer**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

> [!NOTE]
> Installera Python eller [Python Azure Service Bus-paketet][Python Azure Service Bus package], finns det [Python installationsguiden](../python-how-to-install.md).
> 
> 

## <a name="create-a-queue"></a>Skapa en kö
Den **ServiceBusService** objekt kan du arbeta med köer. Lägg till följande kod i den övre delen av Python-fil som du vill komma åt Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Queue
```

Följande kod skapar en **ServiceBusService** objekt. Ersätt `mynamespace`, `sharedaccesskeyname`, och `sharedaccesskey` med namnområdet, delad åtkomst (SAS)-signaturen nyckelnamn och värde.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Värdena för SAS-nyckelnamnet och värdet finns i den [Azure-portalen] [ Azure portal] anslutningsinformationen, eller i Visual Studio **egenskaper** fönstret när du väljer Service Bus-namnrymd i Server Explorer (som visas i föregående avsnitt).

```python
bus_service.create_queue('taskqueue')
```

Den `create_queue` metoden stöder också ytterligare alternativ som gör att du kan åsidosätta standardinställningar för kön, till exempel tid till live (TTL) eller största köstorlek. I följande exempel anger den maximala storleken till 5 GB och TTL-värdet till 1 minut:

```python
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Att skicka ett meddelande till en Service Bus-kö, program-anrop i `send_queue_message` -metoden i den **ServiceBusService** objekt.

I följande exempel visar hur du skickar ett testmeddelande till kön med namnet `taskqueue` med `send_queue_message`:

```python
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Mer information om kvoter finns [Service Bus-kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Meddelanden tas emot från en kö med hjälp av den `receive_queue_message` -metoden i den **ServiceBusService** objekt:

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Meddelanden tas bort från kön när de läses när parametern `peek_lock` är inställd på **FALSKT**. Du kan läsa (titt) och låsa meddelandet utan att ta bort det från kön genom att ange parametern `peek_lock` till **SANT**.

Beteendet för läsning och ta bort meddelandet som en del av receive-åtgärden är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, och sedan när programmet startas om och börjar förbruka meddelanden igen, att ha missat meddelandet som förbrukades innan kraschen.

Om den `peek_lock` parametern anges till **SANT**, inleveransen en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att ett meddelande saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), Slutför det andra steget i processen genom att anropa den **ta bort** -metoden i den **meddelandet** objekt. Den **ta bort** metoden att markera meddelandet som Förbrukat och tas bort från kön.

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram går inte att bearbeta meddelandet av någon anledning, så kan det anropa den **låsa upp** -metoden i den **meddelandet** objekt. Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerad med ett meddelande som ligger låst i kön och om programmet misslyckas med att bearbeta meddelandet innan timeout för lås upphör att gälla (t.ex. Om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och gör det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan det **ta bort** metoden anropas sedan meddelandet att levereras till programmet när den startas om. Det här kallas ofta **At Least Once Processing**, det vill säga, varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av den **MessageId** för meddelandet, som förblir konstant under alla leveransförsök.

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus-köer finns i följande artiklar om du vill veta mer.

* [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

