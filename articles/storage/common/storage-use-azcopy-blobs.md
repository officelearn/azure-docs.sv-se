---
title: Överföra data till eller från Azure Blob-lagring med AzCopy v10 | Microsoft-dokument
description: Den här artikeln innehåller en samling AzCopy-exempelkommandon som hjälper dig att skapa behållare, kopiera filer och synkronisera kataloger mellan lokala filsystem och behållare.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 73685f124f93bb541f33b3b70727d90ce22b3cdd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263445"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Överföra data med AzCopy- och Blob-lagring

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera data till, från eller mellan lagringskonton. Den här artikeln innehåller exempelkommandon som fungerar med Blob-lagring.

> [!TIP]
> Exemplen i den här artikeln omger banargument med enstaka citattecken (''). Använd enstaka citattecken i alla kommandoskal utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe) bifogar du sökvägsargument med dubbla citattecken ("") i stället för enstaka citattecken ('').

## <a name="get-started"></a>Kom igång

Se artikeln [Kom igång med AzCopy](storage-use-azcopy-v10.md) för att hämta AzCopy och lär dig mer om hur du kan tillhandahålla auktoriseringsautentiseringsuppgifter till lagringstjänsten.

> [!NOTE]
> Exemplen i den här artikeln förutsätter att du `AzCopy login` har autentiserat din identitet med kommandot. AzCopy använder sedan ditt Azure AD-konto för att auktorisera åtkomst till data i Blob-lagring.
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till blob-data kan du lägga till den token i resurs-URL:en i varje AzCopy-kommando.
>
> Till exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Skapa en container

