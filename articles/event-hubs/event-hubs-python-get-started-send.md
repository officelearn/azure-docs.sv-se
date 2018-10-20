---
title: Skicka händelser till Azure Event Hubs med hjälp av Python | Microsoft Docs
description: Kom igång med att skicka händelser till Event Hubs med hjälp av Python
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/16/2018
ms.author: sethm
ms.openlocfilehash: bb77ed69ae8f2229cbd62afa545cac9f048689e8
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458011"
---
# <a name="send-events-to-event-hubs-using-python"></a>Skicka händelser till Event Hubs med hjälp av Python

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. Detaljerad översikt över Event Hubs finns i [översikt av Händelsehubbar](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudien beskrivs hur du skickar händelser till en händelsehubb från ett program som skrivits i Python. 

> [!NOTE]
> Du kan hämta den här snabbstarten som ett exempel från den [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), Ersätt `EventHubConnectionString` och `EventHubName` strängarna med värdena för din händelsehubb, och kör den. Du kan också följa stegen i den här kursen och skapa dina egna.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- Python 3.4 och senare.


## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett namnområde för Event Hubs och en händelsehubb
Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [i den här artikeln](event-hubs-create.md), fortsätter sedan enligt följande steg i den här självstudien.

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

## <a name="run-application-to-send-events"></a>Kör programmet för att skicka händelser

Öppna en kommandotolk med Python i dess sökväg för att köra skriptet och kör sedan det här kommandot:

```bash
start python send.py
```

Grattis! Du har nu skickat meddelanden till en händelsehubb.
 
## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skickat meddelanden till en händelsehubb med hjälp av Python. Läs hur du tar emot händelser från en event hub med Python i [ta emot händelser från event hub - Python](event-hubs-python-get-started-receive.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
