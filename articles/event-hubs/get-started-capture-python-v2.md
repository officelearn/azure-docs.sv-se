---
title: Läs Azure Event Hubs som samlar in data från en Python-app (senaste)
description: Den här artikeln visar hur du skriver Python-kod för att samla in data som skickas till en händelsehubb och läsa de infångade händelsedata från ett Azure-lagringskonto.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 34583ef49b2f919391af3fe5700a558b2dc40700
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187247"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>Samla in eventhubbardata i Azure Storage och läsa dem med hjälp av Python (azure-eventhub version 5)

Du kan konfigurera en händelsehubb så att data som skickas till en händelsehubb samlas in i ett Azure storage-konto eller Azure Data Lake Storage Gen 1 eller Gen 2. Den här artikeln visar hur du skriver Python-kod för att skicka händelser till en händelsehubb och läsa de infångade data från **Azure Blob storage**. Mer information om den här funktionen finns i [funktionsöversikt för funktionen Event Hubs Capture](event-hubs-capture-overview.md).

Den här snabbstarten använder [Azure Python SDK](https://azure.microsoft.com/develop/python/) för att demonstrera capture-funktionen. Den *sender.py* appen skickar simulerad miljötelemetri till händelsehubbar i JSON-format. Händelsehubben är konfigurerad för att använda hämtningsfunktionen för att skriva dessa data till Blob-lagring i batchar. Den *capturereader.py* appen läser dessa blobbar och skapar en tilläggsfil för varje enhet. Appen skriver sedan data i CSV-filer.

> [!IMPORTANT]
> Den här snabbstarten använder version 5 av Azure Event Hubs Python SDK. En snabbstart som använder version 1 av Python SDK finns i [den här artikeln](event-hubs-capture-python.md). 

I den här snabbstarten kommer du att göra följande: 

> [!div class="checklist"]
> * Skapa ett Azure Blob-lagringskonto och behållare i Azure-portalen.
> * Skapa ett namnområde för händelsehubbar med hjälp av Azure-portalen.
> * Skapa en händelsehubb med capture-funktionen aktiverad och anslut den till ditt lagringskonto.
> * Skicka data till din händelsehubb med hjälp av ett Python-skript.
> * Läs och bearbeta filer från Event Hubs Capture med hjälp av ett annat Python-skript.

## <a name="prerequisites"></a>Krav

- Python 2.7 och 3.5 eller senare, med PIP installerat och uppdaterat.  
- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.  
- Ett aktivt namnområde för händelsehubbar och händelsenav.
[Skapa ett namnområde för händelsehubbar och en händelsehubb i namnområdet](event-hubs-create.md). Registrera namnet på namnområdet Event Hubs, namnet på händelsehubben och den primära åtkomstnyckeln för namnområdet. Information om hur du hämtar åtkomstnyckeln finns i [Hämta en anslutningssträng för eventhubbar](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Standardnyckelnamnet är *RootManageSharedAccessKey*. För den här snabbstarten behöver du bara primärnyckeln. Du behöver inte anslutningssträngen.  
- Ett Azure-lagringskonto, en blob-behållare i lagringskontot och en anslutningssträng till lagringskontot. Om du inte har dessa objekt gör du följande:  
    1. [Skapa ett Azure-lagringskonto](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)  
    1. [Skapa en blob-container i lagringskontot](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Hämta anslutningssträngen till lagringskontot](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string)

    Var noga med att registrera anslutningssträngen och behållarnamnet för senare användning i den här snabbstarten.  
- Aktivera capture-funktionen för händelsehubben. Det gör du genom att följa instruktionerna i [Aktivera händelsehubbar fånga med hjälp av Azure-portalen](event-hubs-capture-enable-through-portal.md). Välj lagringskontot och blob-behållaren som du skapade i föregående steg. Du kan också aktivera funktionen när du skapar en händelsehubb.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Skapa ett Python-skript för att skicka händelser till din händelsehubb
I det här avsnittet skapar du ett Python-skript som skickar 200 händelser (10 enheter * 20 händelser) till en händelsehubb. Dessa händelser är ett urval miljöläsning som skickas i JSON-format. 

1. Öppna din favorith python-redigerare, till exempel [Visual Studio Code][Visual Studio Code].
2. Skapa ett skript som heter *sender.py*. 
3. Klistra in följande kod i *sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Ersätt följande värden i skripten:  
    * Ersätt `EVENT HUBS NAMESPACE CONNECTION STRING` med anslutningssträngen för namnområdet Event Hubs.  
    * Ersätt `EVENT HUB NAME` med namnet på händelsehubben.  
5. Kör skriptet för att skicka händelser till händelsehubben.  
6. I Azure-portalen kan du verifiera att händelsehubben har tagit emot meddelandena. Växla till **meddelandevyn** i avsnittet **Mått.** Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan sidan visar att meddelandena har tagits emot. 

    [![Kontrollera att händelsehubben har tagit emot meddelandena](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Skapa ett Python-skript för att läsa dina Capture-filer
I det här exemplet lagras de infångade data i Azure Blob-lagring. Skriptet i det här avsnittet läser de infångade datafilerna från ditt Azure-lagringskonto och genererar CSV-filer som du enkelt kan öppna och visa. Du kommer att se 10 filer i den aktuella arbetskatalogen för programmet. Dessa filer kommer att innehålla miljöavläsningar för 10 enheter. 

1. Skapa ett skript som heter *capturereader.py*i Python-redigeraren . Det här skriptet läser de infångade filerna och skapar en fil för varje enhet att skriva data endast för den enheten.
2. Klistra in följande kod i *capturereader.py*. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Ersätt `AZURE STORAGE CONNECTION STRING` med anslutningssträngen för ditt Azure-lagringskonto. Namnet på behållaren som du skapade i den här snabbstarten är *att fånga*. Om du har använt ett annat namn för behållaren ersätter du *hämtningen* med namnet på behållaren i lagringskontot. 

## <a name="run-the-scripts"></a>Kör skripten
1. Öppna en kommandotolk som har Python i sökvägen och kör sedan dessa kommandon för att installera Python-nödvändiga paket:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Ändra katalogen till den katalog där du sparade *sender.py* och *capturereader.py*och kör det här kommandot:
   
   ```
   python sender.py
   ```
   
   Det här kommandot startar en ny Python-process för att köra avsändaren.
3. Vänta några minuter innan hämtningen körs och ange sedan följande kommando i det ursprungliga kommandofönstret:
   
   ```
   python capturereader.py
   ```

   Den här insamlingsprocessorn använder den lokala katalogen för att hämta alla blobbar från lagringskontot och behållaren. Den bearbetar alla som inte är tomma, och det skriver resultaten som CSV-filer i den lokala katalogen.

## <a name="next-steps"></a>Nästa steg
Kolla in [Python-exempel på GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
