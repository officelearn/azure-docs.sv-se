---
title: Flytta Blob storage-data med AzCopy - Team Data Science Process
description: Flytta data till och från Azure Blob Storage med AzCopy
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3165aad326ad476eb5064f0b99acd8c3f5a036d8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474708"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Flytta data till och från Azure Blob Storage med AzCopy
AzCopy är ett kommandoradsverktyg som utformats för att ladda upp, ladda ned och kopiera data till och från Microsoft Azure blob-, fil- och tabellagring.

Anvisningar om hur du installerar AzCopy och ytterligare information om hur du använder med Azure-plattformen finns i [komma igång med kommandoradsverktyget Azcopy](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Om du använder VM som konfigurerats med skript som tillhandahålls av [Data Science virtuella datorer i Azure](virtual-machines.md), och sedan AzCopy är redan installerat på den virtuella datorn.
> 
> [!NOTE]
> En fullständig introduktion till Azure blob storage finns [grunderna i Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob-tjänsten](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Förutsättningar
Det här dokumentet förutsätter att du har en Azure-prenumeration, ett lagringskonto och motsvarande lagringskontots åtkomstnyckel för kontot. Innan du laddar upp/ned data, vet du ditt Azure storage-konto och kontonyckeln.

* Om du vill konfigurera en Azure-prenumeration, se [kostnadsfri utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* Mer information om hur du skapar ett lagringskonto och för att hämta konto och viktig information, se [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Kör AzCopy-kommandon
Om du vill köra AzCopy-kommandon, öppna ett kommandofönster och navigera till installationskatalogen för AzCopy på datorn där AzCopy.exe körbara finns. 

Grundläggande syntax för AzCopy-kommandon är:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Du kan lägga till installationsplatsen AzCopy i systemsökvägen och kör kommandon från en katalog. Som standard installeras AzCopy på *% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy* eller *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Ladda upp filer till en Azure-blob
Om du vill överföra en fil, använder du följande kommando:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Ladda ned filer från en Azure-blob
Om du vill hämta en fil från en Azure-blob, använder du följande kommando:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Överföra BLOB-objekt mellan Azure-behållare
För att överföra blobar mellan Azure-behållare, använder du följande kommando:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Tips för att använda AzCopy
> [!TIP]
> 1. När **överför** filer, */S* Överför filer rekursivt. Utan den här parametern laddas inte filer i undermappar.  
> 2. När **hämtar** filen */S* söker behållare rekursivt tills alla filer i den angivna katalogen och dess underkataloger eller alla filer som matchar det angivna mönstret i den angivna katalogen och dess underkataloger, laddas ned.  
> 3. Du kan inte ange en **specifika blobfil** att hämta med hjälp av den */Source* parametern. Om du vill hämta en viss fil, ange namnet på blob-filen att hämta med hjälp av den */mönstret* parametern. **/S** parametern kan användas för att leta efter ett filen namnet mönstret rekursivt AzCopy. Laddar ned alla filer i den katalogen utan mönsterparametern för AzCopy.
> 
> 

