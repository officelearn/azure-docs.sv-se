---
title: Skicka eller ta emot händelser från Azure Event Hubs med python (senaste)
description: Den här artikeln innehåller en genom gång av hur du skapar ett python-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det senaste Azure-eventhub version 5-paketet.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: d977ae9ea8b78664ac1d3a318f58553da696c089
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906354"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Skicka händelser till eller ta emot händelser från Event Hub med python (Azure-eventhub version 5)

Azure Event Hubs är en stor data strömnings plattform och händelse inmatnings tjänst som kan ta emot och bearbeta miljon tals händelser per sekund. Event Hub kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en Event Hub kan transformeras och lagras med hjälp av en analys av real tids analys eller batch-/minnes kort. Mer information finns i [Event Hubs översikt](event-hubs-about.md) och [Event Hubs funktioner](event-hubs-features.md).

I den här snabb starten beskrivs hur du skapar python-program som kan skicka händelser till eller ta emot händelser från en händelsehubben.

> [!IMPORTANT]
> I den här snabb starten används version 5 av Azure Event Hubs python SDK. En snabb start som använder version 1 av python SDK finns i [den här artikeln](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Ett aktivt Event Hubs namn område och händelsehubben. Skapa dem genom att följa anvisningarna i [snabb start: skapa en Event Hub med hjälp av Azure Portal](event-hubs-create.md). Registrera namn området och händelsehubben för att använda dem senare i den här snabb starten.
- Namnet på den delade åtkomst nyckeln och värdet för primär nyckel för din Event Hubs-namnrymd. Hämta namnet och värdet för åtkomst nyckeln genom att följa anvisningarna i [Hämta en anslutnings sträng för Event Hub](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Standard namnet för åtkomst nyckeln är *RootManageSharedAccessKey*. Registrera nyckel namnet och värdet för primär nyckel som du vill använda senare i den här snabb starten.
- Python 2,7 eller 3,5 eller senare, med PIP installerat och uppdaterat.
- Python-paketet för Event Hubs. 

    Installera paketet genom att köra det här kommandot i en kommando tolk med python i sökvägen:

    ```cmd
    pip install azure-eventhub
    ```

    Installera följande paket för att ta emot händelser med hjälp av Azure Blob Storage som kontroll punkts Arkiv:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>Skicka händelser
I det här avsnittet skapar du ett Python-skript för att skicka händelser till händelsehubben som du skapade tidigare.

1. Öppna din favorit-eller python-redigerare, t. ex. [Visual Studio Code](https://code.visualstudio.com/).
2. Skapa ett skript som heter *send.py*. Det här skriptet skickar en batch med händelser till händelsehubben som du skapade tidigare.
3. Klistra in följande kod i *send.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > För den fullständiga käll koden, inklusive informations kommentarer, går du till [sidan GitHub send_async. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="receive-events"></a>Ta emot händelser
Den här snabb starten använder Azure Blob Storage som ett kontroll punkts arkiv. Kontroll punkts arkivet används för att bevara kontroll punkter (det vill säga de senaste Läs positionerna).  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Skapa ett Azure Storage-konto och en BLOB-behållare
Skapa ett Azure Storage-konto och en BLOB-behållare i det genom att utföra följande steg:

1. [Skapa ett Azure Storage konto](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Skapa en BLOB-behållare](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Se till att du registrerar anslutnings strängen och behållar namnet för senare användning i mottagar koden.


### <a name="create-a-python-script-to-receive-events"></a>Skapa en pythonskript för att ta emot händelser

I det här avsnittet skapar du ett Python-skript för att ta emot händelser från händelsehubben:

1. Öppna din favorit-eller python-redigerare, t. ex. [Visual Studio Code](https://code.visualstudio.com/).
2. Skapa ett skript som heter *recv.py*.
3. Klistra in följande kod i *Recv.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > För den fullständiga käll koden, inklusive ytterligare informations kommentarer, går du till [sidan GitHub recv_with_checkpoint_store_async. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Köra mottagar appen

Öppna en kommandotolk med Python i dess sökväg för att köra skriptet och kör sedan det här kommandot:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Köra avsändar programmet

Öppna en kommandotolk med Python i dess sökväg för att köra skriptet och kör sedan det här kommandot:

```bash
python send.py
```

Mottagar fönstret ska visa de meddelanden som skickats till händelsehubben.


## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du skickat och tagit emot händelser asynkront. Information om hur du skickar och tar emot händelser synkront finns på [sidan GitHub sync_samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

För alla exempel (både synkrona och asynkrona) på GitHub går du till [Azure Event Hubs-klient bibliotek för python-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
