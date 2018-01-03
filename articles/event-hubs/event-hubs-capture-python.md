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
ms.openlocfilehash: cdbb2baea2bc6c45908369ff821c264b66053d95
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs avbilda genomgång: Python

Event Hubs avbilda är en funktion i Händelsehubbar som gör att du kan automatiskt leverera strömmande data i din händelsehubb till ett Azure Blob storage-konto valfritt. Den här funktionen gör det enkelt att utföra batchbearbetning strömning av data i realtid. Den här artikeln beskriver hur du använder Event Hubs avbilda med Python. Mer information om händelsen hubbar avbilda finns i [översiktsartikel](event-hubs-capture-overview.md).

Det här exemplet använder den [Azure Python SDK](https://azure.microsoft.com/develop/python/) att demonstrera funktionen avbildning. Sender.py skickas simulerad miljö telemetri till Händelsehubbar i JSON-format. Händelsehubben är konfigurerad för att använda funktionen avbilda dataskrivning att blob-lagring i batchar. Appen capturereader.py sedan läser dessa blobbar och skapar en append-fil per enhet och sedan skriver data till CSV-filer.

## <a name="what-will-be-accomplished"></a>Detta kommer att utföras

1. Skapa ett Azure Blob Storage-konto och en blob-behållare i den, som använder Azure portal.
2. Skapa ett namnområde som Event Hub med Azure-portalen.
3. Skapa en händelsehubb med funktionen avbilda aktiverad, med hjälp av Azure portal.
4. Skicka data till händelsehubben med Python-skriptet.
5. Läsa filerna från avbildningen och bearbeta dem med en annan Python-skriptet.

## <a name="prerequisites"></a>Förutsättningar

- Python 2.7.x
- En Azure-prenumeration
- En aktiv [Händelsehubbar namnområde och event hub.](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto
1. Logga in på [Azure portal][Azure portal].
2. I det vänstra navigeringsfönstret i portalen klickar du på **ny**, klicka på **lagring**, och klicka sedan på **Lagringskonto**.
3. Fyll i fälten i bladet storage-konto och klicka sedan på **skapa**.
   
   ![][1]
4. Efter att du ser den **distributioner lyckades** klickar du på namnet på det nya lagringskontot och i den **Essentials** bladet, klickar du på **Blobbar**. När den **Blob-tjänst** blad öppnas, klickar du på **+ behållare** längst upp. Namnet på behållaren **avbilda**sedan Stäng den **Blob-tjänst** bladet.
5. Klicka på **åtkomstnycklar** i den vänstra bladet och kopiera namnet på lagringskontot och värdet för **key1**. Spara dessa värden i anteckningar eller en tillfällig plats.

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

1. Fyll i bladet och klicka på **skapa**.
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
          #content_length == 508 is an empty file, so only process content_length > 508 i.e. skip  empty files
          if blob.properties.content_length > 508:
              print('Downloaded a non empty blob: ' + blob.name)
              cleanName = string.replace(blob.name, '/', '_')
              block_blob_service.get_blob_to_path(container, blob.name, cleanName)
              processBlob(cleanName)
              os.remove(cleanName)
          block_blob_service.delete_blob(container, blob.name)
  startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
  ```
4. Se till att klistra in lämpliga värden för lagringskontonamn och nyckel i anropet till `startProcessing`.

## <a name="run-the-scripts"></a>Kör skripten
1. Öppna en kommandotolk med Python i dess sökväg och kör sedan följande kommandon för att installera nödvändiga Python-paket:
   
  ```
  pip install azure-storage
  pip install azure-servicebus
  pip install avro
  ```
   
  Om du har en tidigare version av azure storage eller azure, kan du behöva använda de **--uppgradera** alternativet
   
  Du kan också behöva kör du följande (inte behövs på de flesta datorer):
   
  ```
  pip install cryptography
  ```
2. Ändra katalogen till överallt där du sparade sender.py och capturereader.py och kör det här kommandot:
   
  ```
  start python sender.py
  ```
   
  Detta kommando startar en ny Python-process för att köra avsändaren.
3. Nu ska du vänta några minuter för avbildningen som ska köras. Skriv följande kommando i din ursprungliga kommandofönster:
   
   ```
   python capturereader.py
   ```

   Den här avbildningen processorn använder den lokala katalogen för att hämta alla blobbar från kontot/lagringsbehållaren. Bearbetar någon som inte är tomma och skriver resultatet som CSV-filer till den lokala katalogen.

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt av Händelsehubbar avbilda][Overview of Event Hubs Capture]
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Översikt av händelsehubbar][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
