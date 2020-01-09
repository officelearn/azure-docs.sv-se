---
title: Överföra data till eller från Azure Blob Storage med hjälp av AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling av AzCopy-exempel kommandon som hjälper dig att skapa behållare, kopiera filer och synkronisera kataloger mellan lokala fil system och behållare.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 19b5635d8444c28e66bcf4c6d34f602c9914e7e4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371538"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Överföra data med AzCopy och Blob Storage

AzCopy är ett kommando rads verktyg som du kan använda för att kopiera data till, från eller mellan lagrings konton. Den här artikeln innehåller exempel kommandon som fungerar med Blob Storage.

## <a name="get-started"></a>Kom i gång

Se artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att ladda ned AzCopy och Läs om hur du kan ange autentiseringsuppgifter för lagrings tjänsten.

> [!NOTE]
> I exemplen i den här artikeln förutsätter vi att du har autentiserat din identitet med hjälp av kommandot `AzCopy login`. AzCopy använder sedan ditt Azure AD-konto för att ge åtkomst till data i Blob Storage.
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till BLOB-data kan du lägga till denna token i resurs-URL: en i varje AzCopy-kommando.
>
> Till exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>'`.

## <a name="create-a-container"></a>Skapa en container

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd. exe). Om du använder ett Windows Command Shell (cmd. exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

Du kan använda kommandot [AzCopy make](storage-ref-azcopy-make.md) för att skapa en behållare. I exemplen i det här avsnittet skapas en behållare med namnet `mycontainer`.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Exempel** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Exempel** (hierarkiskt namn område) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

För detaljerade referens dokument, se [AzCopy-fabrikat](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Ladda upp filer

Du kan använda kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) för att ladda upp filer och kataloger från den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Överför en fil
> * Ladda upp en katalog
> * Ladda upp innehållet i en katalog 
> * Ladda upp vissa filer

Detaljerade referens dokument finns i [AzCopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd. exe). Om du använder ett Windows Command Shell (cmd. exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

### <a name="upload-a-file"></a>Överför en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Exempel** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Du kan också ladda upp en fil med en jokertecken (*) var som helst i fil Sök vägen eller fil namnet. Till exempel: `'C:\myDirectory\*.txt'`eller `C:\my*\*.txt`.

> [!NOTE]
> AzCopy som standard överför data till block-blobar. Om du vill ladda upp filer som bifogade blobbar, eller Page blobbar använder du flaggan `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-directory"></a>Ladda upp en katalog

I det här exemplet kopieras en katalog (och alla filer i katalogen) till en BLOB-behållare. Resultatet är en katalog i behållaren med samma namn.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Om du vill kopiera till en katalog i behållaren anger du bara namnet på den katalogen i kommando strängen.

|    |     |
|--------|-----------|
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Om du anger namnet på en katalog som inte finns i behållaren, skapar AzCopy en ny katalog med det namnet.

### <a name="upload-the-contents-of-a-directory"></a>Ladda upp innehållet i en katalog

Du kan ladda upp innehållet i en katalog utan att kopiera den innehåller själva katalogen med jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Exempel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Lägg till flaggan `--recursive` för att ladda upp filer i alla under kataloger.

### <a name="upload-specific-files"></a>Ladda upp vissa filer

Du kan ange fullständiga fil namn eller använda partiella namn med jokertecken (*).

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga fil namn

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med alternativet `--include-path`. Separera enskilda fil namn genom att använda ett semikolon (`;`).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

I det här exemplet överför AzCopy katalogen `C:\myDirectory\photos` och `C:\myDirectory\documents\myFile.txt`s filen. Du måste inkludera alternativet `--recursive` för att överföra alla filer i katalogen `C:\myDirectory\photos`.

Du kan också utesluta filer med hjälp av alternativet `--exclude-path`. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Använd jokertecken

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med alternativet `--include-pattern`. Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av en semicolin (`;`). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta filer med hjälp av alternativet `--exclude-pattern`. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

Alternativen `--include-pattern` och `--exclude-pattern` gäller endast för fil namn och inte till sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalog träd, använder du alternativet `–recursive` för att hämta hela katalog trädet och använder sedan `–include-pattern` och anger `*.txt` för att hämta alla textfiler.

## <a name="download-files"></a>Hämta filer

Du kan använda [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot för att ladda ned blobbar, kataloger och behållare till den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Hämta en fil
> * Ladda ned en katalog
> * Hämta innehållet i en katalog
> * Hämta vissa filer

> [!NOTE]
> Om `Content-md5` egenskap svärdet för en BLOB innehåller en hash, beräknar AzCopy en MD5-hash för hämtade data och verifierar att MD5-hashen som lagras i blobens `Content-md5`-egenskap matchar den beräknade hashen. Om dessa värden inte matchar, Miss lyckas nedladdningen om du inte åsidosätter det här beteendet genom att lägga till `--check-md5=NoCheck` eller `--check-md5=LogOnly` till kommandot Copy.

Detaljerade referens dokument finns i [AzCopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd. exe). Om du använder ett Windows Command Shell (cmd. exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

### <a name="download-a-file"></a>Hämta en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Ladda ned en katalog

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Det här exemplet resulterar i en katalog med namnet `C:\myDirectory\myBlobDirectory` som innehåller alla hämtade filer.

### <a name="download-the-contents-of-a-directory"></a>Hämta innehållet i en katalog

Du kan ladda ned innehållet i en katalog utan att kopiera den innehåller själva katalogen genom att använda jokertecknet (*).

> [!NOTE]
> För närvarande stöds det här scenariot endast för konton som inte har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Lägg till flaggan `--recursive` för att ladda ned filer i alla under kataloger.

### <a name="download-specific-files"></a>Hämta vissa filer

Du kan ange fullständiga fil namn eller använda partiella namn med jokertecken (*).

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga fil namn

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med alternativet `--include-path`. Separera enskilda fil namn med hjälp av en semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

I det här exemplet överför AzCopy katalogen `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` och `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt`s filen. Du måste inkludera alternativet `--recursive` för att överföra alla filer i katalogen `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos`.

Du kan också utesluta filer med hjälp av alternativet `--exclude-path`. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Använd jokertecken

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med alternativet `--include-pattern`. Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av en semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta filer med hjälp av alternativet `--exclude-pattern`. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

Alternativen `--include-pattern` och `--exclude-pattern` gäller endast för fil namn och inte till sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalog träd, använder du alternativet `–recursive` för att hämta hela katalog trädet och använder sedan `–include-pattern` och anger `*.txt` för att hämta alla textfiler.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiera blobar mellan lagringskonton

Du kan använda AzCopy för att kopiera blobar till andra lagrings konton. Kopierings åtgärden är synkron så när kommandot returnerar, vilket indikerar att alla filer har kopierats. 

AzCopy använder [Server-till-Server-](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API: er](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url), så data kopieras direkt mellan lagrings servrar. Dessa kopierings åtgärder använder inte datorns nätverks bandbredd. Du kan öka data flödet för dessa åtgärder genom att ange värdet för `AZCOPY_CONCURRENCY_VALUE`-miljövariabeln. Mer information finns i [optimera data flöde](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Det här scenariot har följande begränsningar i den aktuella versionen.
>
> - Du måste lägga till en SAS-token till varje käll-URL. Om du anger autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (AD) kan du utelämna SAS-token från mål-URL: en.
>-  Premium Block-Blob Storage-konton stöder inte åtkomst nivåer. Utelämna åtkomst nivån för en BLOB från kopierings åtgärden genom att ange `s2s-preserve-access-tier` till `false` (till exempel: `--s2s-preserve-access-tier=false`).

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Kopiera en blob till ett annat lagrings konto
> * Kopiera en katalog till ett annat lagrings konto
> * Kopiera en behållare till ett annat lagrings konto
> * Kopiera alla behållare, kataloger och filer till ett annat lagrings konto

De här exemplen fungerar också med konton som har ett hierarkiskt namn område.

Detaljerade referens dokument finns i [AzCopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd. exe). Om du använder ett Windows Command Shell (cmd. exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiera en blob till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiera en katalog till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kopiera en behållare till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopiera alla behållare, kataloger och blobbar till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synkronisera filer

Du kan synkronisera innehållet i ett lokalt fil system med en BLOB-behållare. Du kan också synkronisera behållare och virtuella kataloger med varandra. Synkroniseringen är enkelriktad. Med andra ord kan du välja vilken av dessa två slut punkter som är källan och vilken som är målet. Synkronisering använder också server-till-Server-API: er. Exemplen som presenteras i det här avsnittet fungerar också med konton som har ett hierarkiskt namn område. 

> [!NOTE]
> Den aktuella versionen av AzCopy synkroniseras inte mellan andra källor och destinationer (till exempel: File Storage eller Amazon Web Services (AWS) S3-buckets).

Kommandot [Sync](storage-ref-azcopy-sync.md) jämför fil namn och senaste ändrade tidsstämplar. Ange `--delete-destination` valfri flagga till värdet `true` eller `prompt` för att ta bort filer i mål katalogen om filerna inte längre finns i käll katalogen.

Om du ställer in `--delete-destination`-flaggan på `true` AzCopy tar bort filer utan att ange någon prompt. Om du vill att en prompt ska visas innan AzCopy tar bort en fil ställer du in flaggan `--delete-destination` `prompt`.

> [!NOTE]
> För att förhindra oavsiktliga borttagningar, se till att aktivera funktionen för [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) innan du använder flaggan `--delete-destination=prompt|true`.

Detaljerade referens dokument finns i [AzCopy Sync](storage-ref-azcopy-sync.md).

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd. exe). Om du använder ett Windows Command Shell (cmd. exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Uppdatera en behållare med ändringar i ett lokalt fil system

I det här fallet är behållaren målet och det lokala fil systemet är källan. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Uppdatera ett lokalt fil system med ändringar i en behållare

I det här fallet är det lokala fil systemet målet och behållaren är källan.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Exempel** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Uppdatera en behållare med ändringar i en annan behållare

Den första behållaren som visas i det här kommandot är källan. Den andra är målet.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Uppdatera en katalog med ändringar i en katalog i en annan fil resurs

Den första katalogen som visas i det här kommandot är källan. Den andra är målet.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i någon av följande artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Självstudie: migrera lokala data till moln lagring med hjälp av AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)

- [Överföra data med AzCopy och Amazon S3-buckets](storage-use-azcopy-s3.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)
