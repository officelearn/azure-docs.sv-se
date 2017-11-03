---
title: "Använda Azure Blob storage (objektlagring) från Python | Microsoft Docs"
description: Lagra ostrukturerade data i molnet med Azure Blob Storage (objektlagring).
services: storage
documentationcenter: python
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 0348e360-b24d-41fa-bb12-b8f18990d8bc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 2/24/2017
ms.author: tamram
ms.openlocfilehash: ae5ad68929a6779ed4944de82a609321a5c4b5ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-blob-storage-from-python"></a>Använda Azure Blob storage från Python
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt
Azure Blob Storage är en tjänst som lagrar ostrukturerade data i molnet som objekt/blobbar. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring.

Den här artikeln visar hur du utför vanliga scenarier med Blob storage. Exemplen är skrivna i Python och Använd den [Microsoft Azure Storage SDK för Python]. Scenarier som tas upp inkluderar överföringen, lista, hämtar och tar bort blobbar.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Hämta och installera Azure Storage SDK för Python

Azure Storage-SDK för Python kräver Python 2.7, 3.3, 3.4, 3.5 eller 3,6 och ingår i 4 olika paket: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` och `azure-storage-queue`. I den här kursen ska vi använda `azure-storage-blob` paketet.
 
### <a name="install-via-pypi"></a>Installera via PyPi

Om du vill installera via Python Package Index (PyPI), skriver du:

```bash
pip install azure-storage-blob
```


> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för Python version 0.36 eller tidigare, måste du först avinstallera med `pip uninstall azure-storage` som vi inte längre publicerar Storage SDK: N för Python i ett paket.
> 
> 

Alternativa installationsmetoder finns i [Azure Storage SDK för Python på Github](https://github.com/Azure/azure-storage-python/).

## <a name="create-a-container"></a>Skapa en behållare
Baserat på vilken typ av blob som du vill använda kan du skapa en **BlockBlobService**, **AppendBlobService**, eller **PageBlobService** objekt. I följande kod används en **BlockBlobService** objekt. Lägg till följande längst upp i en Python-fil som du vill komma åt Azure Block Blob Storage.

```python
from azure.storage.blob import BlockBlobService
```

Följande kod skapar en **BlockBlobService** objekt med lagringskontots namn och åtkomstnyckel.  Ersätt ”MITTKONTO' och 'MinNyckel' med kontonamnet och nyckeln.

```python
block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')
```

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

I följande kodexempel, kan du använda en **BlockBlobService** objekt att skapa behållaren om den inte finns.

```python
block_blob_service.create_container('mycontainer')
```

Som standard är den nya behållaren privat, så du måste ange din lagringsåtkomstnyckel (som du gjorde tidigare) för att ladda ned blobbar från den här behållaren. Du kan skapa behållaren och skicka offentliga åtkomstnivå med följande kod om du vill göra blobbar i behållaren tillgängliga för alla.

```python
from azure.storage.blob import PublicAccess
block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)
```

Du kan också ändra en behållare när du har skapat den med hjälp av följande kod.

```python
block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)
```

När alla på Internet kan se blobbar i en offentlig behållare, men du kan bara ändra eller ta bort dem.

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blobb till en behållare
Du skapar en blockblobb och ladda upp data genom att använda den **skapa\_blob\_från\_sökväg**, **skapa\_blob\_från\_dataströmmen**, **skapa\_blob\_från\_byte** eller **skapa\_blob\_från\_text** -metoderna. De är övergripande metoder som utför nödvändiga högoptimerat när storleken på data överskrider 64 MB.

**Skapa\_blob\_från\_sökväg** Överför innehållet i en fil från den angivna sökvägen och **skapa\_blob\_från\_dataströmmen**Överför innehållet från en redan öppnad filström. **Skapa\_blob\_från\_byte** överför en matris med byte och **skapa\_blob\_från\_text** överför det angivna textvärde med den angivna kodningen (standardvärdet är UTF-8).

I följande exempel Överför innehållet i den **sunset.png** filen till den **myblockblob** blob.

```python
from azure.storage.blob import ContentSettings
block_blob_service.create_blob_from_path(
    'mycontainer',
    'myblockblob',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png')
            )
```

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare
Om du vill visa blobbar i en behållare använder den **lista\_blobbar** metod. Den här metoden returnerar en generator. I följande kod utdata i **namn** för varje blobb i en behållare i konsolen.

```python
generator = block_blob_service.list_blobs('mycontainer')
for blob in generator:
    print(blob.name)
```

## <a name="download-blobs"></a>Ladda ned blobbar
Hämta data från en blob, **hämta\_blob\_till\_sökväg**, **hämta\_blob\_till\_dataströmmen**, **hämta\_blob\_till\_byte**, eller **hämta\_blob\_till\_text**. De är övergripande metoder som utför nödvändiga högoptimerat när storleken på data överskrider 64 MB.

I följande exempel visas hur du använder **hämta\_blob\_till\_sökväg** att hämta innehållet i den **myblockblob** blob och lagra den på **out sunset.png** fil.

```python
block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')
```

## <a name="delete-a-blob"></a>Ta bort en blob
Slutligen, om du vill ta bort en blobb anropa **delete_blob**.

```python
block_blob_service.delete_blob('mycontainer', 'myblockblob')
```

## <a name="writing-to-an-append-blob"></a>Skriva till en tilläggsblobb
En tilläggsblobb är optimerad för tilläggsåtgärder, t.ex loggning. Precis som en blockblobb består en tilläggsblobb av block, men när du lägger till ett nytt block till en tilläggsblobb läggs det alltid till sist i blobben. Du kan inte uppdatera eller ta bort ett befintligt block i en tilläggsblobb. En tilläggsblobbs block-ID:n exponeras inte som de gör för en blockblobb.

Blocken i en tilläggsblobb kan ha olika storlek, upp till högst 4 MB, och en tilläggsblobb kan innehålla högst 50 000 block. Den största storleken på en tilläggsblobb är alltså strax över 195 GB (4 MB × 50 000 block).

I exemplet nedan skapar vi en ny tilläggsblobb och lägger till vissa data i den för att simulera en enkel loggningsåtgärd.

```python
from azure.storage.blob import AppendBlobService
append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

# The same containers can hold all types of blobs
append_blob_service.create_container('mycontainer')

# Append blobs must be created before they are appended to
append_blob_service.create_blob('mycontainer', 'myappendblob')
append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Blob Storage kan du följa dessa länkar om du vill veta mer.

* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API för Azure Storage Services](http://msdn.microsoft.com/library/azure/dd179355)
* [Azure Storage Teamblogg]
* [Microsoft Azure Storage SDK för Python]

[Azure Storage Teamblogg]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK för Python]: https://github.com/Azure/azure-storage-python
