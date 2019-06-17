---
title: Överföra data till eller från Azure Files med hjälp av AzCopy v10 | Microsoft Docs
description: Överföra data med AzCopy och fillagring.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 69d7136396c3d989e63b8956d3e703cc7f9666c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66687928"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Överföra data med AzCopy och fillagring 

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar och filer till eller från ett lagringskonto. Den här artikeln innehåller exempel på kommandon som fungerar med Azure Files.

Innan du kan se den [Kom igång med AzCopy](storage-use-azcopy-v10.md) artikeln om du vill hämta AzCopy och bekanta dig med verktyget.

## <a name="create-file-shares"></a>Skapa filresurser

Du kan använda AzCopy `make` kommando för att skapa en filresurs. I exemplet i det här avsnittet skapar en filresurs med namnet `myfileshare`.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Exempel** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Överföra filer

Du kan använda AzCopy `copy` kommando för att ladda upp filer och kataloger från din lokala dator.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Överför en fil
> * Ladda upp en katalog
> * Ladda upp filer med jokertecken

> [!NOTE]
> AzCopy inte automatiskt beräkna och lagra filens md5-hash-kod. Om du vill AzCopy för att göra det, lägger till den `--put-md5` flagga för att varje kopia-kommando. På så sätt kan när filen har hämtats, AzCopy beräknar en MD5-hash för nedladdade data och verifierar att MD5-hash som lagras i filens `Content-md5` -egenskap stämmer med den beräknade hashen.

### <a name="upload-a-file"></a>Överför en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Exempel** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Ladda upp en katalog

Det här exemplet kopierar en katalog (och alla filer i den katalogen) till en filresurs. Resultatet är en katalog i filresursen med samma namn.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Exempel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Kopiera till en katalog i filresursen genom att ange namnet på katalogen bara i kommandosträng.

|    |     |
|--------|-----------|
| **Exempel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Om du anger namnet på en katalog som inte finns i filresursen, skapar en ny katalog med det namnet i AzCopy.

### <a name="upload-the-contents-of-a-directory"></a>Ladda upp innehållet i en katalog

Du kan ladda upp innehållet i en katalog utan att kopiera katalogen som innehåller själva med hjälp av jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Exempel** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Lägg till den `--recursive` flagga för att ladda upp filer i alla underkataloger.

## <a name="download-files"></a>Hämta filer

Du kan använda AzCopy `copy` kommando för att hämta filer, kataloger och filen delar till din lokala dator.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Hämta en fil
> * Ladda ned en katalog
> * Ladda ned filer med jokertecken

> [!NOTE]
> Om den `Content-md5` egenskapsvärdet för en fil som innehåller ett hash-värde, AzCopy beräknar en MD5-hash för hämtade data och verifierar att MD5-hash som lagras i filens `Content-md5` -egenskap stämmer med den beräknade hashen. Om värdena inte matchar nedladdningen inte såvida du inte åsidosätter det här beteendet genom att lägga till `--check-md5=NoCheck` eller `--check-md5=LogOnly` till kopieringskommandot.

### <a name="download-a-file"></a>Hämta en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Exempel** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Ladda ned en katalog

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Exempel** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Det här exemplet resulterar i en katalog med namnet `C:\myDirectory\myFileShareDirectory` som innehåller alla hämtade filer.

### <a name="download-the-contents-of-a-directory"></a>Ladda ned innehållet i en katalog

Du kan hämta innehållet i en katalog utan att kopiera katalogen som innehåller själva med hjälp av jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Exempel** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Lägg till den `--recursive` flagga för att ladda ned filer i alla underkataloger.

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel på någon av följande artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Överföra data med AzCopy och blob storage](storage-use-azcopy-blobs.md)

- [Överföra data med AzCopy och Amazon S3 buckets](storage-use-azcopy-s3.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)