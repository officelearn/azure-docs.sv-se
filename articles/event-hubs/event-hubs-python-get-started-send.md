---
title: Skicka eller ta emot händelser från Azure Event Hubs med Python (gammal)
description: Den här genomgången visar hur du skapar och kör Python-skript som skickar händelser till eller ta emot händelser från Azure Event Hubs med det gamla azure-eventhub version 1-paketet.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162607"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Snabbstart: Skicka och ta emot händelser med eventhubbar med Python (azure-eventhub version 1)
Den här snabbstarten visar hur du skickar händelser till och ta emot händelser från en händelsehubb med **azure-eventhub version 1 Python-paketet.** 

> [!WARNING]
> Den här snabbstarten använder det gamla azure-eventhub version 1-paketet. En snabbstart som använder den **senaste versionen 5** av paketet finns i [Skicka och ta emot händelser med azure-eventhub version 5](get-started-python-send-v2.md). Information om hur du flyttar programmet från att använda det gamla paketet till ett nytt finns i [guiden för att migrera från azure-eventhub version 1 till version 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Krav
Om du inte har gjort det tidigare i Azure Event Hubs läser du [översikt över eventhubbar](event-hubs-about.md) innan du gör den här snabbstarten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure-prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/) eller använda dina msdn-prenumerationsförmåner när du [skapar ett konto](https://azure.microsoft.com).
- Python 3.4 eller `pip` senare, med installerat och uppdaterat.
- Python-paketet för eventhubbar. Om du vill installera paketet kör du det här kommandot i en kommandotolk som har Python i sökvägen: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Skapa ett namnområde för händelsehubbar och en händelsehubb**. Det första steget är att använda [Azure-portalen](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs och hämta de hanteringsautentiseringsuppgifter som ditt program behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan värdet för åtkomstnyckeln för händelsehubben genom att följa instruktionerna från artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder åtkomstnyckeln i koden du skriver senare i den här snabbstarten. Standardnyckelnamnet är: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Skicka händelser

Så här skapar du ett Python-program som skickar händelser till en händelsehubb:

> [!NOTE]
> I stället för att arbeta igenom snabbstarten kan du hämta och köra [exempelapparna](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) från GitHub. Ersätt `EventHubConnectionString` strängarna och `EventHubName` med dina händelsenavvärden.

1. Öppna din favorith python-redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en ny fil som heter *send.py*. Skriptet skickar 100 händelser till din händelsehubb.
3. Klistra in följande kod i *send.py*, ersätta \<namnområdet Event Hubs \<>, eventhub \<>, AccessKeyName> \<och primärnyckelvärde> med dina värden: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
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
               message = "Message {}".format(i)
               sender.send(EventData(message))
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
   
4. Spara filen. 

Om du vill köra skriptet, från katalogen där du sparade *send.py,* kör du det här kommandot:

```cmd
start python send.py
```

Grattis! Du har nu skickat meddelanden till en händelsehubb.

## <a name="receive-events"></a>Ta emot händelser

Så här skapar du ett Python-program som tar emot händelser från en händelsehubb:

1. Skapa en fil som heter *recv.py*i Python-redigeraren .
2. Klistra in följande kod i *recv.py*, \<ersätta namnområdet Event \<Hubs>, \<eventhub>, AccessKeyName \<> och primärnyckel värde> med dina värden: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
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
   
4. Spara filen.

Om du vill köra skriptet, från katalogen där du sparade *recv.py*, kör du det här kommandot:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Nästa steg
Mer information om Event Hubs finns i följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

