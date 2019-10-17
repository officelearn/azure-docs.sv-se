---
title: Läsa fångade data från python-appen – Azure Event Hubs | Microsoft Docs
description: Skript som använder Azure python SDK för att demonstrera Event Hubs Capture-funktionen.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 10/10/2019
ms.author: shvija
ms.openlocfilehash: 354964e1b66b55dcccd9b5674f011f8c5a38a1c5
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428946"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Genom gång av Event Hubs avbildning: python

Capture är en funktion i Azure Event Hubs. Du kan använda Capture för att automatiskt leverera strömmande data i händelsehubben till ett Azure Blob Storage-konto som du väljer. Den här funktionen gör det enkelt att utföra batchbearbetning vid strömnings data i real tid. Den här artikeln beskriver hur du använder Event Hubs Capture med python. Mer information om Event Hubs avbildning finns i [avbilda händelser via Azure Event Hubs][Overview of Event Hubs Capture].

I den här genom gången används [Azure python SDK](https://azure.microsoft.com/develop/python/) för att demonstrera insamlings funktionen. *Sender.py* -programmet skickar simulerad miljö telemetri till Event HUBS i JSON-format. Händelsehubben använder insamlings funktionen för att skriva dessa data till blob-lagring i batchar. *Capturereader.py* -appen läser dessa blobbar, skapar en tilläggs fil för var och en av dina enheter och skriver data till *CSV* -filer på varje enhet.

I den här genom gången ska du: 

> [!div class="checklist"]
> * Skapa ett Azure Blob Storage-konto och-behållare i Azure Portal.
> * Aktivera Event Hubs avbilda och dirigera den till ditt lagrings konto.
> * Skicka data till händelsehubben med hjälp av ett Python-skript.
> * Läsa och bearbeta filer från Event Hubs avbildning med hjälp av ett annat Python-skript.

## <a name="prerequisites"></a>Krav

- Python 3,4 eller senare, med `pip` installerat och uppdaterat.
  
- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
  
- Ett aktivt Event Hubs-namnområde och händelsehubben som skapats genom att följa anvisningarna i [snabb start: skapa en händelsehubben med Azure Portal](event-hubs-create.md). Anteckna namn området och händelsehubben för att använda dem senare i den här genom gången. 
  
  > [!NOTE]
  > Om du redan har en lagrings behållare som ska användas kan du aktivera avbildning och välja lagrings behållaren när du skapar Händelsehubben. 
  > 
  
- Ditt Event Hubs nyckel namn och primär nyckel värde för delad åtkomst. Hitta eller skapa dessa värden under **principer för delad åtkomst** på din Event Hubs sida. Standard namnet för åtkomst nyckeln är **RootManageSharedAccessKey**. Kopiera åtkomst nyckelns namn och värdet för primär nyckel som ska användas senare i den här genom gången. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Skapa ett Azure Blob Storage-konto och-behållare

Skapa ett lagrings konto och en behållare som ska användas för avbildningen. 

1. Logga in på [Azure-portalen][Azure portal].
2. I det vänstra navigerings fältet väljer du **lagrings konton**och på skärmen **lagrings konton** väljer du **Lägg till**.
3. På skärmen skapa lagrings konto väljer du en prenumeration och en resurs grupp och ger lagrings kontot ett namn. Du kan lämna övriga val som standard. Välj **Granska + skapa**, granska inställningarna och välj sedan **skapa**. 
   
   ![Skapa lagringskonto][1]
   
4. När distributionen är klar väljer **du gå till resurs**, och på skärmen lagrings konto **Översikt** väljer du **behållare**.
5. På **behållare** -skärmen väljer du **+ behållare**. 
6. På sidan **ny behållare** ger du behållaren ett namn och väljer sedan **OK**. Anteckna behållar namnet som ska användas senare i genom gången. 
7. I det vänstra navigerings fönstret på **behållare** -skärmen väljer du **åtkomst nycklar**. Kopiera **lagrings kontots namn**och **nyckel** värdet under **KEY1**för att använda senare i genom gången.
 
## <a name="enable-event-hubs-capture"></a>Aktivera Event Hubs avbildning

1. I Azure Portal navigerar du till händelsehubben genom att välja dess Event Hubs namn område från **alla resurser**, välja **händelse hubbar** i det vänstra navigerings fältet och sedan välja händelsehubben. 
2. På **översikts** skärmen för Event Hub väljer du **avbilda händelser**.
3. På **avbildnings** skärmen väljer du **på**. Välj sedan **Välj behållare**under **Azure Storage behållare**. 
4. På **behållare** -skärmen väljer du den lagrings behållare som du vill använda och väljer sedan **Välj**. 
5. På **avbildnings** skärmen väljer du **Spara ändringar**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Skapa ett Python-skript för att skicka händelser till Event Hub
Det här skriptet skickar 200-händelser till händelsehubben. Händelserna är enkla miljö läsningar som skickas i JSON.

1. Öppna din favorit-eller python-redigerare, t. ex. [Visual Studio Code][Visual Studio Code].
2. Skapa en ny fil med namnet *Sender.py*. 
3. Klistra in följande kod i *Sender.py*. Ersätt dina egna värden för Event Hubs \<namespace >, \<AccessKeyName >, \<primary nyckel värde > och \<eventhub >.
   
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

## <a name="create-a-python-script-to-read-capture-files"></a>Skapa ett Python-skript för att läsa insamlingsfiler

Det här skriptet läser de fångade filerna och skapar en fil för var och en av dina enheter för att skriva data enbart för den enheten.

1. Skapa en ny fil med namnet *capturereader.py*i python-redigeraren. 
2. Klistra in följande kod i *capturereader.py*. Ersätt dina sparade värden för din \<storageaccount >, \<storage konto åtkomst nyckel > och \<storagecontainer >.
   
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

## <a name="run-the-python-scripts"></a>Köra Python-skript

1. Öppna en kommando tolk med python i sökvägen och kör de här kommandona för att installera de python-nödvändiga paketen:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Om du har en tidigare version av `azure-storage` eller `azure` kan du behöva använda alternativet `--upgrade`.
   
   Du kan också behöva köra följande kommando. Du behöver inte köra det här kommandot på de flesta system. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Kör följande kommando från katalogen där du sparade *Sender.py* och *capturereader.py*:
   
   ```cmd
   start python sender.py
   ```
   
   Kommandot startar en ny python-process för att köra avsändaren.
   
3. När inspelningen har slutförts kör du följande kommando:
   
   ```cmd
   python capturereader.py
   ```

   Avbildnings processorn laddar ned alla icke-tomma blobbar från lagrings konto behållaren och skriver resultatet som *CSV* -filer i den lokala katalogen. 

## <a name="next-steps"></a>Nästa steg

Mer information om Event Hubs finns i: 

* [Översikt över Event Hubs avbildning][Overview of Event Hubs Capture]
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Översikt över Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
