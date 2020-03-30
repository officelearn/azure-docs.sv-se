---
title: 'Snabbstart: Läsa infångade data från Python-appen - Azure Event Hubs'
description: 'Snabbstart: Skript som använder Azure Python SDK för att demonstrera funktionen Händelsehubbar.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: 6c830cf871c2ae650bb61e8b3712a664e9e405d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187295"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Snabbstart: Händelsehubbar Fånga genomgång: Python (azure-eventhub version 1)

Capture är en funktion i Azure Event Hubs. Du kan använda Capture för att automatiskt leverera strömmande data i din händelsehubb till ett Azure Blob-lagringskonto som du väljer. Den här funktionen gör det enkelt att göra batchbearbetning på strömmande data i realtid. I den här artikeln beskrivs hur du använder Händelsehubbar capture med Python. Mer information om Insamling av eventhubbar finns [i Samla in händelser via Azure Event Hubs][Overview of Event Hubs Capture].

Den här genomgången använder [Azure Python SDK](https://azure.microsoft.com/develop/python/) för att demonstrera capture-funktionen. Det *sender.py* programmet skickar simulerad miljötelemetri till Event Hubs i JSON-format. Händelsehubben använder hämtningsfunktionen för att skriva dessa data till Blob-lagring i batchar. Den *capturereader.py* app läser dessa blobbar, skapar en tilläggsfil för var och en av dina enheter och skriver data till *CSV-filer* på varje enhet.

> [!WARNING]
> Den här snabbstarten är för version 1 av Azure Event Hubs Python SDK. Vi rekommenderar att du [migrerar](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) koden till [version 5 av Python SDK](get-started-capture-python-v2.md).

I den här genomgången, du: 

> [!div class="checklist"]
> * Skapa ett Azure Blob-lagringskonto och behållare i Azure-portalen.
> * Aktivera Event Hubs Capture och hänvisa den till ditt lagringskonto.
> * Skicka data till din händelsehubb med hjälp av ett Python-skript.
> * Läs och bearbeta filer från Event Hubs Capture med hjälp av ett annat Python-skript.

## <a name="prerequisites"></a>Krav

- Python 3.4 eller `pip` senare, med installerat och uppdaterat.
  
- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
  
- En aktiv händelsehubbar namnområde och händelsehubb, skapad genom att följa instruktionerna i [Snabbstart: Skapa en händelsehubb med Azure-portalen](event-hubs-create.md). Anteckna namnområdet och händelsehubbens namn som ska användas senare i den här genomgången. 
  
  > [!NOTE]
  > Om du redan har en lagringsbehållare att använda kan du aktivera Capture och välja lagringsbehållaren när du skapar händelsehubben. 
  > 
  
- Namnet på händelsehubbar delade åtkomstnyckeln och primärnyckelvärdet. Sök efter eller skapa dessa värden under **Principer för delad åtkomst** på sidan Event hubbar. Standardnamn för åtkomstnyckel är **RootManageSharedAccessKey**. Kopiera åtkomstnyckelns namn och primärnyckelvärdet som ska användas senare i den här genomgången. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Skapa ett Azure Blob-lagringskonto och -behållare

Skapa ett lagringskonto och en behållare som ska användas för insamlingen. 

1. Logga in på [Azure-portalen][Azure portal].
2. I den vänstra navigeringen väljer du **Lagringskonton**och väljer **Lägg till**på skärmen **Lagringskonton** .
3. På skärmen för att skapa lagringskonto väljer du en prenumerations- och resursgrupp och ger lagringskontot ett namn. Du kan lämna de andra valen som standard. Välj **Granska + skapa**, granska inställningarna och välj sedan **Skapa**. 
   
   ![Skapa lagringskonto][1]
   
4. När distributionen är klar väljer du **Gå till resurs**och på skärmen Översikt **över** lagringskonto väljer du **Behållare**.
5. På skärmen **Behållare** väljer du **+ Behållare**. 
6. Ge behållaren ett namn på skärmen **Ny behållare** och välj sedan **OK**. Anteckna behållarnamnet som ska användas senare i genomgången. 
7. I den vänstra navigeringen på **skärmen Behållare** väljer du **Åtkomsttangenter**. Kopiera **lagringskontonamnet**och **nyckelvärdet** under **key1**för att använda senare i genomgången.
 
## <a name="enable-event-hubs-capture"></a>Aktivera hämtning av händelsehubbar

1. I Azure-portalen navigerar du till händelsehubben genom att välja dess namnområde för eventhubbar från **alla resurser,** välja **händelsehubbar** i den vänstra navigeringen och sedan välja händelsehubben. 
2. På skärmen **Översikt för** händelsehubben väljer du **Ta in händelser**.
3. På **inspelningsskärmen** väljer du **På**. Välj sedan **Välj Behållare**under Azure **Storage Container**. 
4. På skärmen **Behållare** väljer du den behållare som du vill använda och väljer sedan **Välj**. 
5. På **inspelningsskärmen** väljer du **Spara ändringar**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Skapa ett Python-skript för att skicka händelser till Event Hub
Det här skriptet skickar 200 händelser till din händelsehubb. Händelserna är enkla miljöavläsningar som skickas i JSON.

1. Öppna din favorith python-redigerare, till exempel [Visual Studio Code][Visual Studio Code].
2. Skapa en ny fil med *namnet sender.py*. 
3. Klistra in följande kod i *sender.py*. Ersätt dina egna värden för \<namnområdet Event Hubs>, \<AccessKeyName \<>, primärnyckelvärde \<> och eventhub->.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Spara filen.

## <a name="create-a-python-script-to-read-capture-files"></a>Skapa ett Python-skript för att läsa Capture-filer

Det här skriptet läser de infångade filerna och skapar en fil för var och en av dina enheter för att skriva data endast för den enheten.

1. Skapa en ny fil som heter *capturereader.py*i Python-redigeraren . 
2. Klistra in följande kod i *capturereader.py*. Ersätt dina sparade \<värden med ditt lagringskonto>, \<åtkomstnyckel \<för lagringskonto> och lagringsbehållare>.
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
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
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Kör Python-skripten

1. Öppna en kommandotolk som har Python i sökvägen och kör dessa kommandon för att installera Pythons nödvändiga paket:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Om du har en `azure-storage` `azure`tidigare version av eller `--upgrade` kan du behöva använda alternativet.
   
   Du kan också behöva köra följande kommando. Köra det här kommandot är inte nödvändigt på de flesta system. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Kör det här kommandot i katalogen där du sparade *sender.py* och *capturereader.py:*
   
   ```cmd
   start python sender.py
   ```
   
   Kommandot startar en ny Python-process för att köra avsändaren.
   
3. När hämtningen är klar kör du det här kommandot:
   
   ```cmd
   python capturereader.py
   ```

   Insamlingsprocessorn hämtar alla icke-tomma blobbar från lagringskontobehållaren och skriver resultaten som *CSV-filer* i den lokala katalogen. 

## <a name="next-steps"></a>Nästa steg

Mer information om eventhubbar finns i: 

* [Översikt över fånga händelsehubbar][Overview of Event Hubs Capture]
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Översikt över Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
