---
title: Skicka eller ta emot händelser från Azure Event Hubs med python (gammal)
description: Den här genom gången visar hur du skapar och kör Python-skript som skickar händelser till eller tar emot händelser från Azure Event Hubs med hjälp av det gamla Azure-eventhub version 1-paketet.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77162607"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Snabb start: skicka och ta emot händelser med Event Hubs med python (Azure-eventhub version 1)
Den här snabb starten visar hur du skickar händelser till och tar emot händelser från en händelsehubben med **Azure-eventhub version 1** python-paketet. 

> [!WARNING]
> Den här snabb starten använder det gamla Azure-eventhub version 1-paketet. En snabb start som använder den senaste **versionen 5** av paketet finns i [skicka och ta emot händelser med Azure-eventhub version 5](get-started-python-send-v2.md). Information om hur du flyttar ditt program från att använda det gamla paketet till ett nytt finns i [guiden för att migrera från Azure-eventhub version 1 till version 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Krav
Om du är nybörjare på Azure Event Hubs, se [Event Hubs översikt](event-hubs-about.md) innan du gör den här snabb starten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/free/) eller använda dina förmåner för MSDN-prenumeranter när du [skapar ett konto](https://azure.microsoft.com).
- Python 3,4 eller senare, med `pip` installerad och uppdaterad.
- Python-paketet för Event Hubs. Installera paketet genom att köra det här kommandot i en kommando tolk med python i sökvägen: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan värdet för åtkomst nyckeln för händelsehubben genom att följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder åtkomst nyckeln i koden som du skriver senare i den här snabb starten. Standard nyckel namnet är: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Skicka händelser

Så här skapar du ett python-program som skickar händelser till en Event Hub:

> [!NOTE]
> I stället för att arbeta via snabb starten kan du ladda ned och köra [exempel apparna](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) från GitHub. Ersätt- `EventHubConnectionString` och `EventHubName` -strängarna med dina Event Hub-värden.

1. Öppna din favorit-eller python-redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en ny fil med namnet *send.py*. Det här skriptet skickar 100-händelser till händelsehubben.
3. Klistra in följande kod i *send.py* \<, Ersätt Event Hubs namn områdes> \<, eventhub- \<>, AccessKeyName> \<och primär nyckel värde> med dina värden: 
   
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

Grattis! Du har nu skickat meddelanden till en händelsehubb.

## <a name="receive-events"></a>Ta emot händelser

Så här skapar du ett python-program som tar emot händelser från en Event Hub:

1. Skapa en fil med namnet *Recv.py*i python-redigeraren.
2. Klistra in följande kod i *Recv.py* \<, Ersätt Event Hubs namn områdes> \<, eventhub- \<>, AccessKeyName> \<och primär nyckel värde> med dina värden: 
   
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

