---
title: Använda Queue Storage från python-Azure Storage
description: Lär dig hur du använder Azure Kötjänst från python för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/14/2018
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 1ed084bfa0cf6879983e38ac6a8c5ab57e8948a8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721361"
---
# <a name="how-to-use-queue-storage-from-python"></a>Använda Queue Storage från Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med Azure Queue Storage-tjänsten. Exemplen skrivs i python och använder [Microsoft Azure Storage SDK för python]. De scenarier som beskrivs är att **Infoga**, **Granska**, **Hämta**och **ta bort** Kömeddelanden, samt **skapa och ta bort köer**. Mer information om köer finns i avsnittet [Nästa steg].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Hämta och installera Azure Storage SDK för python

[Azure Storage SDK för python](https://github.com/azure/azure-storage-python) kräver python 2,7, 3,3, 3,4, 3,5 eller 3,6.
 
### <a name="install-via-pypi"></a>Installera via PyPi

Om du vill installera via python-paket indexet (PyPI) skriver du:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för python version 0,36 eller tidigare, måste du avinstallera den äldre SDK: `pip uninstall azure-storage` n med innan du installerar det senaste paketet.

Alternativa installations metoder finns i [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Visa exempel programmet

Om du vill visa och köra ett exempel program som visar hur du använder python med Azure- [köer, se Azure Storage: Komma igång med Azure-köer i](https://github.com/Azure-Samples/storage-queue-python-getting-started)python. 

Kontrol lera att du har installerat både `azure-storage-queue` -och `azure-storage-common` -paketen för att köra exempel programmet.

## <a name="how-to-create-a-queue"></a>Instruktioner: Skapa en kö

Med **QueueService** -objektet kan du arbeta med köer. Följande kod skapar ett **QueueService** -objekt. Lägg till följande längst upp i en python-fil som du vill ha program mässigt åtkomst till Azure Storage:

```python
from azure.storage.queue import QueueService
```

Följande kod skapar ett **QueueService** -objekt med hjälp av lagrings kontots namn och konto nyckeln. Ersätt "mitt konto" och "MyKey" med ditt konto namn och din nyckel.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instruktioner: Infoga ett meddelande i en kö
Om du vill infoga ett meddelande i en kö använder du metoden **Placera\_meddelande** för att skapa ett nytt meddelande och lägga till det i kön.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Instruktioner: Titta på nästa meddelande
Du kan titta på meddelandet överst i en kö utan att ta bort det från kön genom att anropa metoden **Granska\_meddelanden** . Som standard tittar **gransknings\_meddelanden** i ett enda meddelande.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Instruktioner: Ta bort meddelanden från kön
Koden tar bort ett meddelande från en kö i två steg. När du anropar **Hämta\_meddelanden**får du nästa meddelande i en kö som standard. Ett meddelande som returnerades från **Hämta\_meddelanden** blir osynligt för all annan kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill slutföra borttagningen av meddelandet från kön måste du också **anropa\_ta bort meddelande**. Den här två stegs processen för att ta bort ett meddelande säkerställer att när din kod inte kan bearbeta ett meddelande på grund av maskin-eller program varu fel, kan en annan instans av koden Hämta samma meddelande och försöka igen. Dina kod anrop **tar\_bort meddelande** direkt efter att meddelandet har bearbetats.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.
För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kod exempel används metoden **get\_Messages** för att hämta 16 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en for-slinga. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instruktioner: Ändra innehållet i ett köat meddelande
Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. I koden nedan används **uppdaterings\_meddelande** metoden för att uppdatera ett meddelande. Timeout för synlighet anges till 0, vilket innebär att meddelandet visas omedelbart och innehållet uppdateras.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Instruktioner: Hämta Kölängd
Du kan hämta en uppskattning av antalet meddelanden i en kö. Metoden **Hämta\_köns\_metadata** ber Queue Service att returnera metadata om kön och **approximate_message_count**. Resultatet är bara ungefärlig eftersom meddelanden kan läggas till eller tas bort när Queue Service svarar på din begäran.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Instruktioner: Ta bort en kö
Om du vill ta bort en kö och alla meddelanden som finns i den anropar du metoden **\_ta bort kö** .

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Queue Storage kan du följa dessa länkar om du vill veta mer.

* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK för python]: https://github.com/Azure/azure-storage-python
