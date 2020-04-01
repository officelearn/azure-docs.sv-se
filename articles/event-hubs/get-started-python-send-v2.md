---
title: Skicka eller ta emot händelser från Azure Event Hubs med Python (senaste)
description: Den här artikeln innehåller en genomgång för att skapa ett Python-program som skickar/tar emot händelser till/från Azure Event Hubs med det senaste azure-eventhub version 5-paketet.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 352ff91bf26c7ff4f6945431fe6e1357f030e1db
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477532"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Skicka händelser till eller ta emot händelser från händelsehubbar med hjälp av Python (azure-eventhub version 5)
Den här snabbstarten visar hur du skickar händelser till och ta emot händelser från en händelsehubb med **azure-eventhub version 5 Python-paketet.**

> [!IMPORTANT]
> Den här snabbstarten använder det senaste azure-eventhub version 5-paketet. En snabbstart som använder det gamla azure-eventhub version 1-paketet finns i [Skicka och ta emot händelser med azure-eventhub version 1](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Krav
Om du inte har gjort det tidigare i Azure Event Hubs läser du [översikt över eventhubbar](event-hubs-about.md) innan du gör den här snabbstarten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure-prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/) eller använda dina msdn-prenumerationsförmåner när du [skapar ett konto](https://azure.microsoft.com).
- Python 2.7 eller 3.5 eller senare, med PIP installerat och uppdaterat.
- Python-paketet för eventhubbar. 

    Om du vill installera paketet kör du det här kommandot i en kommandotolk som har Python i sökvägen:

    ```cmd
    pip install azure-eventhub
    ```

    Installera följande paket för att ta emot händelserna med hjälp av Azure Blob storage som kontrollpunktsarkiv:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Skapa ett namnområde för händelsehubbar och en händelsehubb**. Det första steget är att använda [Azure-portalen](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs och hämta de hanteringsautentiseringsuppgifter som ditt program behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutningssträngen för namnområdet Event Hubs** genom att följa instruktionerna från artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutningssträngen senare i den här snabbstarten.

## <a name="send-events"></a>Skicka händelser
I det här avsnittet skapar du ett Python-skript för att skicka händelser till händelsehubben som du skapade tidigare.

1. Öppna din favorith python-redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/).
2. Skapa ett skript som heter *send.py*. Skriptet skickar en grupp händelser till händelsehubben som du skapade tidigare.
3. Klistra in följande kod i *send.py:*

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
    > Den fullständiga källkoden, inklusive informationskommentarer, går till [sidan GitHub send_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).
    

## <a name="receive-events"></a>Ta emot händelser
Den här snabbstarten använder Azure Blob-lagring som ett kontrollpunktsarkiv. Kontrollpunktsarkivet används för att bevara kontrollpunkter (det vill än de senaste lästa positionerna).  

> [!NOTE]
> Om du körs på Azure Stack Hub kan den plattformen ha stöd för en annan version av Storage Blob SDK än de som vanligtvis är tillgängliga på Azure. Om du till exempel kör [på Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)är den högsta tillgängliga versionen för lagringstjänsten version 2017-11-09. I det här fallet, förutom följande steg i det här avsnittet, måste du också lägga till kod för att rikta lagringstjänstens API-version 2017-11-09. Ett exempel på hur du inriktar dig på en specifik Storage API-version finns i de [synkrona](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) och [asynkrona](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) exemplen på GitHub. Mer information om Azure Storage-tjänstversionerna som stöds på Azure Stack Hub finns i [Azure Stack Hub storage: Skillnader och överväganden](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Skapa ett Azure-lagringskonto och en blob-behållare
Skapa ett Azure-lagringskonto och en blob-behållare i det genom att göra följande steg:

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Skapa en blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutningssträngen till lagringskontot](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Var noga med att registrera anslutningssträngen och behållarnamnet för senare användning i mottagningskoden.


### <a name="create-a-python-script-to-receive-events"></a>Skapa ett Python-skript för att ta emot händelser

I det här avsnittet skapar du ett Python-skript för att ta emot händelser från din händelsehubb:

1. Öppna din favorith python-redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/).
2. Skapa ett skript som heter *recv.py*.
3. Klistra in följande kod i *recv.py:*

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
    > Den fullständiga källkoden, inklusive ytterligare informationskommentarer, går till [sidan GitHub recv_with_checkpoint_store_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Kör mottagarappen

Om du vill köra skriptet öppnar du en kommandotolk som har Python i sökvägen och kör sedan det här kommandot:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Kör avsändarappen

Om du vill köra skriptet öppnar du en kommandotolk som har Python i sökvägen och kör sedan det här kommandot:

```bash
python send.py
```

Mottagarfönstret ska visa de meddelanden som skickades till händelsehubben.


## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skickat och tagit emot händelser asynkront. Om du vill veta hur du skickar och ta emot händelser synkront går du till [sidan GitHub sync_samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

För alla exempel (både synkrona och asynkrona) på GitHub går du till [Azure Event Hubs-klientbibliotek för Python-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
