---
title: "Flytta Data till och från Azure Blob Storage med hjälp av Python | Microsoft Docs"
description: "Flytta data till och från Azure Blob Storage med Python"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 24276252-b3dd-4edf-9e5d-f6803f8ccccc
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: d7847f695a77ad469f56a20518cb979c41384d1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Flytta data till och från Azure Blob Storage med hjälp av Python
Det här avsnittet beskriver hur du listan, ladda upp och ladda ned blobbar med Python-API. Med Python-API finns i Azure SDK, kan du:

* Skapa en behållare
* Ladda upp en blobb till en behållare
* Ladda ned blobbar
* Visa en lista över blobbarna i en behållare
* Ta bort en blob

Mer information om hur du använder Python-API finns [hur du använder tjänsten Blob Storage från Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Om du använder en virtuell dator som har konfigurerats med skript som tillhandahålls av [datavetenskap virtuella datorer i Azure](virtual-machines.md), och sedan AzCopy är redan installerad på den virtuella datorn.
> 
> [!NOTE]
> En fullständig introduktion till Azure blob storage finns i [grunderna i Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob-tjänsten](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Krav
Det här dokumentet förutsätter att du har en Azure-prenumeration, ett lagringskonto och motsvarande lagringsnyckel för det kontot. Innan du laddar upp/hämtar data, måste du känna till din Azure storage-konto och nyckel.

* Om du vill konfigurera en Azure-prenumeration finns [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett lagringskonto och för att hämta konto och viktig information, se [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).

## <a name="upload-data-to-blob"></a>Ladda upp Data till Blob
Lägg till följande kodavsnitt i den övre delen av Python-kod som du vill komma åt Azure Storage:

    from azure.storage.blob import BlobService

Den **BlobService** objekt kan du arbeta med behållare och blobbar. Följande kod skapar ett BlobService-objekt med hjälp av lagringskontots namn och åtkomstnyckel. Ersätt kontonamnet och kontonyckel med din verkliga konto och nyckel.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Använd följande metoder för att överföra data till en blob:

1. Placera\_block\_blob\_från\_sökväg (Överför innehållet i en fil från den angivna sökvägen)
2. Placera\_block_blob\_från\_fil (Överför innehållet från en redan öppnad filström)
3. Placera\_block\_blob\_från\_byte (överföringar en byte-matris)
4. Placera\_block\_blob\_från\_text (Överför det angivna textvärdet med den angivna kodningen)

Följande exempelkod överför en lokal fil till en behållare:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Följande exempelkod överför alla filer (exklusive kataloger) i en lokal katalog till blob storage:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Hämta Data från Blob
Du kan använda följande metoder för att hämta data från en blob:

1. Hämta\_blob\_till\_sökväg
2. Hämta\_blob\_till\_fil
3. Hämta\_blob\_till\_byte
4. Hämta\_blob\_till\_text

Dessa metoder som utför nödvändiga högoptimerat när storleken på data överskrider 64 MB.

Följande exempelkod hämtar innehållet i en blobb i en behållare till en lokal fil:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Följande exempelkod laddar ned alla blobbar från en behållare. Den använder listan\_BLOB-objekt om du vill hämta en lista över tillgängliga blobbar i behållaren och hämtar dem till en lokal katalog.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
