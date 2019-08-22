---
title: Överföra data till eller från Azure Files med AzCopy v10 | Microsoft Docs
description: Överföra data med AzCopy och fil lagring.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 0c350776323c1b2949285a7ebe6a7c2778ae4dc4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648763"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Överföra data med AzCopy och fil lagring 

AzCopy är ett kommando rads verktyg som du kan använda för att kopiera blobbar eller filer till eller från ett lagrings konto. Den här artikeln innehåller exempel kommandon som fungerar med Azure Files.

Innan du börjar kan du läsa artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att hämta AzCopy och bekanta dig med verktyget.

## <a name="create-file-shares"></a>Skapa fil resurser

Du kan använda kommandot AzCopy `make` för att skapa en fil resurs. Exemplet i det här avsnittet skapar en fil resurs med `myfileshare`namnet.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Exempel** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Överföra filer

Du kan använda kommandot AzCopy `copy` för att ladda upp filer och kataloger från den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Ladda upp en fil
> * Ladda upp en katalog
> * Ladda upp filer med hjälp av jokertecken

> [!NOTE]
> AzCopy beräknar och lagrar inte filens MD5-hash-kod automatiskt. Om du vill att AzCopy ska göra det lägger `--put-md5` du till flaggan i varje Copy-kommando. När filen har hämtats beräknar AzCopy en MD5-hash för hämtade data och verifierar att MD5-hashen som lagras i filens `Content-md5` egenskap matchar det beräknade hash-värdet.

### <a name="upload-a-file"></a>Ladda upp en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Exempel** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Ladda upp en katalog

I det här exemplet kopieras en katalog (och alla filer i katalogen) till en fil resurs. Resultatet är en katalog i fil resursen med samma namn.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Exempel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Om du vill kopiera till en katalog i fil resursen anger du bara namnet på den katalogen i kommando strängen.

|    |     |
|--------|-----------|
| **Exempel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Om du anger namnet på en katalog som inte finns i fil resursen, skapar AzCopy en ny katalog med det namnet.

### <a name="upload-the-contents-of-a-directory"></a>Ladda upp innehållet i en katalog

Du kan ladda upp innehållet i en katalog utan att kopiera den innehåller själva katalogen med jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Exempel** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att ladda upp filer i alla under kataloger.

## <a name="download-files"></a>Hämta filer

Du kan använda kommandot AzCopy `copy` för att ladda ned filer, kataloger och fil resurser till den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Ladda ned en fil
> * Ladda ned en katalog
> * Ladda ned filer med hjälp av jokertecken

> [!NOTE]
> Om egenskap svärdet för en fil innehåller en hash, beräknar AzCopy en MD5-hash för hämtade data och kontrollerar att MD5-hashen som lagras i `Content-md5` filens egenskap matchar det beräknade hashvärdet. `Content-md5` Om dessa värden inte matchar, Miss lyckas nedladdningen om du inte åsidosätter det här `--check-md5=NoCheck` beteendet genom att lägga till eller `--check-md5=LogOnly` till kommandot Kopiera.

### <a name="download-a-file"></a>Ladda ned en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Exempel** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Ladda ned en katalog

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Exempel** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Det här exemplet resulterar i en katalog `C:\myDirectory\myFileShareDirectory` med namnet som innehåller alla hämtade filer.

### <a name="download-the-contents-of-a-directory"></a>Hämta innehållet i en katalog

Du kan ladda ned innehållet i en katalog utan att kopiera den innehåller själva katalogen genom att använda jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Exempel** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att ladda ned filer i alla under kataloger.

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i någon av följande artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)

- [Överföra data med AzCopy och Amazon S3-buckets](storage-use-azcopy-s3.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)