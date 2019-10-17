---
title: Skicka och ta emot händelser med python – Azure Event Hubs
description: Den här genom gången visar hur du skapar och kör Python-skript som skickar händelser till eller tar emot händelser från Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/11/2019
ms.author: shvija
ms.openlocfilehash: 330a7f5dc325c707b5be7ce9f9b3242a1d4c9547
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428896"
---
# <a name="send-and-receive-events-with-event-hubs-using-python"></a>Skicka och ta emot händelser med Event Hubs med python

Azure Event Hubs är en stor data strömnings plattform och händelse inmatnings tjänst som kan ta emot och bearbeta miljon tals händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri från distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. Mer information om Event Hubs finns i [Azure-Event Hubs](event-hubs-about.md) och- [funktioner och terminologi i Azure Event Hubs](event-hubs-features.md).

Den här snabb starten visar hur du skapar python-program som skickar händelser till och tar emot händelser från en händelsehubben. 

> [!NOTE]
> I stället för att arbeta via snabb starten kan du ladda ned och köra [exempel apparna](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) från GitHub. Ersätt `EventHubConnectionString`-och `EventHubName`-strängar med Event Hub-värdena. 

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Ett aktivt Event Hubs-namnområde och händelsehubben som skapats genom att följa anvisningarna i [snabb start: skapa en händelsehubben med Azure Portal](event-hubs-create.md). Anteckna namn området och händelsehubben som ska användas senare i den här genom gången. 
- Namnet på den delade åtkomst nyckeln och värdet för primär nyckel för din Event Hubs-namnrymd. Hämta namnet och värdet för åtkomst nyckeln genom att följa anvisningarna i [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Standard namnet för åtkomst nyckeln är **RootManageSharedAccessKey**. Kopiera nyckel namnet och värdet för primär nyckel som ska användas senare i den här genom gången. 
- Python 3,4 eller senare, med `pip` installerat och uppdaterat.
- Python-paketet för Event Hubs. Installera paketet genom att köra det här kommandot i en kommando tolk med python i sökvägen: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > Koden i den här snabb starten använder den aktuella stabila versionen av 1.3.1 för Event Hubs SDK. Exempel kod som använder för hands versionen av SDK finns i [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Skicka händelser

Så här skapar du ett python-program som skickar händelser till en Event Hub:

1. Öppna din favorit-eller python-redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en ny fil med namnet *send.py*. Det här skriptet skickar 100-händelser till händelsehubben.
3. Klistra in följande kod i *send.py*, och ersätt Event Hubs \<namespace >, \<eventhub >, \<AccessKeyName > och \<primary nyckel värde > med dina värden: 
   
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

Kör skriptet från katalogen där du sparade *send.py*genom att köra det här kommandot:

```cmd
start python send.py
```

Gratulerar! Du har nu skickat meddelanden till en händelsehubb.

## <a name="receive-events"></a>Ta emot händelser

Så här skapar du ett python-program som tar emot händelser från en Event Hub:

1. Skapa en fil med namnet *Recv.py*i python-redigeraren.
2. Klistra in följande kod i *Recv.py*, och ersätt Event Hubs \<namespace >, \<eventhub >, \<AccessKeyName > och \<primary nyckel värde > med dina värden: 
   
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

Kör skriptet från katalogen där du sparade *Recv.py*genom att köra det här kommandot:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Nästa steg
Mer information om Event Hubs finns i följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