Du kan använda kommandot [azcopy make](storage-ref-azcopy-make.md) för att skapa en behållare. Exemplen i det här `mycontainer`avsnittet skapar en behållare med namnet .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Exempel** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Exempel** (hierarkiskt namnområde) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Detaljerad referensdokument finns i [azoskopi make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Överföra filer

Du kan använda kommandot [akoposkopikopiering](storage-ref-azcopy-copy.md) för att ladda upp filer och kataloger från den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Överför en fil
> * Ladda upp en katalog
> * Ladda upp innehållet i en katalog 
> * Ladda upp specifika filer

> [!TIP]
> Du kan justera uppladdningen med valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Ladda upp filer som Tillägg Blobbar eller Sidblobar.|**--blob-typ**=\[BlockBlob\|PageBlob\|LäggdBlob\]|
> |Ladda upp till en viss åtkomstnivå (till exempel arkivnivån).|**--block-blob-tier**=\[\|Ingen\|\|Hot Cool Arkiv\]|
> |Komprimera filer automatiskt.|**--decompress**=\[gzip\|tömma\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Överför en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Exempel** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Du kan också ladda upp en fil med hjälp av en jokerteckensymbol (*) var som helst i filsökvägen eller filnamnet. Till exempel: `'C:\myDirectory\*.txt'` `C:\my*\*.txt`eller .

### <a name="upload-a-directory"></a>Ladda upp en katalog

I det här exemplet kopieras en katalog (och alla filer i den katalogen) till en blob-behållare. Resultatet är en katalog i behållaren med samma namn.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Om du vill kopiera till en katalog i behållaren anger du bara namnet på katalogen i kommandosträngen.

|    |     |
|--------|-----------|
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Om du anger namnet på en katalog som inte finns i behållaren skapar AzCopy en ny katalog med det namnet.

### <a name="upload-the-contents-of-a-directory"></a>Ladda upp innehållet i en katalog

Du kan ladda upp innehållet i en katalog utan att kopiera själva innehållskatalogen med hjälp av jokerteckensymbolen (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Exempel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att ladda upp filer i alla underkataloger.

### <a name="upload-specific-files"></a>Ladda upp specifika filer

Du kan ange fullständiga filnamn eller använda partiella namn med jokertecken (*).

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga filnamn

Använd [kommandot askakopkopian med](storage-ref-azcopy-copy.md) `--include-path` alternativet . Separera enskilda filnamn med hjälp`;`av ett semikolon ( ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

I det här exemplet `C:\myDirectory\photos` överför AzCopy katalogen `C:\myDirectory\documents\myFile.txt` och filen. Du måste inkludera `--recursive` alternativet att överföra `C:\myDirectory\photos` alla filer i katalogen.

Du kan också utesluta `--exclude-path` filer med hjälp av alternativet. Mer information finns i referensdokument för [azkopiakopiering.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Använda jokertecken

Använd [kommandot askakopkopian med](storage-ref-azcopy-copy.md) `--include-pattern` alternativet . Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av`;`en semikolon ( ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta `--exclude-pattern` filer med hjälp av alternativet. Mer information finns i referensdokument för [azkopiakopiering.](storage-ref-azcopy-copy.md)

Alternativen `--include-pattern` `--exclude-pattern` och gäller endast för filnamn och inte på sökvägen.  Om du vill kopiera alla textfiler som finns i `–recursive` ett katalogträd använder du alternativet för `–include-pattern` att `*.txt` hämta hela katalogträdet och använder sedan och anger för att hämta alla textfiler.

## <a name="download-files"></a>Hämta filer

Du kan använda kommandot [akoposkopikopiering](storage-ref-azcopy-copy.md) för att hämta blobbar, kataloger och behållare till den lokala datorn.

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
> |Komprimera filer automatiskt.|**--decompress**=\[gzip\|tömma\]|
> |Ange hur detaljerade du vill att dina kopieringsrelaterade loggposter ska vara.|**--log-nivå**=\[\|VARNING\|\|FEL INFO NONE\]|
> |Ange om och hur du skriver över de filer och blobbar som står i konflikt vid målet.|**--skriva över**=\[\|sant\|falskt ifSourceNewer\|prompt\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Om `Content-md5` egenskapsvärdet för en blob innehåller en hash beräknar AzCopy en MD5-hash för nedladdade data och `Content-md5` verifierar att MD5-hashen som lagras i blobens egenskap matchar den beräknade hashen. Om dessa värden inte matchar misslyckas hämtningen om du `--check-md5=NoCheck` `--check-md5=LogOnly` inte åsidosätter det här beteendet genom att lägga till eller kopiera kommandot.

### <a name="download-a-file"></a>Hämta en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Ladda ner en katalog

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Det här exemplet resulterar i en katalog med namnet `C:\myDirectory\myBlobDirectory` som innehåller alla nedladdade filer.

### <a name="download-the-contents-of-a-directory"></a>Ladda ner innehållet i en katalog

Du kan hämta innehållet i en katalog utan att kopiera själva innehållskatalogen med hjälp av jokerteckensymbolen (*).

> [!NOTE]
> För närvarande stöds det här scenariot endast för konton som inte har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att hämta filer i alla underkataloger.

### <a name="download-specific-files"></a>Ladda ner specifika filer

Du kan ange fullständiga filnamn eller använda partiella namn med jokertecken (*).

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga filnamn

Använd [kommandot askakopkopian med](storage-ref-azcopy-copy.md) `--include-path` alternativet . Separera enskilda filnamn med hjälp`;`av en semikolon ( ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

I det här exemplet `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` överför AzCopy katalogen `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` och filen. Du måste inkludera `--recursive` alternativet att överföra `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` alla filer i katalogen.

Du kan också utesluta `--exclude-path` filer med hjälp av alternativet. Mer information finns i referensdokument för [azkopiakopiering.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Använda jokertecken

Använd [kommandot askakopkopian med](storage-ref-azcopy-copy.md) `--include-pattern` alternativet . Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av`;`en semikolon ( ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta `--exclude-pattern` filer med hjälp av alternativet. Mer information finns i referensdokument för [azkopiakopiering.](storage-ref-azcopy-copy.md)

Alternativen `--include-pattern` `--exclude-pattern` och gäller endast för filnamn och inte på sökvägen.  Om du vill kopiera alla textfiler som finns i `–recursive` ett katalogträd använder du alternativet för `–include-pattern` att `*.txt` hämta hela katalogträdet och använder sedan och anger för att hämta alla textfiler.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiera blobar mellan lagringskonton

Du kan använda AzCopy för att kopiera blobbar till andra lagringskonton. Kopieringen är synkron så när kommandot returneras, som anger att alla filer har kopierats. 

AzCopy använder [api:er](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [från server till server](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , så att data kopieras direkt mellan lagringsservrar. Dessa kopieringsåtgärder använder inte datorns nätverksbandbredd. Du kan öka dataflödet för dessa åtgärder `AZCOPY_CONCURRENCY_VALUE` genom att ange värdet för miljövariabeln. Mer information finns i [Optimera dataflödet](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Det här scenariot har följande begränsningar i den aktuella versionen.
>
> - Du måste lägga till en SAS-token i varje käll-URL. Om du anger behörighetsautentiseringsuppgifter med hjälp av Azure Active Directory (AD) kan du utelämna SAS-token endast från mål-URL:en.
>-  Premium-block blob lagringskonton stöder inte åtkomstnivåer. Utelämna åtkomstnivån för en blob från kopieringsåtgärden `s2s-preserve-access-tier` genom att ange till `false` (till exempel: `--s2s-preserve-access-tier=false`).

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Kopiera en blob till ett annat lagringskonto
> * Kopiera en katalog till ett annat lagringskonto
> * Kopiera en behållare till ett annat lagringskonto
> * Kopiera alla behållare, kataloger och filer till ett annat lagringskonto

De här exemplen fungerar också med konton som har ett hierarkiskt namnområde. [Med åtkomst till flera protokoll i Datasjölagring](../blobs/data-lake-storage-multi-protocol-access.md) kan`blob.core.windows.net`du använda samma URL-syntax ( ) på dessa konton.

> [!TIP]
> Du kan justera kopieringen med valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera filer som Tilläggsblobar eller sidblobar.|**--blob-typ**=\[BlockBlob\|PageBlob\|LäggdBlob\]|
> |Kopiera till en viss åtkomstnivå (till exempel arkivnivån).|**--block-blob-tier**=\[\|Ingen\|\|Hot Cool Arkiv\]|
> |Komprimera filer automatiskt.|**--decompress**=\[gzip\|tömma\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiera en blob till ett annat lagringskonto

Använd samma URL-syntax (`blob.core.windows.net`) för konton som har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiera en katalog till ett annat lagringskonto

Använd samma URL-syntax (`blob.core.windows.net`) för konton som har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exempel** (hierarkiskt namnområde)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kopiera en behållare till ett annat lagringskonto

Använd samma URL-syntax (`blob.core.windows.net`) för konton som har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exempel** (hierarkiskt namnområde)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopiera alla behållare, kataloger och blobbar till ett annat lagringskonto

Använd samma URL-syntax (`blob.core.windows.net`) för konton som har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Exempel** (hierarkiskt namnområde)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synkronisera filer

Du kan synkronisera innehållet i ett lokalt filsystem med en blob-behållare. Du kan också synkronisera behållare och virtuella kataloger med varandra. Synkronisering är enkelriktad. Med andra ord väljer du vilken av dessa två slutpunkter som är källan och vilken som är målet. Synkronisering använder också server till server-API:er. Exemplen som presenteras i det här avsnittet fungerar också med konton som har ett hierarkiskt namnområde. 

> [!NOTE]
> Den aktuella versionen av AzCopy synkroniseras inte mellan andra källor och destinationer (till exempel: Fillagring eller Amazon Web Services (AWS) S3-buckets).

[Synkroniseringskommandot](storage-ref-azcopy-sync.md) jämför filnamn och senast ändrade tidsstämplar. Ange `--delete-destination` den valfria flaggan `true` `prompt` till ett värde av eller ta bort filer i målkatalogen om dessa filer inte längre finns i källkatalogen.

Om du `--delete-destination` ställer `true` in flaggan på AzCopy tas filer bort utan att ange en uppmaning. Om du vill att en fråga ska visas innan `--delete-destination` AzCopy tar bort en fil ställer du in flaggan på `prompt`.

> [!NOTE]
> Om du vill förhindra oavsiktliga borttagningar måste du `--delete-destination=prompt|true` aktivera funktionen för mjuk [borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) innan du använder flaggan.

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

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Uppdatera en behållare med ändringar i ett lokalt filsystem

I det här fallet är behållaren målet och det lokala filsystemet är källan. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Uppdatera ett lokalt filsystem med ändringar i en behållare

I det här fallet är det lokala filsystemet målet och behållaren är källan.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Exempel** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Uppdatera en behållare med ändringar i en annan behållare

Den första behållaren som visas i det här kommandot är källan. Den andra är destinationen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Uppdatera en katalog med ändringar i en katalog i en annan filresurs

Den första katalogen som visas i det här kommandot är källan. Den andra är destinationen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i någon av dessa artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

- [Överför data med AzCopy och Amazon S3 hinkar](storage-use-azcopy-s3.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)
