---
title: Överföra data till eller från Azure-filer med AzCopy v10 | Microsoft-dokument
description: Överför data med AzCopy och fillagring.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 59f5733009424c60f2b9c48e68d70bbc29ad7095
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263377"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Överföra data med AzCopy och fillagring 

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobbar eller filer till eller från ett lagringskonto. Den här artikeln innehåller exempelkommandon som fungerar med Azure-filer.

Innan du börjar kan du se artikeln [Kom igång med AzCopy](storage-use-azcopy-v10.md) för att hämta AzCopy och bekanta dig med verktyget.

> [!TIP]
> Exemplen i den här artikeln omger banargument med enstaka citattecken (''). Använd enstaka citattecken i alla kommandoskal utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe) bifogar du sökvägsargument med dubbla citattecken ("") i stället för enstaka citattecken ('').

## <a name="create-file-shares"></a>Skapa filresurser

Du kan använda kommandot [azcopy make](storage-ref-azcopy-make.md) för att skapa en filresurs. I exemplet i det här avsnittet `myfileshare`skapas en filresurs med namnet .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Exempel** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Detaljerad referensdokument finns i [azoskopi make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Överföra filer

Du kan använda kommandot [akoposkopikopiering](storage-ref-azcopy-copy.md) för att ladda upp filer och kataloger från den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Överför en fil
> * Ladda upp en katalog
> * Ladda upp innehållet i en katalog
> * Ladda upp en viss fil

> [!TIP]
> Du kan justera uppladdningen med valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomstkontrollistor (ACL: er) tillsammans med filerna.|**--bevara-smb-behörigheter**=\[\|sant falskt\]|
> |Kopiera SMB-egenskapsinformation tillsammans med filerna.|**--bevara-smb-info**=\[\|sant falskt\]|
> |Ladda upp filer som Tillägg Blobbar eller Sidblobar.|**--blob-typ**=\[BlockBlob\|PageBlob\|LäggdBlob\]|
> |Ladda upp till en viss åtkomstnivå (till exempel arkivnivån).|**--block-blob-tier**=\[\|Ingen\|\|Hot Cool Arkiv\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy beräknar inte automatiskt och lagrar filens md5-hash-kod. Om du vill att AzCopy ska `--put-md5` göra det lägger du till flaggan i varje kopieringskommando. På så sätt, när filen hämtas, beräknar AzCopy en MD5 hash för nedladdade data och `Content-md5` verifierar att MD5-hash som lagras i filens egenskap matchar den beräknade hashen.

### <a name="upload-a-file"></a>Överför en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Exempel** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Du kan också ladda upp en fil med hjälp av en jokerteckensymbol (*) var som helst i filsökvägen eller filnamnet. Till exempel: `'C:\myDirectory\*.txt'` `C:\my*\*.txt`eller .

### <a name="upload-a-directory"></a>Ladda upp en katalog

I det här exemplet kopieras en katalog (och alla filer i katalogen) till en filresurs. Resultatet är en katalog i filresursen med samma namn.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Om du vill kopiera till en katalog i filresursen anger du bara namnet på katalogen i kommandosträngen.

|    |     |
|--------|-----------|
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Om du anger namnet på en katalog som inte finns i filresursen skapar AzCopy en ny katalog med det namnet.

### <a name="upload-the-contents-of-a-directory"></a>Ladda upp innehållet i en katalog

Du kan ladda upp innehållet i en katalog utan att kopiera själva innehållskatalogen med hjälp av jokerteckensymbolen (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Exempel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att ladda upp filer i alla underkataloger.

### <a name="upload-specific-files"></a>Ladda upp specifika filer

Du kan ange fullständiga filnamn eller använda partiella namn med jokertecken (*).

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga filnamn

Använd [kommandot askakopkopian med](storage-ref-azcopy-copy.md) `--include-path` alternativet . Separera enskilda filnamn med hjälp`;`av ett semikolon ( ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

I det här exemplet `C:\myDirectory\photos` överför AzCopy katalogen `C:\myDirectory\documents\myFile.txt` och filen. Du måste inkludera `--recursive` alternativet att överföra `C:\myDirectory\photos` alla filer i katalogen.

Du kan också utesluta `--exclude-path` filer med hjälp av alternativet. Mer information finns i referensdokument för [azkopiakopiering.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Använda jokertecken

Använd [kommandot askakopkopian med](storage-ref-azcopy-copy.md) `--include-pattern` alternativet . Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av`;`en semikolon ( ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta `--exclude-pattern` filer med hjälp av alternativet. Mer information finns i referensdokument för [azkopiakopiering.](storage-ref-azcopy-copy.md)

Alternativen `--include-pattern` `--exclude-pattern` och gäller endast för filnamn och inte på sökvägen.  Om du vill kopiera alla textfiler som finns i `–recursive` ett katalogträd använder du alternativet för `–include-pattern` att `*.txt` hämta hela katalogträdet och använder sedan och anger för att hämta alla textfiler.

## <a name="download-files"></a>Hämta filer

Du kan använda kommandot [akoposkopikopiering](storage-ref-azcopy-copy.md) för att hämta filer, kataloger och filresurser till den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Hämta en fil
> * Ladda ner en katalog
> * Ladda ner innehållet i en katalog
> * Ladda ner specifika filer

> [!TIP]
> Du kan justera hämtningen med valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomstkontrollistor (ACL: er) tillsammans med filerna.|**--bevara-smb-behörigheter**=\[\|sant falskt\]|
> |Kopiera SMB-egenskapsinformation tillsammans med filerna.|**--bevara-smb-info**=\[\|sant falskt\]|
> |Komprimera filer automatiskt.|**--decompress**=\[gzip\|tömma\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Om `Content-md5` egenskapsvärdet för en fil innehåller en hash beräknar AzCopy en MD5-hash för nedladdade data och `Content-md5` verifierar att MD5-hashen som lagras i filens egenskap matchar den beräknade hashen. Om dessa värden inte matchar misslyckas hämtningen om du `--check-md5=NoCheck` `--check-md5=LogOnly` inte åsidosätter det här beteendet genom att lägga till eller kopiera kommandot.

### <a name="download-a-file"></a>Hämta en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Ladda ner en katalog

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Det här exemplet resulterar i en katalog med namnet `C:\myDirectory\myFileShareDirectory` som innehåller alla nedladdade filer.

### <a name="download-the-contents-of-a-directory"></a>Ladda ner innehållet i en katalog

Du kan hämta innehållet i en katalog utan att kopiera själva innehållskatalogen med hjälp av jokerteckensymbolen (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att hämta filer i alla underkataloger.

### <a name="download-specific-files"></a>Ladda ner specifika filer

Du kan ange fullständiga filnamn eller använda partiella namn med jokertecken (*).

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga filnamn

Använd [kommandot askakopkopian med](storage-ref-azcopy-copy.md) `--include-path` alternativet . Separera enskilda filnamn med hjälp`;`av en semikolon ( ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

I det här exemplet `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` överför AzCopy katalogen `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` och filen. Du måste inkludera `--recursive` alternativet att överföra `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` alla filer i katalogen.

Du kan också utesluta `--exclude-path` filer med hjälp av alternativet. Mer information finns i referensdokument för [azkopiakopiering.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Använda jokertecken

Använd [kommandot askakopkopian med](storage-ref-azcopy-copy.md) `--include-pattern` alternativet . Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av`;`en semikolon ( ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta `--exclude-pattern` filer med hjälp av alternativet. Mer information finns i referensdokument för [azkopiakopiering.](storage-ref-azcopy-copy.md)

Alternativen `--include-pattern` `--exclude-pattern` och gäller endast för filnamn och inte på sökvägen.  Om du vill kopiera alla textfiler som finns i `–recursive` ett katalogträd använder du alternativet för `–include-pattern` att `*.txt` hämta hela katalogträdet och använder sedan och anger för att hämta alla textfiler.

## <a name="copy-files-between-storage-accounts"></a>Kopiera filer mellan lagringskonton

Du kan använda AzCopy för att kopiera filer till andra lagringskonton. Kopieringen är synkron så när kommandot returneras, som anger att alla filer har kopierats.

AzCopy använder [api:er](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [från server till server](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , så att data kopieras direkt mellan lagringsservrar. Dessa kopieringsåtgärder använder inte datorns nätverksbandbredd. Du kan öka dataflödet för dessa åtgärder `AZCOPY_CONCURRENCY_VALUE` genom att ange värdet för miljövariabeln. Mer information finns i [Optimera dataflödet](storage-use-azcopy-configure.md#optimize-throughput).

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Kopiera en fil till ett annat lagringskonto
> * Kopiera en katalog till ett annat lagringskonto
> * Kopiera en filresurs till ett annat lagringskonto
> * Kopiera alla filresurser, kataloger och filer till ett annat lagringskonto

> [!TIP]
> Du kan justera kopieringen med valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomstkontrollistor (ACL: er) tillsammans med filerna.|**--bevara-smb-behörigheter**=\[\|sant falskt\]|
> |Kopiera SMB-egenskapsinformation tillsammans med filerna.|**--bevara-smb-info**=\[\|sant falskt\]|
> |Kopiera filer som Tilläggsblobar eller sidblobar.|**--blob-typ**=\[BlockBlob\|PageBlob\|LäggdBlob\]|
> |Kopiera till en viss åtkomstnivå (till exempel arkivnivån).|**--block-blob-tier**=\[\|Ingen\|\|Hot Cool Arkiv\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Kopiera en fil till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiera en katalog till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Kopiera en filresurs till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopiera alla filresurser, kataloger och filer till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Synkronisera filer

Du kan synkronisera innehållet i en filresurs med en annan filresurs. Du kan också synkronisera innehållet i en katalog i en filresurs med innehållet i en katalog som finns i en annan filresurs. Synkronisering är enkelriktad. Med andra ord väljer du vilken av dessa två slutpunkter som är källan och vilken som är målet. Synkronisering använder också server till server-API:er.

> [!NOTE]
> För närvarande stöds det här scenariot endast för konton som inte har ett hierarkiskt namnområde. Den aktuella versionen av AzCopy synkroniseras inte mellan Azure Files och Blob Storage.

[Synkroniseringskommandot](storage-ref-azcopy-sync.md) jämför filnamn och senast ändrade tidsstämplar. Ange `--delete-destination` den valfria flaggan `true` `prompt` till ett värde av eller ta bort filer i målkatalogen om dessa filer inte längre finns i källkatalogen.

Om du `--delete-destination` ställer `true` in flaggan på AzCopy tas filer bort utan att ange en uppmaning. Om du vill att en fråga ska visas innan `--delete-destination` AzCopy tar bort en fil ställer du in flaggan på `prompt`.

> [!TIP]
> Du kan justera synkroniseringen med valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Ange hur strikt MD5-hashar ska valideras vid nedladdning.|**--check-md5**=\[NoCheck LogOnly FailIfDifferent FailIfDifferentOrMissing --check-md5 NoCheck LogOnly FailIfDifferent FailIfDifferentOrMissing --check-md5 NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentOrMissing --\]|
> |Exkludera filer baserat på ett mönster.|**--exclude-path**|
> |Ange hur detaljerade du vill att dina synkroniseringsrelaterade loggposter ska vara.|**--log-nivå**=\[\|VARNING\|\|FEL INFO NONE\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Uppdatera en filresurs med ändringar i en annan filresurs

Den första filresursen som visas i det här kommandot är källan. Den andra är destinationen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Uppdatera en katalog med ändringar i en katalog i en annan filresurs

Den första katalogen som visas i det här kommandot är källan. Den andra är destinationen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Uppdatera en filresurs för att matcha innehållet i en ögonblicksbild av resursen

Den första filresursen som visas i det här kommandot är källan. I slutet av URI lägger `&sharesnapshot=` du till strängen följt av **DateTime-värdet** för ögonblicksbilden. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

Mer information om ögonblicksbilder av resurs finns i [Översikt över ögonblicksbilder av resurs för Azure-filer](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files).

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i någon av dessa artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Överföra data med AzCopy- och bloblagring](storage-use-azcopy-blobs.md)

- [Överför data med AzCopy och Amazon S3 hinkar](storage-use-azcopy-s3.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)
