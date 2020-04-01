---
title: 'Snabbstart: Använd Azure Service Bus-köer med Python'
description: Den här artikeln visar hur du använder Python för att skapa, skicka meddelanden till och ta emot meddelanden från Azure Service Bus-köer.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: acb0b0e84804ecf6025e05590133dee9b0d54c48
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478653"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Snabbstart: Använd Azure Service Bus-köer med Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Den här artikeln visar hur du använder Python för att skapa, skicka meddelanden till och ta emot meddelanden från Azure Service Bus-köer. 

Mer information om Python Azure Service Bus-biblioteken finns i [Service Bus-bibliotek för Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Krav
- En Azure-prenumeration. Du kan aktivera dina fördelar för [Visual Studio- eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ett servicebussnamnområde som skapats genom att följa stegen i [Snabbstart: Använd Azure-portalen för att skapa ett servicebussämne och prenumerationer](service-bus-quickstart-topics-subscriptions-portal.md). Kopiera den primära **anslutningssträngen** från skärmen Principer för delad åtkomst om du vill använda senare i den här artikeln. 
- Python 3.4x eller högre, med [Python Azure Service Bus-paketet][Python Azure Service Bus package] installerat. Mer information finns i [installationsguiden](/azure/developer/python/azure-sdk-install)för Python . 

## <a name="create-a-queue"></a>Skapa en kö

Med ett **ServiceBusClient-objekt** kan du arbeta med köer. Om du vill komma åt Service Bus programmatiskt lägger du till följande rad högst upp i Python-filen:

```python
from azure.servicebus import ServiceBusClient
```

Lägg till följande kod för att skapa ett **ServiceBusClient-objekt.** Ersätt `<connectionstring>` med ditt primära anslutningssträngvärde för Service Bus. Du hittar det här värdet under **Principer för delad åtkomst** i tjänstbussens namnområde i [Azure-portalen][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

Följande kod använder `create_queue` metoden för **ServiceBusClient** för `taskqueue` att skapa en kö med namnet med standardinställningar:

```python
sb_client.create_queue("taskqueue")
```

Du kan använda alternativ för att åsidosätta standardköinställningar, till exempel meddelandetid att leva (TTL) eller maximal ämnesstorlek. Följande kod skapar en `taskqueue` kö som anropas med en maximal köstorlek på 5 GB och TTL-värde på 1 minut:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö

Om du vill skicka ett meddelande till `send` en servicebusskö anropar ett program metoden på **ServiceBusClient-objektet.** I följande kodexempel skapas en köklient och ett testmeddelande skickas till `taskqueue` kön. Ersätt `<connectionstring>` med ditt primära anslutningssträngvärde för Service Bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Begränsningar och kvoter för meddelandestorlek

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för hur många meddelanden en kö kan innehålla, men det finns ett tak för den totala storleken på de meddelanden som kön har. Du kan definiera köstorlek vid skapande, med en övre gräns på 5 GB. 

Mer information om kvoter finns i [Service Bus-kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Köklienten tar emot meddelanden från `get_receiver` en kö med hjälp av metoden på **TjänstenBusClient-objektet.** I följande kodexempel skapas en köklient och ett meddelande tas emot från `taskqueue` kön. Ersätt `<connectionstring>` med ditt primära anslutningssträngvärde för Service Bus. 

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

### <a name="use-the-peek_lock-parameter"></a>Använda parametern peek_lock

Den `peek_lock` valfria `get_receiver` parametern för avgör om Service Bus tar bort meddelanden från kön när de läss. Standardläget för meddelandemottagande är `peek_lock` *PeekLock*eller inställt på **Sant**, som läser (kikar) och låser meddelanden utan att ta bort dem från kön. Varje meddelande måste sedan uttryckligen fyllas i för att ta bort det från kön.

Om du vill ta bort meddelanden från kön när `peek_lock` de `get_receiver` läses kan du ange parametern till **False**. Att ta bort meddelanden som en del av mottagningsåtgärden är den enklaste modellen, men fungerar bara om programmet kan tolerera saknade meddelanden om det finns ett fel. För att förstå detta, överväga ett scenario där konsumenten utfärdar en mottagningsbegäran och sedan kraschar innan du bearbetar den. Om meddelandet togs bort när det togs emot, när programmet startas om och börjar konsumera meddelanden igen, har det missat meddelandet som det tog emot före kraschen.

Om ditt program inte kan tolerera missade meddelanden är mottagning en tvåstegsåtgärd. PeekLock hittar nästa meddelande som ska förbrukas, låser det för att förhindra att andra konsumenter tar emot det och returnerar det till programmet. När meddelandet har bearbetats eller lagrats slutförs den andra `complete` fasen av mottagningsprocessen genom att anropa metoden på **meddelandeobjektet.**  Metoden `complete` markerar meddelandet som förbrukat och tar bort det från kön.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsliga meddelanden

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram av någon anledning inte kan `unlock` bearbeta ett meddelande kan det anropa metoden på **meddelandeobjektet.** Service Bus låser upp meddelandet i kön och gör det tillgängligt för att tas emot igen, antingen av samma eller ett annat tidskrävande program.

Det finns också en timeout för meddelanden som är låsta i kön. Om ett program inte behandlar ett meddelande innan låstidsgränsen går ut, till exempel om programmet kraschar, låser Service Bus upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

Om ett program kraschar efter bearbetning av `complete` ett meddelande men innan du anropar metoden levereras meddelandet till programmet när det startas om. Det här beteendet kallas ofta *Minst en gång Bearbetning*. Varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras på nytt. Om ditt scenario inte kan tolerera dubblettbearbetning kan du använda egenskapen **MessageId** för meddelandet, som förblir konstant över leveransförsök, för att hantera dubblettmeddelandeleverans. 

> [!TIP]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Med Service Bus Explorer kan du ansluta till ett servicebussnamnområde och enkelt administrera meddelandeentiteter. Verktyget innehåller avancerade funktioner som import-/exportfunktioner och möjligheten att testa ämnen, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i servicebussköer läser du [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions] för att lära dig mer.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
