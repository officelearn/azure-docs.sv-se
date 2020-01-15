---
title: Skicka eller ta emot händelser med python-Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genom gång av hur du skapar ett python-program som skickar händelser till Azure Event Hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: a137f274744a6acec22d036f9f4c5c4a5174cc14
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942599"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Skicka händelser till eller ta emot händelser från Event Hubs med python

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här självstudien beskrivs hur du skapar python-program för att skicka händelser till eller ta emot händelser från en händelsehubben. 

> [!IMPORTANT]
> I den här snabb starten används version 5 av Azure Event Hubs python SDK. En snabb start som använder den gamla version 1 av python SDK finns i [den här artikeln](event-hubs-python-get-started-send.md). Om du använder version 1 av SDK rekommenderar vi att du migrerar din kod till den senaste versionen. Mer information finns i [migreringsguiden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).


## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, finns följande förhandskrav:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Ett aktivt Event Hubs-namnområde och händelsehubben som skapats genom att följa anvisningarna i [snabb start: skapa en händelsehubben med Azure Portal](event-hubs-create.md). Anteckna namn området och händelsehubben som ska användas senare i den här genom gången. 
- Namnet på den delade åtkomst nyckeln och värdet för primär nyckel för din Event Hubs-namnrymd. Hämta namnet och värdet för åtkomst nyckeln genom att följa anvisningarna i [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Standard namnet för åtkomst nyckeln är **RootManageSharedAccessKey**. Kopiera nyckel namnet och värdet för primär nyckel som ska användas senare i den här genom gången. 
- Python 2,7, 3,5 eller senare, med `pip` installerat och uppdaterat.
- Python-paketet för Event Hubs. Installera paketet genom att köra det här kommandot i en kommando tolk med python i sökvägen: 
    
    ```cmd
    pip install azure-eventhub
    ```

    Installera det här paketet för att ta emot händelser med hjälp av Azure Blob Storage som kontroll punkts arkiv. 

    ```cmd
    pip install azure-eventhub-checkpointstoreblobaio
    ```

## <a name="send-events"></a>Skicka händelser
I det här avsnittet ska du skapa ett Python-skript för att skicka händelser till händelsehubben som du skapade tidigare. 

1. Öppna din favorit-eller python-redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa ett skript som heter **send.py**. Det här skriptet skickar en batch med händelser till händelsehubben som du skapade tidigare. 
3. Klistra in följande kod i send.py. Se kod kommentarer för mer information. 

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData
    
    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and 
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()
    
            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))
    
            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)
    
    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())
    
    ```

    > [!NOTE]
    > Den fullständiga käll koden med mycket användbara kommentarer finns i [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)

## <a name="receive-events"></a>Ta emot händelser
I den här snabb starten används ett Azure-Blob Storage som kontroll punkts arkiv. Kontroll punkts arkivet används för att bevara kontroll punkter (senaste Läs position).  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Skapa en Azure Storage och en BLOB-behållare
Följ dessa steg om du vill skapa en Azure Storage-konto en BLOB-behållare. 

1. [Skapa ett Azure Storage konto](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
2. [Skapa en BLOB-behållare](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Anteckna anslutnings sträng och behållar namn. Du kommer att använda dem i Receive-koden. 


### <a name="create-python-script-to-receive-events"></a>Skapa Python-skript för att ta emot händelser

I det här avsnittet skapar du ett Python-skript för att ta emot händelser från händelsehubben:

1. Öppna din favorit-eller python-redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa ett skript som heter **recv.py**.
3. Klistra in följande kod i recv.py. Se kod kommentarer för mer information. 

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore
    
    
    async def on_event(partition_context, event):
        # print the event data 
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))
    
        # update the checkpoint so that the program doesn't read the events 
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)
    
    async def main():
        # create an Azure blob checkpoint store to store the checkpoints 
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")
    
        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)
    
    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Den fullständiga käll koden med mycket användbara kommentarer finns i [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)


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

Du bör se de meddelanden som skickades till händelsehubben i mottagar fönstret. 


## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du skickat och ta emot händelser asynkront. Information om hur du skickar och tar emot händelser synkront finns i exempel på [den här platsen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Du kan hitta alla exempel (både synkronisering och asynkron) på GitHub [här](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 
