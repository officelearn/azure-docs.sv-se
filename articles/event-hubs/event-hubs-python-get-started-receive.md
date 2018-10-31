---
title: Ta emot händelser från Azure Event Hubs med hjälp av Python | Microsoft Docs
description: Börja ta emot händelser från Event Hubs med hjälp av Python
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 9707f0cf5a76c0ac4006b53ca8166d3b42fc9af4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240570"
---
# <a name="receive-events-from-event-hubs-using-python"></a>Ta emot händelser från Event Hubs med hjälp av Python

Azure Event Hubs är en mycket skalbar Händelsehanteringssystem som kan hantera flera miljoner händelser per sekund programmen kan bearbeta och analysera stora datamängder som produceras av anslutna enheter och andra system. När samlats in i en händelsehubb, du kan ta emot och hantera händelser med hjälp av pågående hanterare eller vidarebefordran till andra system för analys av.

Mer information om Händelsehubbar finns i [översikt av Händelsehubbar][Event Hubs overview].

Den här självstudien beskrivs hur du tar emot händelser från en händelsehubb från ett program som skrivits i Python. För att skicka händelser, se [motsvarande Skicka artikel](event-hubs-python-get-started-send.md).

Koden i den här självstudiekursen kommer från [dessa GitHub-exempel](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), som du kan kontrollera för att se fullständiga fungerande program, inklusive importuttryck och variabeldeklarationer. Andra exempel finns i samma mapp i GitHub.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- Python 3.4 och senare.
- En befintlig Event Hubs-namnområde och event hub. Du kan skapa dessa enheter genom att följa instruktionerna i [i den här artikeln](event-hubs-create.md). 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


## <a name="install-python-package"></a>Installera Python-paketet

Öppna en kommandotolk med Python i dess sökväg för att installera Python-paketet för Event Hubs, och sedan köra det här kommandot: 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-receive-events"></a>Skapa en pythonskript för att ta emot händelser

Skapa sedan ett Python-program som tar emot händelser från en händelsehubb:

1. Öppna din favoritredigerare i Python som [Visual Studio Code][Visual Studio Code].
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

## <a name="receive-events"></a>Ta emot händelser

Öppna en kommandotolk med Python i dess sökväg för att köra skriptet och kör sedan det här kommandot:

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du ett Python-program som har fått meddelanden från en event hub. Läs hur du skickar händelser till en event hub med Python i [skicka händelser från event hub - Python](event-hubs-python-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
