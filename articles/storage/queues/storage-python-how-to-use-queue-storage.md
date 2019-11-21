---
title: How to use Azure Queue storage v2.1 from Python - Azure Storage
description: Learn how to use the Azure Queue service v2.1 from Python to create and delete queues, and insert, get, and delete messages.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 09/17/2019
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: b5382a6a1ea381d57a026e9d42190152e38f7696
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209531"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>How to use Azure Queue storage v2.1 from Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

This article demonstrates common scenarios using the Azure Queue storage service. The scenarios covered include inserting, peeking, getting, and deleting queue messages, and creating and deleting queues.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt

The samples in this article are written in Python and use the [Microsoft Azure Storage SDK for Python]. For more information on queues, see the [Next steps](#next-steps) section.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Download and install Azure Storage SDK for Python

The [Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) requires Python version 2.7, 3.3, or later.
 
### <a name="install-via-pypi"></a>Install via PyPi

To install via the Python Package Index (PyPI), type:

```bash
pip install azure-storage-blob==2.1.0
```

> [!NOTE]
> If you are upgrading from the Azure Storage SDK for Python version 0.36 or earlier, uninstall the older SDK using `pip uninstall azure-storage` before installing the latest package.

For alternative installation methods, see [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>View the sample application

To view and run a sample application that shows how to use Python with Azure Queues, see [Azure Storage: Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

To run the sample application, make sure you have installed both the `azure-storage-queue` and `azure-storage-common` packages.

## <a name="create-a-queue"></a>Skapa en kö

The [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) object lets you work with queues. The following code creates a `QueueService` object. Add the following near the top of any Python file in which you wish to programmatically access Azure Storage:

```python
from azure.storage.queue import QueueService
```

The following code creates a `QueueService` object using the storage account name and account key. Replace *myaccount* and *mykey* with your account name and key.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Infoga ett meddelande i en kö

To insert a message into a queue, use the [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) method to create a new message and add it to the queue.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure queue messages are stored as text. If you want to store binary data, setup Base64 encoding and decoding functions on the queue service object before putting a message in the queue.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>En titt på nästa meddelande

You can peek at the message in the front of a queue without removing it from the queue by calling the [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) method. By default, `peek_messages` peeks at a single message.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Dequeue messages

Your code removes a message from a queue in two steps. When you call [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), you get the next message in a queue by default. A message returned from `get_messages` becomes invisible to any other code reading messages from this queue. Som standard är det här meddelandet osynligt i 30 sekunder. To finish removing the message from the queue, you must also call [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). This two-step process of removing a message assures that when your code fails to process a message due to hardware or software failure, another instance of your code can get the same message and try again. Your code calls `delete_message` right after the message has been processed.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. The following code example uses the `get_messages` method to get 16 messages in one call. Then it processes each message using a for loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett meddelande i kön

Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. The code below uses the [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) method to update a message. The visibility timeout is set to 0, meaning the message appears immediately and the content is updated.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Hämta kölängden

Du kan hämta en uppskattning av antalet meddelanden i en kö. The [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) method asks the queue service to return metadata about the queue, and the `approximate_message_count`. The result is only approximate because messages can be added or removed after the queue service responds to your request.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Ta bort en kö

To delete a queue and all the messages contained in it, call the [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) method.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Nästa steg

Now that you've learned the basics of queue storage, follow these links to learn more.

* [Azure Queues Python API reference](/python/api/azure-storage-queue)
* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
