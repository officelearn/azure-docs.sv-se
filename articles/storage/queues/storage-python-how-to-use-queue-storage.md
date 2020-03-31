---
title: Så här använder du Azure Queue storage v2.1 från Python - Azure Storage
description: Lär dig hur du använder Azure Queue-tjänsten v2.1 från Python för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: ca0831fd7554058d21e315b67d6965579af1d38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060922"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Så här använder du Azure Queue storage v2.1 från Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Den här artikeln visar vanliga scenarier med hjälp av Azure Queue storage-tjänsten. Scenarierna omfattar infoga, granska, hämta och ta bort kömeddelanden samt skapa och ta bort köer.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt

Exemplen i den här artikeln är skrivna i Python och använder [Microsoft Azure Storage SDK för Python]. Mer information om köer finns i avsnittet [Nästa steg.](#next-steps)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Ladda ned och installera Azure Storage SDK för Python

[Azure Storage SDK för Python](https://github.com/azure/azure-storage-python) kräver Python version 2.7, 3.3 eller senare.
 
### <a name="install-via-pypi"></a>Installera via PyPi

Om du vill installera via Python Package Index (PyPI) skriver du:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för Python version 0.36 eller `pip uninstall azure-storage` tidigare avinstallerar du den äldre SDK-datorn innan du installerar det senaste paketet.

Alternativa installationsmetoder finns i [Azure Storage SDK för Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Visa exempelprogrammet

Information om hur du visar och kör ett exempelprogram som visar hur du använder Python med Azure-köer finns i [Azure Storage: Komma igång med Azure-köer i Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Om du vill köra exempelprogrammet kontrollerar `azure-storage-queue` `azure-storage-common` du att du har installerat både paketen och paketen.

## <a name="create-a-queue"></a>Skapa en kö

Med [QueueService-objektet](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) kan du arbeta med köer. Följande kod skapar `QueueService` ett objekt. Lägg till följande längst upp i en Python-fil där du vill programmatiskt komma åt Azure Storage:

```python
from azure.storage.queue import QueueService
```

Följande kod skapar `QueueService` ett objekt med hjälp av lagringskontots namn och kontonyckel. Ersätt *myaccount* och *mykey* med ditt kontonamn och nyckel.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Infoga ett meddelande i en kö

Om du vill infoga ett meddelande i en kö använder du [metoden put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) för att skapa ett nytt meddelande och lägga till det i kön.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure-kömeddelanden lagras som text. Om du vill lagra binära data konfigurerar du Base64-kodnings- och avkodningsfunktioner i kötjänstobjektet innan du placerar ett meddelande i kön.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>En titt på nästa meddelande

Du kan granska meddelandet framför en kö utan att ta bort det från kön genom att anropa [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) metoden. Som standard `peek_messages` tittar du på ett enda meddelande.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Avköa meddelanden

Koden tar bort ett meddelande från en kö i två steg. När du ringer [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)får du som standard nästa meddelande i en kö. Ett meddelande som `get_messages` returneras från blir osynligt för alla andra kodläsningsmeddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill ta bort meddelandet från kön måste du också ringa [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att när koden inte bearbetar ett meddelande på grund av maskinvaru- eller programvarufel kan en annan instans av koden få samma meddelande och försöka igen. Koden ringer `delete_message` direkt efter att meddelandet har bearbetats.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kodexempel används `get_messages` metoden för att hämta 16 meddelanden i ett samtal. Sedan bearbetar varje meddelande med hjälp av en för-loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett meddelande i kön

Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. Koden nedan använder [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) metoden för att uppdatera ett meddelande. Tidsgränsen för synlighet är inställd på 0, vilket innebär att meddelandet visas omedelbart och innehållet uppdateras.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Hämta kölängden

Du kan hämta en uppskattning av antalet meddelanden i en kö. Metoden [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) ber kötjänsten att returnera metadata om kön `approximate_message_count`och . Resultatet är bara ungefärligt eftersom meddelanden kan läggas till eller tas bort när kötjänsten svarar på din begäran.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Ta bort en kö

Om du vill ta bort en kö och alla meddelanden i den anropar du [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) metoden.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i kölagring följer du de här länkarna för att lära dig mer.

* [Azure-köer Python API-referens](/python/api/azure-storage-queue)
* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK för Python]: https://github.com/Azure/azure-storage-python
