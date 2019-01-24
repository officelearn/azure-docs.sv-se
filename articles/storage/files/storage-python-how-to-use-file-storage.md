---
title: Utveckla för Azure Files med Python | Microsoft Docs
description: Lär dig mer om att utveckla Python-program och tjänster som använder Azure-filer för att lagra fildata.
services: storage
author: wmgries
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: tamram
ms.component: files
ms.openlocfilehash: 17b44dcaf6731f3882ed417a94532c6d26c7454d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818974"
---
# <a name="develop-for-azure-files-with-python"></a>Utveckla för Azure Files med Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Den här kursen visar grunderna i att använda Python för att utveckla program eller tjänster som använder Azure-filer för att lagra fildata. I den här självstudien ska vi skapa ett enkelt konsolprogram och visar hur du utför grundläggande åtgärder med Python och Azure Files:

* Skapa Azure-filresurser
* Skapa kataloger
* Räkna upp filer och kataloger i en Azure-filresurs
* Ladda upp, hämta och ta bort en fil

> [!Note]  
> Eftersom Azure Files kan nås över SMB, är det möjligt att skriva enkla program som har åtkomst till Azure-filresursen med hjälp av Python-i/o-standardklasser och funktioner. Den här artikeln beskriver hur du skriver program som använder Azure Storage Python SDK, som använder den [API: et för Azure Files-REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) att kommunicera med Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Ladda ned och installera Azure Storage SDK för Python

Den [Azure Storage SDK för Python](https://github.com/azure/azure-storage-python) kräver Python 2.7, 3.3, 3.4, 3.5 och 3.6.
 
## <a name="install-via-pypi"></a>Installera via PyPi

Om du vill installera via Python Package Index (PyPI), skriver du:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för Python version 0.36 eller tidigare, avinstallera den äldre SDK med hjälp av `pip uninstall azure-storage` innan du installerar det senaste paketet.

Alternativa installationsmetoder, finns det [Azure Storage SDK för Python på GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Visa exempelprogrammet
f för att visa och köra ett exempelprogram som visar hur du använder Python med Azure Files finns i [Azure Storage: Komma igång med Azure Files i Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Om du vill köra exempelprogrammet, kontrollera att du har installerat både den `azure-storage-file` och `azure-storage-common` paket.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera programmet att använda Azure Files
Lägg till följande längst upp i valfri Python-källfilen som du vill komma åt Azure Storage via programmering.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Skapa en anslutning till Azure Files 
Den `FileService` objekt kan du arbeta med resurser, kataloger och filer. Följande kod skapar en `FileService` objekt med hjälp av lagringskontots namn och åtkomstnyckel. Ersätt `<myaccount>` och `<mykey>` med kontonamnet och nyckeln.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
I följande kodexempel som du kan använda en `FileService` objektet för att skapa resursen om den inte finns.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Skapa en katalog
Du kan även sortera lagring genom att placera filer i undermappar i stället för att alla i rotkatalogen. Azure Files kan du skapa så många kataloger som ditt konto tillåter. Koden nedan skapar en underkatalog med namnet **sampledir** under rotkatalogen.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Räkna upp filer och kataloger i en Azure-filresurs
Visa filer och kataloger på en resurs genom att använda den **lista\_kataloger\_och\_filer** metod. Den här metoden returnerar en generator. I följande kod utdata i **namn** för varje fil och katalog på en resurs till konsolen.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Överför en fil 
Azure-filresurs som innehåller minst, en rotkatalog där filer kan finnas. I det här avsnittet lär du dig att överföra en fil från lokal lagring till rotkatalogen för en resurs.

Om du vill skapa en fil och ladda upp data, använda den `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` eller `create_file_from_text` metoder. De är på hög nivå metoder som utför nödvändiga storlekar när storleken på data överskrider 64 MB.

`create_file_from_path` Överför innehållet i en fil från den angivna sökvägen och `create_file_from_stream` Överför innehållet från en redan öppnad filström. `create_file_from_bytes` Överför en matris med byte, och `create_file_from_text` överför det angivna textvärdet med hjälp av den angivna kodningen (standardvärdet är UTF-8).

I följande exempel överförs innehållet i den **sunset.png** filen till den **myfile** fil.

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
Hämta data från en fil, `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, eller `get_file_to_text`. De är på hög nivå metoder som utför nödvändiga storlekar när storleken på data överskrider 64 MB.

I följande exempel visar hur du använder `get_file_to_path` att ladda ned innehållet i den **myfile** filen och lagra den till den **ut sunset.png** fil.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Ta bort en fil
Slutligen, om du vill ta bort en fil, anropa `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Skapa ögonblicksbild
Du kan skapa en punkt i tiden kopia av hela filresursen.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Skapa en ögonblicksbild av filresurs med metadata**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Lista över resurser och ögonblicksbilder 
Du kan visa alla ögonblicksbilder för en viss resurs.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Bläddra ögonblicksbild av filresurs
Du kan bläddra innehållet i varje ögonblicksbild av en resurs att hämta filer och kataloger från den tidpunkten i tid.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Hämta filen från en ögonblicksbild av filresurs
Du kan hämta en fil från en ögonblicksbild för återställning-scenariot.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Ta bort en ögonblicksbild av en enskild resurs  
Du kan ta bort en ögonblicksbild av en enskild resurs.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Ta bort resursen när ögonblicksbilder av filresurser finns
En resurs som innehåller ögonblicksbilder kan inte tas bort om inte alla ögonblicksbilder tas bort först.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur man hanterar Azure Files med Python, kan du följa dessa länkar om du vill veta mer.

* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK för Python](https://github.com/Azure/azure-storage-python)
