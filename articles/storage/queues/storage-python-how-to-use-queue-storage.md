---
title: Så här använder du Azure Queue Storage v 2.1 från python-Azure Storage
description: Lär dig hur du använder Azure Kötjänst v 2.1 från python för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: 6cbaf19c5c4d52a5de1ef1d63ad5465859555810
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465311"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Så här använder du Azure Queue Storage v 2.1 från python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Den här artikeln visar vanliga scenarier med Azure Queue Storage-tjänsten. Scenarierna som beskrivs är att infoga, granska, hämta och ta bort Kömeddelanden, samt skapa och ta bort köer.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt

Exemplen i den här artikeln är skrivna i python och använder [Microsoft Azure Storage SDK för python]. Mer information om köer finns i avsnittet [Nästa steg](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Ladda ned och installera Azure Storage SDK för Python

[Azure Storage SDK för python](https://github.com/azure/azure-storage-python) kräver python version 2,7, 3,3 eller senare.
 
### <a name="install-via-pypi"></a>Installera via PyPi

Om du vill installera via python-paket indexet (PyPI) skriver du:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för python version 0,36 eller tidigare, måste du avinstallera den äldre SDK: n med `pip uninstall azure-storage` innan du installerar det senaste paketet.

Alternativa installations metoder finns i [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Visa exempel programmet

Om du vill visa och köra ett exempel program som visar hur du använder python med Azure-köer, se [Azure Storage: komma igång med Azure-köer i python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Kontrol lera att du har installerat både-och-paketen för att köra exempel programmet `azure-storage-queue` `azure-storage-common` .

## <a name="create-a-queue"></a>Skapa en kö

Med [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) -objektet kan du arbeta med köer. Följande kod skapar ett- `QueueService` objekt. Lägg till följande längst upp i en python-fil som du vill ha program mässigt åtkomst till Azure Storage:

```python
from azure.storage.queue import QueueService
```

Följande kod skapar ett- `QueueService` objekt med hjälp av lagrings kontots namn och konto nyckeln. Ersätt mitt *konto* och *MyKey* med ditt konto namn och din nyckel.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Infoga ett meddelande i en kö

Om du vill infoga ett meddelande i en kö använder du metoden [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) för att skapa ett nytt meddelande och lägga till det i kön.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure Queue-meddelanden lagras som text. Om du vill lagra binära data måste du konfigurera base64-kodning och avkodnings funktioner för objektet Queue Service innan du skickar ett meddelande i kön.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>En titt på nästa meddelande

Du kan titta på meddelandet överst i en kö utan att ta bort det från kön genom att anropa metoden [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) . Som standard `peek_messages` tittar vi på ett enskilt meddelande.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Ta bort meddelanden från kön

Koden tar bort ett meddelande från en kö i två steg. När du anropar [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)får du nästa meddelande i en kö som standard. Ett meddelande som returnerades från `get_messages` blir osynligt för all annan kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Den här två stegs processen för att ta bort ett meddelande säkerställer att när din kod inte kan bearbeta ett meddelande på grund av maskin-eller program varu fel, kan en annan instans av koden Hämta samma meddelande och försöka igen. Dina kod anrop `delete_message` direkt efter att meddelandet har bearbetats.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kod exempel används `get_messages` metoden för att hämta 16 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en for-slinga. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett meddelande i kön

Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. I koden nedan används [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) -metoden för att uppdatera ett meddelande. Timeout för synlighet anges till 0, vilket innebär att meddelandet visas omedelbart och innehållet uppdateras.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Hämta kölängden

Du kan hämta en uppskattning av antalet meddelanden i en kö. Metoden [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) ber Queue Service att returnera metadata om kön och `approximate_message_count` . Resultatet är bara ungefärlig eftersom meddelanden kan läggas till eller tas bort när Queue Service svarar på din begäran.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Ta bort en kö

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du metoden [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) .

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i Queue Storage kan du följa dessa länkar om du vill veta mer.

* [Azure-köer python API-referens](/python/api/azure-storage-queue)
* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK för python]: https://github.com/Azure/azure-storage-python
