---
title: Azure Event Hubs Capture genomgången | Microsoft Docs
description: Exempel som använder Azure Python SDK för att visa hur man använder funktionen Event Hubs Capture.
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
ms.date: 10/05/2017
ms.author: shvija
ms.openlocfilehash: b38e64891ce1065290d46f0fae2d22e151e03e4d
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005622"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs Capture genomgång: Python

Avbildning är en funktion i Azure Event Hubs. Du kan använda den automatiskt leverera strömmande data i din event hub till en Azure Blob storage-konto valfritt. Den här funktionen gör det enkelt att utföra batchbearbetning på strömmande data i realtid. Den här artikeln beskriver hur du använder Event Hubs Capture med Python. Läs mer om Event Hubs Capture den [översiktsartikeln](event-hubs-capture-overview.md).

Det här exemplet används den [Azure Python SDK](https://azure.microsoft.com/develop/python/) att demonstrera avbildningsfunktionen. Sender.py programmet skickar simulerad telemetri i miljön till Event Hubs i JSON-format. Händelsehubben har konfigurerats för att använda avbildningsfunktionen för att skriva dessa data till Blob storage i batchar. Appen capturereader.py läser dessa blobar och skapar en Lägg till fil per enhet. Sedan skriver appen data till CSV-filer.

## <a name="what-youll-accomplish"></a>Vad du ska göra

1. Skapa ett Azure Blob storage-konto och en blob-behållare inom den, med hjälp av Azure portal.
2. Skapa ett namnområde för Event Hubs med hjälp av Azure portal.
3. Skapa en event hub med avbildningsfunktionen aktiverad med hjälp av Azure portal.
4. Skicka data till event hub med hjälp av ett Python-skript.
5. Läsa filerna fånga in och bearbeta dem med hjälp av en annan Python-skriptet.

## <a name="prerequisites"></a>Förutsättningar

- Python 2.7.x
- En Azure-prenumeration
- En aktiv [Event Hubs-namnområde och en händelsehubb](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob storage-konto
1. Logga in på [Azure Portal][Azure portal].
2. I den vänstra rutan i portalen väljer du **New** > **Storage** > **Lagringskonto**.
3. Slutför val i den **skapa lagringskonto** rutan och välj sedan **skapa**.
   
   ![Fönstret ”Skapa storage-konto”][1]
4. Efter att du ser den **distributioner lyckades** visas, Välj namnet på det nya lagringskontot och i den **Essentials** rutan och välj sedan **Blobar**. När den **Blobtjänst** -fönstret öppnas, Välj **+ behållare** högst upp. Namnge behållaren **avbilda**, och stäng sedan den **Blobtjänst** fönstret.
5. Välj **åtkomstnycklar** i den vänstra rutan och kopiera namnet på lagringskontot och värdet för **key1**. Spara det här värdet i Anteckningar eller på någon annan tillfällig plats.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Skapa en pythonskript för att skicka händelser till din event hub
1. Öppna din favoritredigerare i Python som [Visual Studio Code][Visual Studio Code].
2. Skapa ett skript som heter **sender.py**. Det här skriptet skickar 200 händelser till din event hub. De är enkla miljön information som skickas som JSON.
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
4. Uppdatera den föregående koden om du vill använda ditt namnområdesnamn och nyckelvärdet event hub-namn som du fick när du skapade Event Hubs-namnområdet.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Skapa en pythonskript för att läsa dina filer som avbildning

1. Fyll i fönstret och välj **skapa**.
2. Skapa ett skript som heter **capturereader.py**. Det här skriptet läser de hämtade filerna och skapar en fil per enhet för att skriva data endast för den enheten.
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
4. Klistra in lämpliga värden för ditt lagringskontonamn och nyckel i anropet till `startProcessing`.

## <a name="run-the-scripts"></a>Kör skripten
1. Öppna en kommandotolk med Python i dess sökväg och kör sedan följande kommandon för att installera nödvändiga Python-paket:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Om du har en tidigare version av antingen **azure-storage** eller **azure**, du kan behöva använda den **– uppgradera** alternativet.
   
   Du kan också behöva köra följande kommando (inte behövs på de flesta datorer):
   
   ```
   pip install cryptography
   ```
2. Ändra katalogen till var du sparade sender.py och capturereader.py och kör det här kommandot:
   
   ```
   start python sender.py
   ```
   
   Detta kommando startar en ny Python-process för att köra avsändaren.
3. Vänta några minuter för avbildning ska köras. Skriv sedan följande kommando i kommandofönstret ursprungliga:
   
   ```
   python capturereader.py
   ```

   Den här capture-processorn använder den lokala katalogen för att ladda ned alla blobbar från den lagringskonto/behållaren. Bearbetas av någon som inte är tomma och skriver resultatet som CSV-filer i den lokala katalogen.

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs med hjälp av följande länkar:

* [Översikt över Event Hubs Capture][Overview of Event Hubs Capture]
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Översikt av händelsehubbar][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
