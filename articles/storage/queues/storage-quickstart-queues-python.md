---
title: 'Snabbstart: Azure Queue storage library v12 - Python'
description: Lär dig hur du använder Azure Queue Python v12-biblioteket för att skapa en kö och lägga till meddelanden i kön. Därefter får du lära dig att läsa och ta bort meddelanden från kön. Du får också lära dig hur du tar bort en kö.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: d6ccd3cc61f9d8244874823be76496a4f4e1073c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199775"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Snabbstart: Azure Queue storage client library v12 for Python Snabbstart: Azure Queue storage client library v12 for Python Snabbstart: Azure Queue storage client library v12 for Python Snabbstart

Kom igång med Azure Queue storage client library version 12 för Python. Azure Queue storage är en tjänst för att lagra ett stort antal meddelanden för senare hämtning och bearbetning. Följ dessa steg för att installera paketet och prova exempelkod för grundläggande uppgifter.

Använd Azure Queue storage client library v12 for Python för att:

* Skapa en kö
* Lägga till meddelanden i en kö
* Granska meddelanden i en kö
* Uppdatera ett meddelande i en kö
* Ta emot meddelanden från en kö
* Ta bort meddelanden från en kö
* Ta bort en kö

[API-referensdokumentation](https://docs.microsoft.com/python/api/azure-storage-queue/index) | [Biblioteksnamnkodpaket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue) | [(Python Package Index)](https://pypi.org/project/azure-storage-queue/) | [Exempel](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
* Azure storage-konto - [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) för ditt operativsystem - 2.7, 3.5 eller högre

## <a name="setting-up"></a>Inrätta

I det här avsnittet får du hjälp med att förbereda ett projekt för att arbeta med Azure Queue storage-klientbiblioteket v12 för Python.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Python-program med namnet *queues-quickstart-v12*.

1. Skapa en ny katalog för projektet i ett konsolfönster (till exempel cmd, PowerShell eller Bash).

    ```console
    mkdir queues-quickstart-v12
    ```

1. Växla till den nyskapade *kökatalogen-quickstart-v12.*

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Installera paketet

Installera Azure Blob storage-klientbiblioteket för `pip install` Python-paketet med kommandot.

```console
pip install azure-storage-queue
```

Det här kommandot installerar Azure Queue storage-klientbiblioteket för Python-paketet och alla bibliotek som det är beroende av. I det här fallet är det bara Azure-kärnbiblioteket för Python.

### <a name="set-up-the-app-framework"></a>Konfigurera ramverket för appen

1. Öppna en ny textfil i kodredigeraren
1. Lägga `import` till utdrag
1. Skapa strukturen för programmet, inklusive mycket grundläggande undantagshantering

    Här är koden:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Spara den nya filen som *queues-quickstart-v12.py* i katalogen *köer-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Ett kömeddelande kan vara upp till 64 kB i storlek. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Köer används ofta för att skapa en eftersläpning av arbete för att bearbeta asynkront. Kölagring erbjuder tre typer av resurser:

* Lagringskontot
* En kö i lagringskontot
* Meddelanden i kön

Följande diagram visar relationen mellan de här resurserna.

![Diagram över lagringsarkitektur för kö](./media/storage-queues-introduction/queue1.png)

Använd följande Python-klasser för att interagera med dessa resurser:

* [QueueServiceClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` Med möjlighet att hantera alla köer i ditt lagringskonto.
* [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient): `QueueClient` Klassen låter dig hantera och manipulera en enskild kö och dess meddelanden.
* [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` Klassen representerar de enskilda objekt som returneras när du anropar [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) i en kö.

## <a name="code-examples"></a>Kodexempel

I det här exemplet visar kodavsnitt hur du utför följande åtgärder med Azure Queue storage-klientbiblioteket för Python:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en kö](#create-a-queue)
* [Lägga till meddelanden i en kö](#add-messages-to-a-queue)
* [Granska meddelanden i en kö](#peek-at-messages-in-a-queue)
* [Uppdatera ett meddelande i en kö](#update-a-message-in-a-queue)
* [Ta emot meddelanden från en kö](#receive-messages-from-a-queue)
* [Ta bort meddelanden från en kö](#delete-messages-from-a-queue)
* [Ta bort en kö](#delete-a-queue)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutningssträngen för lagringskontot. Anslutningssträngen lagras den miljövariabel som skapas i avsnittet [Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den `try` här koden i blocket:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Skapa en kö

Bestäm ett namn för den nya kön. Koden nedan lägger till ett UUID-värde i könamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Könamn får bara innehålla gemener, siffror och bindestreck och måste börja med en bokstav eller ett tal. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck. Namnet måste också vara mellan 3 och 63 tecken långt. Mer information om namngivning av köer finns i [Namnge köer och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata).

Skapa en instans av klassen [QueueClient.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) Anropa sedan [create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) för att skapa kön i ditt lagringskonto.

Lägg till den här `try` koden i slutet av blocket:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Lägga till meddelanden i en kö

Följande kodavsnitt lägger till meddelanden i [send_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) kö genom att anropa send_message-metoden. Den sparar också [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage) som returneras från det tredje `send_message` samtalet. Används `saved_message` för att uppdatera meddelandeinnehållet senare i programmet.

Lägg till den här `try` koden i slutet av blocket:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Granska meddelanden i en kö

Granska meddelandena i kön genom [peek_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) att anropa peek_messages-metoden. Metoden `peek_messages` hämtar ett eller flera meddelanden från framsidan av kön men ändrar inte meddelandets synlighet.

Lägg till den här `try` koden i slutet av blocket:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Uppdatera ett meddelande i en kö

Uppdatera innehållet i ett meddelande genom att anropa [update_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metoden. Metoden `update_message` kan ändra ett meddelandes synlighetstidsutgång och innehåll. Meddelandeinnehållet måste vara en UTF-8-kodad sträng som är upp till 64 kB i storlek. Tillsammans med det nya innehållet skickar du in värden från meddelandet som sparades tidigare i koden. Värdena `saved_message` identifierar vilket meddelande som ska uppdateras.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Hämta tidigare tillagda meddelanden genom att anropa [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) metoden.

Lägg till den här `try` koden i slutet av blocket:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Ta bort meddelanden från en kö

Ta bort meddelanden från kön när de har tagits emot och bearbetats. I det här fallet är bearbetning bara visar meddelandet på konsolen.

Appen pausar för användarindata genom att anropa `input` innan den bearbetar och tar bort meddelandena. Kontrollera i din [Azure-portal](https://portal.azure.com) att resurserna har skapats korrekt innan de tas bort. Alla meddelanden som inte uttryckligen tas bort kommer så småningom att bli synliga i kön igen för en ny chans att bearbeta dem.

Lägg till den här `try` koden i slutet av blocket:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Ta bort en kö

Följande kod rensar de resurser som appen har [delete_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) skapat genom att ta bort kön med delete_queue-metoden.

Lägg till den här `try` koden i slutet av blocket och spara filen:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar och lägger till tre meddelanden i en Azure-kö. Koden visar meddelandena i kön och hämtar och tar sedan bort dem innan du slutligen tar bort kön.

I konsolfönstret navigerar du till katalogen som innehåller *queues-quickstart-v12.py-filen* och kör sedan följande `python` kommando för att köra appen.

```console
python queues-quickstart-v12.py
```

Utdata för appen liknar följande exempel:

```output
Azure Queue storage v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

När appen pausas innan du tar emot meddelanden kontrollerar du ditt lagringskonto i [Azure-portalen](https://portal.azure.com). Kontrollera att meddelandena finns i kön.

Tryck på **Retur** för att ta emot och ta bort meddelandena. När du uppmanas till det trycker du på **Retur** igen för att ta bort kön och slutför demon.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar en kö och lägger till meddelanden i den med Python-kod. Sedan lärde du dig att granska, hämta och ta bort meddelanden. Slutligen har du lärt dig hur du tar bort en meddelandekö.

För självstudier, exempel, snabbstarter och annan dokumentation, besök:

> [!div class="nextstepaction"]
> [Azure för Python-utvecklare](https://docs.microsoft.com/azure/python/)

* Mer information finns i [Azure Storage-biblioteken för Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
* Om du vill se fler exempelappar för Azure Queue storage fortsätter du till [Azure Queue storage v12 Python-klientbiblioteksexempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
