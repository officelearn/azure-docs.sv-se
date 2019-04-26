---
title: Skicka eller ta emot händelser med hjälp av Python – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genomgång för att skapa ett Python-program som skickar händelser till Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 6a8f0ddcfe6de904219059c6e761ead4c004732d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461458"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Skicka händelser till eller ta emot händelser från Event Hubs med hjälp av Python

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudien beskrivs hur du skapar Python-program att skicka händelser till eller ta emot händelser från en event hub. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Python 3.4 och senare.
- Använd den [Azure-portalen](https://portal.azure.com) att skapa ett namnområde av typen Event Hubs och hämta autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan värdet för åtkomstnyckeln för event hub genom att följa instruktionerna från artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder åtkomstnyckeln i koden du skriver senare i den här självstudien. Standard nyckelnamnet är: **RootManageSharedAccessKey**.

## <a name="install-python-package"></a>Installera Python-paketet

Öppna en kommandotolk med Python i dess sökväg för att installera Python-paketet för Event Hubs, och sedan köra det här kommandot: 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>Skicka händelser

### <a name="create-a-python-script-to-send-events"></a>Skapa ett Python-skript för att skicka händelser

Skapa sedan ett Python-program som skickar händelser till en händelsehubb:

1. Öppna din favoritredigerare i Python som [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa ett skript som heter **send.py**. Det här skriptet skickar 100 händelser till din event hub.
3. Klistra in följande kod i send.py, där du ersätter värdena adress-, ANVÄNDAR- och NYCKELN med de värden som du fick från Azure-portalen i föregående avsnitt: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

try:
    if not ADDRESS:
        raise ValueError("No EventHubs URL supplied.")

    # Create Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    sender = client.add_sender(partition="0")
    client.run()
    try:
        start_time = time.time()
        for i in range(100):
            print("Sending message: {}".format(i))
            sender.send(EventData(str(i)))
    except:
        raise
    finally:
        end_time = time.time()
        client.stop()
        run_time = end_time - start_time
        logger.info("Runtime: {} seconds".format(run_time))

except KeyboardInterrupt:
    pass
```

### <a name="run-application-to-send-events"></a>Kör programmet för att skicka händelser

Öppna en kommandotolk med Python i dess sökväg för att köra skriptet och kör sedan det här kommandot:

```bash
start python send.py
```

Grattis! Du har nu skickat meddelanden till en händelsehubb.

## <a name="receive-events"></a>Ta emot händelser

### <a name="create-a-python-script-to-receive-events"></a>Skapa en pythonskript för att ta emot händelser

Skapa sedan ett Python-program som tar emot händelser från en händelsehubb:

1. Öppna din favoritredigerare i Python som [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa ett skript som heter **recv.py**.
3. Klistra in följande kod i recv.py, där du ersätter värdena adress-, ANVÄNDAR- och NYCKELN med de värden som du fick från Azure-portalen i föregående avsnitt: 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
        total += 1

    end_time = time.time()
    client.stop()
    run_time = end_time - start_time
    print("Received {} messages in {} seconds".format(total, run_time))

except KeyboardInterrupt:
    pass
finally:
    client.stop()
```

### <a name="receive-events"></a>Ta emot händelser

Öppna en kommandotolk med Python i dess sökväg för att köra skriptet och kör sedan det här kommandot:

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktionerna och terminologin i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

