---
title: "Utveckla för Azure-filer med Python | Microsoft Docs"
description: "Lär dig hur du utvecklar Python-program och tjänster som använder Azure-filer för att lagra fildata."
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/19/2017
ms.author: tamram
ms.openlocfilehash: f4d3d6a7c6b16ca9c6f1ae7e73b57103a5ae4844
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="develop-for-azure-files-with-python"></a>Utveckla för Azure-filer med Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Den här kursen visar grunderna i Python för att utveckla program eller tjänster som använder Azure-filer för att lagra fildata. I den här kursen ska vi skapa ett enkelt konsolprogram och visar hur du utför grundläggande åtgärder med Python och Azure-filer:

* Skapa Azure-filresurser
* Skapa kataloger
* Räkna upp filer och kataloger i en filresurs på Azure
* Ladda upp, hämta och ta bort en fil

> [!Note]  
> Eftersom Azure-filer kan nås över SMB, är det möjligt att skriva enkla program som har åtkomst till Azure filresursen med standard Python-i/o-klasser och funktioner. Den här artikeln beskriver hur du skriver program som använder Azure Storage Python SDK, som använder den [Azure filer REST API](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/file-service-rest-api) tala med Azure-filer.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Hämta och installera Azure Storage SDK för Python

Azure Storage-SDK för Python kräver Python 2.7, 3.3, 3.4, 3.5 eller 3,6 och ingår i 4 olika paket: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` och `azure-storage-queue`. I den här kursen ska vi använda `azure-storage-file` paketet.
 
## <a name="install-via-pypi"></a>Installera via PyPi

Om du vill installera via Python Package Index (PyPI), skriver du:

```bash
pip install azure-storage-file
```


> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för Python version 0.36 eller tidigare, måste du först avinstallera med `pip uninstall azure-storage` som vi inte längre publicerar Storage SDK: N för Python i ett paket.
> 
> 

Alternativa installationsmetoder finns i [Azure Storage SDK för Python på Github](https://github.com/Azure/azure-storage-python/).

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera programmet så att Azure-filer
Lägg till följande längst upp i alla Python-källfilen som du vill komma åt Azure Storage.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Konfigurera en anslutning till Azure-filer 
Den `FileService` objektet kan du arbeta med resurser, kataloger och filer. Följande kod skapar en `FileService` objekt med lagringskontots namn och åtkomstnyckel. Ersätt `<myaccount>` och `<mykey>` med kontonamnet och nyckeln.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Skapa en filresurs på Azure
I följande kodexempel, kan du använda en `FileService` objekt att skapa resursen om den inte finns.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Skapa en katalog
Du kan även sortera lagring genom att lägga till filer i underkataloger i stället för att alla i rotkatalogen. Azure Files kan du skapa så många kataloger som ditt konto tillåter. Koden nedan för att skapa en underkatalog med namnet **sampledir** under rotkatalogen.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Räkna upp filer och kataloger i en filresurs på Azure
Om du vill visa en lista över filer och kataloger på en resurs, Använd den **lista\_kataloger\_och\_filer** metod. Den här metoden returnerar en generator. I följande kod utdata i **namn** av varje fil- och på en resurs i konsolen.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Överför en fil 
Azure File resursen innehåller minst, en rotkatalog där filer kan finnas. I det här avsnittet lär du dig hur du överför en fil från lokal lagring till rotkatalogen för en resurs.

Om du vill skapa en fil och överför data använder den `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` eller `create_file_from_text` metoder. De är övergripande metoder som utför nödvändiga högoptimerat när storleken på data överskrider 64 MB.

`create_file_from_path`Överför innehållet i en fil från den angivna sökvägen och `create_file_from_stream` Överför innehållet från en redan öppnad filström. `create_file_from_bytes`Överför en matris med byte och `create_file_from_text` överför det angivna textvärdet med den angivna kodningen (standardvärdet är UTF-8).

I följande exempel Överför innehållet i den **sunset.png** filen till den **minfil** fil.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Hämta en fil
Hämta data från en fil, `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, eller `get_file_to_text`. De är övergripande metoder som utför nödvändiga högoptimerat när storleken på data överskrider 64 MB.

I följande exempel visas hur du använder `get_file_to_path` att hämta innehållet i den **minfil** filen och spara den till den **out sunset.png** fil.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Ta bort en fil
Slutligen, om du vill ta bort en fil, anropa `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot-preview"></a>Skapa resursen ögonblicksbild (förhandsgranskning)
Du kan skapa en punkt i tiden kopia av hela filresursen.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Skapa resursen ögonblicksbild med metadata**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Lista över resurser och ögonblicksbilder 
Du kan visa alla ögonblicksbilder för en viss resurs.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Bläddra dela ögonblicksbild
Du kan bläddra innehållet i varje resurs ögonblicksbild för att hämta filer och kataloger från den tidpunkten i tid.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Hämta filen från resursen ögonblicksbild
Du kan hämta en fil från en ögonblicksbild av en resurs för återställning-scenariot.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Ta bort en ögonblicksbild av en enskild resurs  
Du kan ta en ögonblicksbild av en enskild resurs.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Ta bort resursen när det finns ögonblicksbilder för resursen
En resurs som innehåller ögonblicksbilder kan inte tas bort om inte alla ögonblicksbilder tas bort först.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du hanterar Azure-filer med Python, kan du följa dessa länkar om du vill veta mer.

* [Python Developer Center](/develop/python/)
* [REST-API för Azure Storage Services](http://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK för Python](https://github.com/Azure/azure-storage-python)