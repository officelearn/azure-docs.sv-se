---
title: Överföra data till eller från Azure Files med AzCopy v10 | Microsoft Docs
description: Överföra data med AzCopy och fil lagring.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8aa0e5304825b3f016694a40b3fc1e176518237a
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526696"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Överföra data med AzCopy och fil lagring 

AzCopy är ett kommando rads verktyg som du kan använda för att kopiera blobbar eller filer till eller från ett lagrings konto. Den här artikeln innehåller exempel kommandon som fungerar med Azure Files.

Innan du börjar kan du läsa artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att hämta AzCopy och bekanta dig med verktyget.

## <a name="create-file-shares"></a>Skapa fil resurser

Du kan använda kommandot [AzCopy make](storage-ref-azcopy-make.md) för att skapa en fil resurs. Exemplet i det här avsnittet skapar en fil resurs med namnet `myfileshare`.

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd. exe). Om du använder ett Windows Command Shell (cmd. exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Exempel** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

För detaljerade referens dokument, se [AzCopy-fabrikat](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Överföra filer

Du kan använda kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) för att ladda upp filer och kataloger från den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Överför en fil
> * Ladda upp en katalog
> * Ladda upp innehållet i en katalog
> * Ladda upp en fil

> [!NOTE]
> AzCopy beräknar och lagrar inte filens MD5-hash-kod automatiskt. Om du vill att AzCopy ska göra det lägger du till flaggan `--put-md5` till varje Copy-kommando. När filen har hämtats beräknar AzCopy en MD5-hash för hämtade data och verifierar att MD5-hashen som lagras i filens `Content-md5`-egenskap matchar det beräknade hash-värdet.

För detaljerade referens dokument se [AzCopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd. exe). Om du använder ett Windows Command Shell (cmd. exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

### <a name="upload-a-file"></a>Överför en fil

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Exempel** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Du kan också ladda upp en fil med en jokertecken (*) var som helst i fil Sök vägen eller fil namnet. Till exempel: `'C:\myDirectory\*.txt'`eller `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Ladda upp en katalog

I det här exemplet kopieras en katalog (och alla filer i katalogen) till en fil resurs. Resultatet är en katalog i fil resursen med samma namn.

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Om du vill kopiera till en katalog i fil resursen anger du bara namnet på den katalogen i kommando strängen.

|    |     |
|--------|-----------|
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Om du anger namnet på en katalog som inte finns i fil resursen, skapar AzCopy en ny katalog med det namnet.

### <a name="upload-the-contents-of-a-directory"></a>Ladda upp innehållet i en katalog

Du kan ladda upp innehållet i en katalog utan att kopiera den innehåller själva katalogen med jokertecknet (*).

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Exempel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Lägg till flaggan `--recursive` för att ladda upp filer i alla under kataloger.

### <a name="upload-specific-files"></a>Ladda upp vissa filer

Du kan ange fullständiga fil namn eller använda partiella namn med jokertecken (*).

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga fil namn

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med alternativet `--include-path`. Separera enskilda fil namn genom att använda ett semikolon (`;`).

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

I det här exemplet överför AzCopy katalogen `C:\myDirectory\photos` och `C:\myDirectory\documents\myFile.txt`s filen. Du måste inkludera alternativet `--recursive` för att överföra alla filer i katalogen `C:\myDirectory\photos`.

Du kan också utesluta filer med hjälp av alternativet `--exclude-path`. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Använd jokertecken

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med alternativet `--include-pattern`. Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av en semicolin (`;`).

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta filer med hjälp av alternativet `--exclude-pattern`. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

Alternativen `--include-pattern` och `--exclude-pattern` gäller endast för fil namn och inte till sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalog träd, använder du alternativet `–recursive` för att hämta hela katalog trädet och använder sedan `–include-pattern` och anger `*.txt` för att hämta alla textfiler.

## <a name="download-files"></a>Hämta filer

Du kan använda kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) för att ladda ned filer, kataloger och fil resurser till den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Hämta en fil
> * Ladda ned en katalog
> * Hämta innehållet i en katalog
> * Hämta vissa filer

> [!NOTE]
> Om `Content-md5` egenskap svärdet för en fil innehåller en hash, beräknar AzCopy en MD5-hash för hämtade data och verifierar att MD5-hashen som lagras i filens `Content-md5`-egenskap matchar den beräknade hashen. Om dessa värden inte matchar, Miss lyckas nedladdningen om du inte åsidosätter det här beteendet genom att lägga till `--check-md5=NoCheck` eller `--check-md5=LogOnly` till kommandot Copy.

För detaljerade referens dokument se [AzCopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd. exe). Om du använder ett Windows Command Shell (cmd. exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

### <a name="download-a-file"></a>Hämta en fil

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Ladda ned en katalog

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Det här exemplet resulterar i en katalog med namnet `C:\myDirectory\myFileShareDirectory` som innehåller alla hämtade filer.

### <a name="download-the-contents-of-a-directory"></a>Hämta innehållet i en katalog

Du kan ladda ned innehållet i en katalog utan att kopiera den innehåller själva katalogen genom att använda jokertecknet (*).

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Lägg till flaggan `--recursive` för att ladda ned filer i alla under kataloger.

### <a name="download-specific-files"></a>Hämta vissa filer

Du kan ange fullständiga fil namn eller använda partiella namn med jokertecken (*).

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga fil namn

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med alternativet `--include-path`. Separera enskilda fil namn med hjälp av en semicolin (`;`).

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

I det här exemplet överför AzCopy katalogen `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` och `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`s filen. Du måste inkludera alternativet `--recursive` för att överföra alla filer i katalogen `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`.

Du kan också utesluta filer med hjälp av alternativet `--exclude-path`. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Använd jokertecken

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med alternativet `--include-pattern`. Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av en semicolin (`;`).

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta filer med hjälp av alternativet `--exclude-pattern`. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

Alternativen `--include-pattern` och `--exclude-pattern` gäller endast för fil namn och inte till sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalog träd, använder du alternativet `–recursive` för att hämta hela katalog trädet och använder sedan `–include-pattern` och anger `*.txt` för att hämta alla textfiler.

## <a name="copy-files-between-storage-accounts"></a>Kopiera filer mellan lagrings konton

Du kan använda AzCopy för att kopiera filer till andra lagrings konton. Kopierings åtgärden är synkron så när kommandot returnerar, vilket indikerar att alla filer har kopierats.

AzCopy använder [Server-till-Server-](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API: er](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url), så data kopieras direkt mellan lagrings servrar. Dessa kopierings åtgärder använder inte datorns nätverks bandbredd. Du kan öka data flödet för dessa åtgärder genom att ange värdet för `AZCOPY_CONCURRENCY_VALUE`-miljövariabeln. Mer information finns i [optimera data flöde](storage-use-azcopy-configure.md#optimize-throughput).

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Kopiera en fil till ett annat lagrings konto
> * Kopiera en katalog till ett annat lagrings konto
> * Kopiera en fil resurs till ett annat lagrings konto
> * Kopiera alla fil resurser, kataloger och filer till ett annat lagrings konto

Detaljerade referens dokument finns i [AzCopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd. exe). Om du använder ett Windows Command Shell (cmd. exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

### <a name="copy-a-file-to-another-storage-account"></a>Kopiera en fil till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiera en katalog till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Kopiera en fil resurs till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopiera alla fil resurser, kataloger och filer till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Synkronisera filer

Du kan synkronisera innehållet i en fil resurs med en annan fil resurs. Du kan också synkronisera innehållet i en katalog i en fil resurs med innehållet i en katalog som finns i en annan fil resurs. Synkroniseringen är enkelriktad. Med andra ord kan du välja vilken av dessa två slut punkter som är källan och vilken som är målet. Synkronisering använder också server-till-Server-API: er.

> [!NOTE]
> För närvarande stöds det här scenariot endast för konton som inte har ett hierarkiskt namn område. Den aktuella versionen av AzCopy synkroniseras inte mellan Azure Files och Blob Storage.

Kommandot [Sync](storage-ref-azcopy-sync.md) jämför fil namn och senaste ändrade tidsstämplar. Ange `--delete-destination` valfri flagga till värdet `true` eller `prompt` för att ta bort filer i mål katalogen om filerna inte längre finns i käll katalogen.

Om du ställer in `--delete-destination`-flaggan på `true` AzCopy tar bort filer utan att ange någon prompt. Om du vill att en prompt ska visas innan AzCopy tar bort en fil ställer du in flaggan `--delete-destination` `prompt`.

Detaljerade referens dokument finns i [AzCopy Sync](storage-ref-azcopy-sync.md).

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd. exe). Om du använder ett Windows Command Shell (cmd. exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Uppdatera en fil resurs med ändringar i en annan fil resurs

Den första fil resursen som visas i det här kommandot är källan. Den andra är målet.

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Uppdatera en katalog med ändringar i en katalog i en annan fil resurs

Den första katalogen som visas i det här kommandot är källan. Den andra är målet.

|    |     |
|--------|-----------|
| **Uttryck** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i någon av följande artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)

- [Överföra data med AzCopy och Amazon S3-buckets](storage-use-azcopy-s3.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)
