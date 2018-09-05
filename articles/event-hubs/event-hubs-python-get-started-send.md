---
title: Skicka händelser till Azure Event Hubs med hjälp av Python | Microsoft Docs
description: Kom igång med att skicka händelser till Event Hubs med hjälp av Python
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 762e21cfc7d16b614eb637c569f8bfc5b6115db1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703797"
---
# <a name="send-events-to-event-hubs-using-python"></a>Skicka händelser till Event Hubs med hjälp av Python

Azure Event Hubs är en mycket skalbar Händelsehanteringssystem som kan hantera flera miljoner händelser per sekund programmen kan bearbeta och analysera stora datamängder som produceras av anslutna enheter och andra system. När samlats in i en händelsehubb, du kan ta emot och hantera händelser med hjälp av pågående hanterare eller vidarebefordran till andra system för analys av.

Mer information om Händelsehubbar finns i [översikt av Händelsehubbar][Event Hubs overview].

Den här självstudien beskrivs hur du skickar händelser till en händelsehubb från ett program som skrivits i Python. Om du vill ta emot händelser, se [motsvarande Receive-artikel](event-hubs-python-get-started-receive.md).

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

## <a name="create-a-python-script-to-send-events"></a>Skapa ett Python-skript för att skicka händelser

Skapa sedan ett Python-program som skickar händelser till en händelsehubb:

1. Öppna din favoritredigerare i Python som [Visual Studio Code][Visual Studio Code].
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

## <a name="send-events"></a>Skicka händelser

Öppna en kommandotolk med Python i dess sökväg för att köra skriptet och kör sedan det här kommandot:

```bash
start python send.py
```
 
## <a name="next-steps"></a>Nästa steg

Nu när du har skickat händelser till en event hub med Python, för att ta emot händelser i [motsvarande Receive-artikel](event-hubs-python-get-started-receive.md).

Finns på följande sidor om du vill veta mer om Event Hubs:

* [Översikt av händelsehubbar][Event Hubs overview]
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
