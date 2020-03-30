---
title: Utveckla för Azure-filer med Python | Microsoft-dokument
description: Lär dig hur du utvecklar Python-program och -tjänster som använder Azure-filer för att lagra fildata.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699385"
---
# <a name="develop-for-azure-files-with-python"></a>Utveckla för Azure Files med Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Den här självstudien visar grunderna i att använda Python för att utveckla program eller tjänster som använder Azure Files för att lagra fildata. I den här självstudien skapar vi ett enkelt konsolprogram och visar hur du utför grundläggande åtgärder med Python- och Azure-filer:

* Skapa Azure-filresurser
* Skapa kataloger
* Räkna upp filer och kataloger i en Azure-filresurs
* Ladda upp, ladda ned och ta bort en fil

> [!Note]  
> Eftersom Azure-filer kan nås via SMB är det möjligt att skriva enkla program som kommer åt Azure-filresursen med standardklasserna och funktionerna i Python I/O. I den här artikeln beskrivs hur du skriver program som använder Azure Storage Python SDK, som använder [AZURE Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) för att prata med Azure-filer.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Ladda ned och installera Azure Storage SDK för Python

[Azure Storage SDK för Python](https://github.com/azure/azure-storage-python) kräver Python 2.7, 3.3, 3.4, 3.5 eller 3.6.
 
## <a name="install-via-pypi"></a>Installera via PyPi

Om du vill installera via Python Package Index (PyPI) skriver du:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för Python version 0.36 eller `pip uninstall azure-storage` tidigare avinstallerar du den äldre SDK-datorn innan du installerar det senaste paketet.

Alternativa installationsmetoder finns i [Azure Storage SDK för Python på GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Visa exempelprogrammet
f Om du vill visa och köra ett exempelprogram som visar hur du använder Python med Azure-filer finns i [Azure Storage: Komma igång med Azure-filer i Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Om du vill köra exempelprogrammet kontrollerar `azure-storage-file` `azure-storage-common` du att du har installerat både paketen och paketen.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera ditt program så att det använder Azure Files
Lägg till följande längst upp i en Python-källfil där du vill programmatiskt komma åt Azure Storage.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Konfigurera en anslutning till Azure-filer 
Med `FileService` objektet kan du arbeta med resurser, kataloger och filer. Följande kod skapar `FileService` ett objekt med hjälp av lagringskontots namn och kontonyckel. Ersätt `<myaccount>` och `<mykey>` med kontonamnet och nyckeln.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
I följande kodexempel kan `FileService` du använda ett objekt för att skapa resursen om den inte finns.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Skapa en katalog
Du kan också ordna lagring genom att placera filer i underkataloger i stället för att ha dem alla i rotkatalogen. Med Azure Files kan du skapa så många kataloger som ditt konto tillåter. Koden nedan skapar en underkatalog med namnet **Sampledir** under rotkatalogen.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Räkna upp filer och kataloger i en Azure-filresurs
Om du vill visa filer och kataloger i en resurs använder du **metoden listkataloger\_\_och\_filer.** Den här metoden returnerar en generator. Följande kod matar ut **namnet** på varje fil och katalog i en resurs till konsolen.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Överför en fil 
Azure-filresurs innehåller åtminstone en rotkatalog där filer kan finnas. I det här avsnittet får du lära dig hur du laddar upp en fil från lokalt lagringsutrymme till rotkatalogen för en resurs.

Om du vill skapa en `create_file_from_path`fil `create_file_from_stream` `create_file_from_bytes` och `create_file_from_text` överföra data använder du metoderna eller metoderna . De är metoder på hög nivå som utför den nödvändiga segmenteringen när storleken på data överstiger 64 MB.

`create_file_from_path`laddar upp innehållet i en fil från `create_file_from_stream` den angivna sökvägen och överför innehållet från en redan öppnad fil/ström. `create_file_from_bytes`laddar upp en matris med `create_file_from_text` byte och överför det angivna textvärdet med den angivna kodningen (standardvärdet till UTF-8).

I följande exempel överförs innehållet i filen **sunset.png** till **myfile-filen.**

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Hämta en fil
Om du vill hämta `get_file_to_path`data `get_file_to_stream` `get_file_to_bytes`från `get_file_to_text`en fil använder du , , eller . De är metoder på hög nivå som utför den nödvändiga segmenteringen när storleken på data överstiger 64 MB.

Följande exempel visar `get_file_to_path` hur du använder för att hämta innehållet i **myfile-filen** och lagra den i filen **out-sunset.png.**

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Ta bort en fil
Slutligen, för att ta `delete_file`bort en fil, ring .

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Skapa ögonblicksbild av resurs
Du kan skapa en tidskopia av hela filresursen.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Skapa ögonblicksbild av resurs med metadata**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Lista resurser och ögonblicksbilder 
Du kan lista alla ögonblicksbilder för en viss resurs.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Bläddra bland ögonblicksbild av resurs
Du kan bläddra i innehållet i varje ögonblicksbild av resursen för att hämta filer och kataloger från den tidpunkten.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Hämta fil från ögonblicksbild av resurs
Du kan hämta en fil från en ögonblicksbild av resursen för återställningsscenariot.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Ta bort en ögonblicksbild av en enskild resurs  
Du kan ta bort en ögonblicksbild av en enda resurs.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Ta bort resurs när det finns ögonblicksbilder av resurs
Det går inte att ta bort en resurs som innehåller ögonblicksbilder om inte alla ögonblicksbilder tas bort först.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig att manipulera Azure-filer med Python följer du dessa länkar för att lära dig mer.

* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK för Python](https://github.com/Azure/azure-storage-python)
