---
title: 'Snabb start: använda Azure Service Bus köer med python'
description: Den här artikeln visar hur du använder python för att skapa, skicka meddelanden till och ta emot meddelanden från Azure Service Bus köer.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 55ed71c6947c70ac797656f2f18cf71dd8aaae9d
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852488"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Snabb start: använda Azure Service Bus köer med python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Den här artikeln visar hur du använder python för att skapa, skicka meddelanden till och ta emot meddelanden från Azure Service Bus köer. 

Mer information om python Azure Service Bus-bibliotek finns i [Service Bus bibliotek för python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du kan aktivera dina [förmåner för Visual Studio eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ett Service Bus namn område som skapats genom att följa stegen i [snabb starten: använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer](service-bus-quickstart-topics-subscriptions-portal.md). Kopiera den primära anslutnings strängen från skärmen **principer för delad åtkomst** som du kan använda senare i den här artikeln. 
- Python 3.4 x eller senare, med [python Azure Service Bus][Python Azure Service Bus package] -paketet installerat. Mer information finns i [installations guiden för python](/azure/developer/python/azure-sdk-install). 

## <a name="create-a-queue"></a>Skapa en kö

Med ett **ServiceBusClient** -objekt kan du arbeta med köer. Lägg till följande rad längst upp i python-filen för att program mässigt komma åt Service Bus:

```python
from azure.servicebus import ServiceBusClient
```

Lägg till följande kod för att skapa ett **ServiceBusClient** -objekt. Ersätt `<connectionstring>` med ditt Service Bus primärt värde för anslutnings sträng. Du hittar det här värdet under **principer för delad åtkomst** i Service Bus namn området i [Azure Portal][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

I följande kod används `create_queue` **ServiceBusClient** -metoden för att skapa en kö med namnet `taskqueue` med standardinställningar:

```python
sb_client.create_queue("taskqueue")
```

Du kan använda alternativen för att åsidosätta standardinställningar för kön, till exempel TTL (Time to Live) eller maximal ämnes storlek. Följande kod skapar en kö med namnet `taskqueue` med en maximal kös Tor lek på 5 GB och TTL-värde på 1 minut:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö

Om du vill skicka ett meddelande till en Service Bus kö anropar ett program `send` metoden i **ServiceBusClient** -objektet. Följande kod exempel skapar en Queue-klient och skickar ett test meddelande till `taskqueue` kön. Ersätt `<connectionstring>` med ditt Service Bus primärt värde för anslutnings sträng. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Storleks gränser och kvoter för meddelanden

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som en kö kan innehålla, men det finns ett tak för den totala storleken på de meddelanden som kön innehåller. Du kan definiera kös Tor lek vid skapande tillfället med en övre gräns på 5 GB. 

Mer information om kvoter finns i [Service Bus kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Kön klienten tar emot meddelanden från en kö med hjälp av `get_receiver` metoden på **ServiceBusClient** -objektet. Följande kod exempel skapar en Queue-klient och tar emot ett meddelande från `taskqueue` kön. Ersätt `<connectionstring>` med ditt Service Bus primärt värde för anslutnings sträng. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>Använd peek_lock parameter

Den valfria `peek_lock` parametern för `get_receiver` avgör om Service Bus tar bort meddelanden från kön när de läses. Standard läget för att ta emot meddelanden är *PeekLock*, eller `peek_lock` anges till **True**, som läser (tittar) och låser meddelanden utan att ta bort dem från kön. Varje meddelande måste slutföras explicit för att ta bort det från kön.

Om du vill ta bort meddelanden från kön när de har lästs, kan du ange `peek_lock` parametern `get_receiver` till **false**. Att ta bort meddelanden som en del av Receive-åtgärden är den enklaste modellen, men fungerar bara om programmet kan tolerera saknade meddelanden om det uppstår ett fel. För att förstå det här beteendet bör du överväga ett scenario där konsumenten utfärdar en Receive-begäran och sedan kraschar innan den bearbetas. Om meddelandet togs bort när det togs emot när programmet startas om och börjar förbruka meddelanden igen, har det missa det meddelande som togs emot före kraschen.

Om programmet inte kan tolerera missade meddelanden, kan du ta emot en åtgärd i två steg. PeekLock söker efter nästa meddelande som ska förbrukas, låser det för att hindra andra användare från att ta emot det och returnerar det till programmet. När du har bearbetat eller lagrat meddelandet Slutför programmet det andra steget i Receive-processen genom att anropa `complete` metoden i objektet **Message** .  `complete`Metoden markerar meddelandet som förbrukat och tar bort det från kön.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Hantera program krascher och oläsbara meddelanden

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta ett meddelande av någon anledning, kan det anropa `unlock` metoden i objektet **Message** . Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen av samma eller ett annat användnings program.

Det finns också en tids gräns för meddelanden som är låsta i kön. Om ett program inte kan bearbeta ett meddelande innan låsnings tids gränsen går ut, till exempel om programmet kraschar, Service Bus låser upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

Om ett program kraschar efter att ett meddelande har bearbetats men innan du anropar `complete` -metoden, skickas meddelandet vidare till programmet när det startas om. Det här beteendet kallas ofta *för bearbetning med minst en gång*. Varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om ditt scenario inte kan tolerera dubbla bearbetningar kan du använda meddelandets **messageid** -egenskap, som förblir konstant över leverans försök, för att hantera dubbla meddelande leveranser. 

> [!TIP]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Med Service Bus Explorer kan du ansluta till ett Service Bus-namnområde och enkelt administrera meddelande enheter. Verktyget innehåller avancerade funktioner som import/export-funktioner och möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i Service Bus köer, se [köer, ämnen och prenumerationer][Queues, topics, and subscriptions] för att lära dig mer.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
