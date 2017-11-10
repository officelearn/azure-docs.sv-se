---
title: "Flytta Data till och från Azure Blob Storage med hjälp av AzCopy | Microsoft Docs"
description: "Flytta data till och från Azure Blob Storage med AzCopy"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: c309ceb2-0e83-4a07-b16d-c997dcd62d5c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 928d579da0cb469fd714b8a456a64917158d8ebc
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Flytta data till och från Azure Blob Storage med hjälp av AzCopy
AzCopy är ett kommandoradsverktyg som utformats för att ladda upp, hämtar och kopiering av data till och från Microsoft Azure blob-, fil- och tabellagring.

Anvisningar om hur du installerar AzCopy och ytterligare information om hur du använder det med Azure-plattformen finns [komma igång med kommandoradsverktyget Azcopy](../../storage/common/storage-use-azcopy.md).

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

## <a name="run-azcopy-commands"></a>Kör kommandon för AzCopy
För att köra AzCopy kommandon, öppna ett kommandofönster och gå till installationskatalogen för AzCopy på datorn där AzCopy.exe körbara finns. 

Den grundläggande syntaxen för AzCopy kommandon är:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Du kan lägga till installationsplatsen AzCopy i systemsökvägen och kör kommandon från en katalog. Som standard installeras AzCopy på *% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy* eller *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Ladda upp filer till en Azure blob
Om du vill överföra en fil, använder du följande kommando:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Hämta filer från en Azure blob
Om du vill hämta en fil från en Azure blob, använder du följande kommando:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Överföra blobbar mellan Azure-behållare
Om du vill överföra blobbar mellan Azure-behållare, använder du följande kommando:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Tips om att använda AzCopy
> [!TIP]
> 1. När **överför** filer, */S* Överför filer rekursivt. Utan den här parametern överförs inte filer i underkataloger.  
> 2. När **hämtar** filen */S* söker behållaren rekursivt tills alla filer i den angivna katalogen och dess underkataloger eller alla filer som matchar det angivna mönstret i den angivna katalogen och dess underkataloger, hämtas.  
> 3. Du kan inte ange en **specifika blob-fil** att hämta med hjälp av den */Source* parameter. Om du vill hämta en viss fil, ange filnamnet blob för att hämta med hjälp av den */mönstret* parameter. **/S** parametern kan användas för att leta efter en fil namn mönster rekursivt AzCopy. Laddar ned alla filer i katalogen utan mönsterparametern AzCopy.
> 
> 

