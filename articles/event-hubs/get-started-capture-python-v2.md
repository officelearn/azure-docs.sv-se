---
title: Läs Azure Event Hubs fångade data från python-appen | Microsoft Docs
description: Den här artikeln visar hur du skriver python-kod för att avbilda data som skickas till en händelsehubben och läsa insamlade händelse data från en Azure Storage.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: 43223f7cb9ed254340c99d235d494d1e93583c7f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293546"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Avbilda Event Hubs data i Azure Storage och läsa det med python 
Du kan använda Konfigurera en händelsehubben så att data som skickas till en Event Hub samlas in i en Azure Storage eller Azure Data Lake Storage. Den här artikeln visar hur du använder Skriv python-kod för att skicka händelser till en händelsehubben och läsa fångade data från en Azure Blob Storage. Mer information om den här funktionen finns i [Översikt över Event Hubs Capture-funktionen](event-hubs-capture-overview.md).

Det här exemplet används den [Azure Python SDK](https://azure.microsoft.com/develop/python/) att demonstrera avbildningsfunktionen. Sender.py programmet skickar simulerad telemetri i miljön till Event Hubs i JSON-format. Händelsehubben har konfigurerats för att använda avbildningsfunktionen för att skriva dessa data till Blob storage i batchar. Appen capturereader.py läser dessa blobar och skapar en Lägg till fil per enhet. Sedan skriver appen data till CSV-filer.

> [!IMPORTANT]
> I den här snabb starten används version 5 av Azure Event Hubs python SDK. En snabb start som använder den gamla version 1 av python SDK finns i [den här artikeln](event-hubs-capture-python.md). Om du använder version 1 av SDK rekommenderar vi att du migrerar din kod till den senaste versionen. Mer information finns i [migreringsguiden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).

I den här snabbstarten kommer du att göra följande: 

> [!div class="checklist"]
> * Skapa ett Azure Blob Storage-konto och-behållare i Azure Portal.
> * Skapa ett Event Hubs namn område med hjälp av Azure Portal.
> * Skapa en Event Hub med insamlings funktionen aktive rad och Anslut den till ditt lagrings konto.
> * Skicka data till händelsehubben med hjälp av ett Python-skript.
> * Läsa och bearbeta filer från Event Hubs avbildning med hjälp av ett annat Python-skript.

## <a name="prerequisites"></a>Krav

- Python 2,7, 3,5 eller senare, med `pip` installerat och uppdaterat.
- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- [Skapa ett Event Hubs-namnområde och en händelsehubben i namn området](event-hubs-create.md). Anteckna namnet på namn området Event Hubs, namnet på händelsehubben och den primära åtkomst nyckeln för namn området. Hämta åtkomst nyckeln genom att följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Standard nyckelnamnet är: **RootManageSharedAccessKey**. Du behöver inte anslutnings strängen för självstudien. Du behöver bara den primära nyckeln. 
- Följ dessa steg om du vill skapa ett **Azure Storage-konto** och en **BLOB-behållare**:
    1. [Skapa ett Azure Storage-konto](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal).
    2. [Skapa en BLOB-behållare i lagringen](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 
    3. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string).

        Anteckna **anslutnings sträng** och **behållar namn**. Du kommer att använda dem senare i koden. 
- Aktivera funktionen **Capture** för Event Hub genom att följa anvisningarna nedan: [Aktivera Event Hubs avbildning med hjälp av Azure Portal](event-hubs-capture-enable-through-portal.md). Välj det lagrings konto och den BLOB-behållare som du skapade i föregående steg. Du kan också aktivera funktionen när du skapar en Event Hub. 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Skapa en pythonskript för att skicka händelser till din event hub
I det här avsnittet skapar du ett Python-skript som skickar 200-händelser (10 enheter * 20 händelser) till en Event Hub. Dessa händelser är exempel på miljö läsning som skickas i JSON-format. 

1. Öppna din favorit-eller python-redigerare, t. ex. [Visual Studio Code][Visual Studio Code].
2. Skapa ett skript som heter **sender.py**. 
3. Klistra in följande kod i sender.py. Se kod kommentarer för mer information. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. Ersätt följande värden i skripten:
    1. Ersätt `EVENT HUBS NAMESPACE CONNECTION STRING` med anslutnings strängen för ditt Event Hubs-namnområde.
    2. Ersätt `EVENT HUB NAME` med namnet på händelsehubben. 
5. Kör skriptet för att skicka händelser till händelsehubben. 
6. I Azure Portal kan du kontrol lera att händelsehubben har tagit emot meddelandena. Växla till vyn **meddelanden** i avsnittet **mått** . Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan det visar att meddelandena har mottagits. 

    [![verifiera att händelsehubben tog emot meddelanden](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Skapa en pythonskript för att läsa dina filer som avbildning
I det här exemplet lagras insamlade data i Azure Blob Storage. Skriptet i det här avsnittet läser in datafilerna från Azure Storage och genererar CSV-filer så att du enkelt kan öppna och visa innehållet. 10 filer visas i den aktuella arbets katalogen i programmet. De här filerna kommer att innehålla miljö avläsningar för 10 enheter. 

1. Skapa ett skript med namnet **capturereader.py**i python-redigeraren. Det här skriptet läser de hämtade filerna och skapar en fil per enhet för att skriva data endast för den enheten.
2. Klistra in följande kod i capturereader.py. Se kod kommentarer för mer information. 
   
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
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. Ersätt `<AZURE STORAGE CONNECTION STRING>` med anslutnings strängen för ditt Azure Storage-konto. Namnet på den behållare som du skapade i den här självstudien är: **avbilda**. Om du har använt ett annat namn på behållaren ersätter du `capture` med namnet på behållaren i lagrings kontot. 

## <a name="run-the-scripts"></a>Kör skripten
1. Öppna en kommandotolk med Python i dess sökväg och kör sedan följande kommandon för att installera nödvändiga Python-paket:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Ändra katalogen till var du sparade sender.py och capturereader.py och kör det här kommandot:
   
   ```
   python sender.py
   ```
   
   Detta kommando startar en ny Python-process för att köra avsändaren.
3. Vänta några minuter för avbildning ska köras. Skriv sedan följande kommando i kommandofönstret ursprungliga:
   
   ```
   python capturereader.py
   ```

   Den här capture-processorn använder den lokala katalogen för att ladda ned alla blobbar från den lagringskonto/behållaren. Bearbetas av någon som inte är tomma och skriver resultatet som CSV-filer i den lokala katalogen.

## <a name="next-steps"></a>Nästa steg
Kolla in python-exempel på GitHub [här](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
