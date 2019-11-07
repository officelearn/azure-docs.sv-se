---
title: 'Snabb start: använda Azure Service Bus köer med python'
description: 'Snabb start: Lär dig hur du använder Azure Service Bus köer från python.'
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: 4319299eabb57451e3a25a69196a63094f66ab9b
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721636"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Snabb start: använda Azure Service Bus köer med python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

I den här självstudien får du lära dig hur du skapar python-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö. 

## <a name="prerequisites"></a>Nödvändiga komponenter
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Följ stegen i [Azure Portal för att skapa en Service Bus Queue](service-bus-quickstart-portal.md) -artikel.
    1. Läs snabb **översikten** över Service Bus **köer**. 
    2. Skapa ett Service Bus- **namnområde**. 
    3. Hämta **anslutnings strängen**. 

        > [!NOTE]
        > Du skapar en **kö** i Service Bus namn området genom att använda python i den här självstudien. 
1. Installera python eller [python Azure Service Bus-paketet][Python Azure Service Bus package]finns i [installations guiden för python](/azure/python/python-sdk-azure-install). Läs den fullständiga dokumentationen för Service Bus python SDK [här](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Skapa en kö
Med **ServiceBusClient** -objektet kan du arbeta med köer. Lägg till följande kod nära överkanten av en python-fil som du vill ha åtkomst till Service Bus via programmering:

```python
from azure.servicebus import ServiceBusClient
```

Följande kod skapar ett **ServiceBusClient** -objekt. Ersätt `<CONNECTION STRING>` med din Service Bus-ConnectionString.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Du hittar värdena för SAS-nyckelns namn och värde i [Azure Portal][Azure portal] anslutnings information eller i fönstret **Egenskaper** för Visual Studio när du väljer Service Bus namn området i Server Explorer (se föregående avsnitt).

```python
sb_client.create_queue("taskqueue")
```

`create_queue`-metoden stöder också ytterligare alternativ, vilket gör att du kan åsidosätta standardinställningar för kön, till exempel TTL (Time to Live) eller maximal kös Tor lek. I följande exempel anges maximal kös Tor lek till 5 GB och TTL-värdet till 1 minut:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

Mer information finns i [Azure Service Bus python-dokumentation](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Om du vill skicka ett meddelande till en Service Bus kö anropar ditt program metoden `send` på `ServiceBusClient`-objektet.

Följande exempel visar hur du skickar ett test meddelande till kön med namnet `taskqueue` med `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus kvoter][Service Bus quotas].

Mer information finns i [Azure Service Bus python-dokumentation](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Meddelanden tas emot från en kö med hjälp av metoden `get_receiver` på `ServiceBusService`-objektet:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Mer information finns i [Azure Service Bus python-dokumentation](/python/api/overview/azure/servicebus?view=azure-python).


Meddelanden tas bort från kön när de läses när parametern `peek_lock` har angetts till **false**. Du kan läsa (granska) och låsa meddelandet utan att ta bort det från kön genom att ange parametern `peek_lock` till **Sant**.

Beteendet att läsa och ta bort meddelandet som en del av Receive-åtgärden är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande i händelse av ett fel. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus har markerat meddelandet som förbrukat, och när programmet startas om och börjar förbruka meddelanden igen, kommer det att ha missat meddelandet som förbrukades innan kraschen.

Om `peek_lock`-parametern har angetts till **True**blir mottagningen en åtgärd i två steg, vilket gör det möjligt att stödja program som inte kan tolerera meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det tillförlitligt för framtida bearbetning) slutförs det andra steget i Receive-processen genom att anropa **Delete** -metoden i objektet **Message** . Metoden **Delete** kommer att markera meddelandet som förbrukat och ta bort det från kön.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta meddelandet av någon anledning, kan det anropa metoden **Unlock** på objektet **Message** . Detta leder till att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användnings program eller ett annat användnings program.

Det finns också en tids gräns som är kopplad till ett meddelande som är låst i kön och om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut (till exempel om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och göra det tillgängligt för att tas emot igen.

I händelse av att programmet kraschar när meddelandet har bearbetats men innan **Delete** -metoden anropas, kommer meddelandet att skickas vidare till programmet när det startas om. Detta kallas ofta **minst en gång bearbetning**, det vill säga varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av egenskapen **MessageId** för meddelandet. Detta är och förblir konstant under alla leveransförsök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Service Bus köer kan du läsa mer i de här artiklarna.

* [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

