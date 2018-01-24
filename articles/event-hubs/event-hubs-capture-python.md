---
title: "Azure Event Hubs avbilda genomgången | Microsoft Docs"
description: "Exempel som använder Azure Python SDK för att demonstrera funktionen Event Hubs avbilda."
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 97cadbde2ddedade1a8688f1380b9ff9194613e7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs avbilda genomgång: Python

Avbildning är en funktion av Händelsehubbar i Azure. Du kan använda den för att automatiskt leverera strömmande data i din händelsehubb till ett Azure Blob storage-konto valfritt. Den här funktionen gör det enkelt att utföra batchbearbetning strömning av data i realtid. Den här artikeln beskriver hur du använder Event Hubs avbilda med Python. Mer information om händelsen hubbar avbilda finns i [översiktsartikel](event-hubs-capture-overview.md).

Det här exemplet använder den [Azure Python SDK](https://azure.microsoft.com/develop/python/) att demonstrera funktionen avbildning. Sender.py skickas simulerad miljö telemetri till Händelsehubbar i JSON-format. Händelsehubben har konfigurerats för att använda funktionen avbilda för att skriva data till Blob storage i batchar. Appen capturereader.py läser dessa blobbar och skapar en append-fil per enhet. Appen sedan skriver data i CSV-filer.

## <a name="what-youll-accomplish"></a>Vad du ska göra

1. Skapa ett Azure Blob storage-konto och en blobbbehållare i den, med hjälp av Azure portal.
2. Skapa ett namnområde som Händelsehubbar med hjälp av Azure portal.
3. Skapa en händelsehubb med funktionen avbilda aktiveras med hjälp av Azure-portalen.
4. Skicka data till händelsehubben med hjälp av Python-skriptet.
5. Läsa filerna från avbildningen och bearbeta dem med hjälp av en annan Python-skriptet.

## <a name="prerequisites"></a>Förutsättningar

- Python 2.7.x
- En Azure-prenumeration
- En aktiv [Händelsehubbar namnområde och event hub](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob storage-konto
1. Logga in på [Azure Portal][Azure portal].
2. I den vänstra rutan i portalen väljer **ny** > **lagring** > **Lagringskonto**.
3. Slutföra valen i den **skapa lagringskonto** fönstret och välj sedan **skapa**.
   
   ![”Skapa storage-konto” fönstret][1]
4. Efter att du ser den **distributioner lyckades** visas, Välj namnet på det nya lagringskontot och i den **Essentials** fönstret och välj sedan **Blobbar**. När den **Blob-tjänst** öppnas fönstret, Välj **+ behållare** längst upp. Namnet på behållaren **avbilda**, och stäng sedan den **Blob-tjänst** fönstret.
5. Välj **åtkomstnycklar** i den vänstra rutan och kopiera namnet på lagringskontot och värdet för **key1**. Spara dessa värden i anteckningar eller en tillfällig plats.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Skapa en Python-skript för att skicka händelser till din event hub
1. Öppna din favorit Python-redigerare, till exempel [Visual Studio Code][Visual Studio Code].
2. Skapa ett skript som heter **sender.py**. Det här skriptet skickar 200 händelser till din event hub. De är enkla miljön avläsningar skickas i JSON.
3. Klistra in följande kod i sender.py:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Uppdatera föregående kod för att använda din namnområdesnamnet och värdet för nyckeln händelsehubbens namn som du fick när du skapade namnområdet Händelsehubbar.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Skapa en Python-skript för att läsa Capture-filer

1. Fyll i fönstret och välj **skapa**.
2. Skapa ett skript som heter **capturereader.py**. Det här skriptet läser insamlade filer och skapar en fil per enhet för att skriva data endast för enheten.
3. Klistra in följande kod i capturereader.py:
   
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
           if not dict.has_key(parsed_json['id']):
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
       print 'Processor started using path: ' + os.getcwd()
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = string.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
   ```
4. Klistra in lämpliga värden för lagringskontonamn och nyckel i anropet till `startProcessing`.

## <a name="run-the-scripts"></a>Kör skripten
1. Öppna en kommandotolk med Python i dess sökväg och kör sedan följande kommandon för att installera nödvändiga Python-paket:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Om du har en tidigare version av antingen **azure storage** eller **azure**, du kan behöva använda det **--uppgradera** alternativet.
   
   Du kan också behöva köra kommandot (inte behövs på de flesta datorer):
   
   ```
   pip install cryptography
   ```
2. Ändra katalogen till överallt där du sparade sender.py och capturereader.py och kör det här kommandot:
   
   ```
   start python sender.py
   ```
   
   Detta kommando startar en ny Python-process för att köra avsändaren.
3. Vänta några minuter för avbildningen som ska köras. Skriv följande kommando i din ursprungliga kommandofönster:
   
   ```
   python capturereader.py
   ```

   Den här avbildningen processorn använder den lokala katalogen för att hämta alla blobbar från kontot/lagringsbehållaren. Den bearbetar någon som inte är tomma och skriver resultatet som CSV-filer i den lokala katalogen.

## <a name="next-steps"></a>Nästa steg

Mer information om Händelsehubbar med hjälp av följande länkar:

* [Översikt av Händelsehubbar avbilda][Overview of Event Hubs Capture]
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Översikt av händelsehubbar][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
