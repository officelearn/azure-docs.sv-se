---
title: Utveckla för Azure Files med python | Microsoft Docs
description: Lär dig hur du utvecklar python-program och-tjänster som använder Azure Files för att lagra fildata.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.custom: tracking-python
ms.openlocfilehash: d04b158761d501d4a79d91fe551670b57f9847f3
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678723"
---
# <a name="develop-for-azure-files-with-python"></a>Utveckla för Azure Files med Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

I den här självstudien demonstreras grunderna för att använda python för att utveckla program eller tjänster som använder Azure Files för att lagra fildata. I den här självstudien skapar vi ett enkelt konsol program och visar hur du utför grundläggande åtgärder med python och Azure Files:

* Skapa Azure-filresurser
* Skapa kataloger
* Räkna upp filer och kataloger i en Azure-filresurs
* Ladda upp, ladda ned och ta bort en fil

> [!Note]  
> Eftersom Azure Files kan nås via SMB, är det möjligt att skriva enkla program som har åtkomst till Azure-filresursen med hjälp av standard-I/O-klasser och-funktioner. Den här artikeln beskriver hur du skriver program som använder Azure Storage python SDK, som använder [Azure Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) för att prata med Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Hämta och installera Azure Storage SDK för python

[Azure Storage SDK för python](https://github.com/azure/azure-storage-python) kräver python 2,7, 3,3, 3,4, 3,5 eller 3,6.
 
## <a name="install-via-pypi"></a>Installera via PyPi

Om du vill installera via python-paket indexet (PyPI) skriver du:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för python version 0,36 eller tidigare, måste du avinstallera den äldre SDK: n med `pip uninstall azure-storage` innan du installerar det senaste paketet.

Alternativa installations metoder finns i [Azure Storage SDK för python på GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Visa exempel programmet
Om du vill visa och köra ett exempel program som visar hur du använder python med Azure Files, se [Azure Storage: komma igång med Azure Files i python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Kontrol lera att du har installerat både-och-paketen för att köra exempel programmet `azure-storage-file` `azure-storage-common` .

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera ditt program så att det använder Azure Files
Lägg till följande längst upp i en python-källfil som du vill ha åtkomst till Azure Storage via programmering.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Konfigurera en anslutning till Azure Files 
`FileService`Med objektet kan du arbeta med resurser, kataloger och filer. Följande kod skapar ett- `FileService` objekt med hjälp av lagrings kontots namn och konto nyckeln. Ersätt `<myaccount>` och `<mykey>` med kontonamnet och nyckeln.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
I följande kod exempel kan du använda ett `FileService` objekt för att skapa resursen om den inte finns.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Skapa en katalog
Du kan också organisera lagring genom att lägga till filer i underordnade kataloger i stället för att ha alla i rot katalogen. Med Azure Files kan du skapa så många kataloger som ditt konto kommer att tillåta. Koden nedan skapar en under katalog med namnet **sampledir** under rot katalogen.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Räkna upp filer och kataloger i en Azure-filresurs
Om du vill visa en lista över filer och kataloger i en resurs använder du metoden **lista \_ kataloger \_ och \_ filer** . Den här metoden returnerar en generator. Följande kod matar ut **namnet** på varje fil och katalog i en resurs till-konsolen.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Överför en fil 
Azure-filresursen innehåller minst en rot katalog där filerna kan finnas. I det här avsnittet får du lära dig hur du laddar upp en fil från lokal lagring till rot katalogen för en resurs.

Använd `create_file_from_path` `create_file_from_stream` metoderna,, eller för att skapa en fil och överföra `create_file_from_bytes` data `create_file_from_text` . De är avancerade metoder som utför den nödvändiga delningen när storleken på data överskrider 64 MB.

`create_file_from_path`överför innehållet i en fil från den angivna sökvägen och `create_file_from_stream` laddar upp innehållet från en redan öppnad fil/data ström. `create_file_from_bytes`laddar upp en matris med byte och `create_file_from_text` laddar upp det angivna textvärdet med den angivna kodningen (Standardvärdet är UTF-8).

I följande exempel överförs innehållet i **sunset.png** -filen **till filen.**

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
Använd,, eller för att hämta data från en fil `get_file_to_path` `get_file_to_stream` `get_file_to_bytes` `get_file_to_text` . De är avancerade metoder som utför den nödvändiga delningen när storleken på data överskrider 64 MB.

I följande exempel visas hur `get_file_to_path` du hämtar innehållet i **filen i** filen och lagrar det i **out-sunset.png** -filen.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Ta bort en fil
Ta slutligen bort en fil genom att anropa `delete_file` .

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Skapa resurs ögonblicks bild
Du kan skapa en tidpunkts kopia av hela fil resursen.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Skapa resurs ögonblicks bild med metadata**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Lista över resurser och ögonblicks bilder 
Du kan visa en lista över alla ögonblicks bilder för en viss resurs.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Bläddra i resurs ögonblicks bild
Du kan bläddra igenom innehållet i varje resurs ögonblicks bild för att hämta filer och kataloger från den tidpunkten.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Hämta fil från resurs ögonblicks bild
Du kan ladda ned en fil från en resurs ögonblicks bild för ditt återställnings scenario.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Ta bort en ögonblicks bild av en enskild resurs  
Du kan ta bort en ögonblicks bild av en enskild resurs.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Ta bort resurs när resurs ögonblicks bilder finns
Det går inte att ta bort en resurs som innehåller ögonblicks bilder om inte alla ögonblicks bilder tas bort först.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du manipulerar Azure Files med python kan du följa dessa länkar om du vill veta mer.

* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK för python](https://github.com/Azure/azure-storage-python)
