---
title: Överföra data till eller från Azure Blob Storage med hjälp av AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling av AzCopy-exempel kommandon som hjälper dig att skapa behållare, kopiera filer och synkronisera kataloger mellan lokala fil system och behållare.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/01/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 1c9c271fed094bf4777af73d588551f66f4db6f5
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512128"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Överföra data med AzCopy och Blob Storage

AzCopy är ett kommando rads verktyg som du kan använda för att kopiera data till, från eller mellan lagrings konton. Den här artikeln innehåller exempel kommandon som fungerar med Blob Storage.

> [!TIP]
> I exemplen i den här artikeln omger du Sök vägs argumenten med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

## <a name="get-started"></a>Kom igång

Se artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att ladda ned AzCopy och Läs om hur du kan ange autentiseringsuppgifter för lagrings tjänsten.

> [!NOTE]
> I exemplen i den här artikeln förutsätter vi att du har autentiserat din identitet med hjälp av `AzCopy login` kommandot. AzCopy använder sedan ditt Azure AD-konto för att ge åtkomst till data i Blob Storage.
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till BLOB-data kan du lägga till denna token i resurs-URL: en i varje AzCopy-kommando.
>
> Exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Skapa en container

Du kan använda kommandot [AzCopy make](storage-ref-azcopy-make.md) för att skapa en behållare. I exemplen i det här avsnittet skapas en behållare med namnet `mycontainer` .

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
> * Ladda upp en fil
> * Ladda upp en katalog
> * Ladda upp innehållet i en katalog 
> * Ladda upp vissa filer
> * Ladda upp en fil med index-Taggar

> [!TIP]
> Du kan ändra uppladdnings åtgärden med valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Ladda upp filer som bifogade blobar eller sidblobar.|**--BLOB-Type** = \[ BlockBlob \| PageBlob- \| AppendBlob\]|
> |Ladda upp till en viss åtkomstnivå (till exempel arkivnivå).|**--Block-Blob-Tier** = \[ Inget \| varmt \| coolt \| Arkiv\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Ladda upp en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Exempel** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Du kan också ladda upp en fil med en jokertecken (*) var som helst i fil Sök vägen eller fil namnet. Till exempel: `'C:\myDirectory\*.txt'` , eller `C:\my*\*.txt` .

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
> Lägg till `--recursive` flaggan för att ladda upp filer i alla under kataloger.

### <a name="upload-specific-files"></a>Ladda upp vissa filer

Du kan ladda upp vissa filer genom att använda fullständiga fil namn, partiella namn med jokertecken (*) eller med hjälp av datum och tider.

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga fil namn

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-path` alternativet. Separera enskilda fil namn genom att använda semikolon ( `;` ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

I det här exemplet överför AzCopy `C:\myDirectory\photos` katalogen och `C:\myDirectory\documents\myFile.txt` filen. Du måste inkludera `--recursive` alternativet för att överföra alla filer i `C:\myDirectory\photos` katalogen.

Du kan också utesluta filer med hjälp av `--exclude-path` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Använd jokertecken

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-pattern` alternativet. Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av en semicolin ( `;` ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta filer med hjälp av `--exclude-pattern` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

`--include-pattern`Alternativen och `--exclude-pattern` gäller endast för fil namn och inte till sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalog träd, använder du `–recursive` alternativet för att hämta hela katalog trädet och använder sedan `–include-pattern` och anger `*.txt` för att hämta alla textfiler.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Ladda upp filer som har ändrats efter datum och tid 

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-after` alternativet. Ange ett datum och en tid i ISO-8601-format (till exempel: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Exempel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Mer detaljerad information finns i referens dokument för [AzCopy-kopiering](storage-ref-azcopy-copy.md) .

### <a name="upload-a-file-with-index-tags"></a>Ladda upp en fil med index-Taggar

Du kan ladda upp en fil och lägga till [taggar för BLOB-index (för hands version)](../blobs/storage-manage-find-blobs.md) till mål-bloben.  

Om du använder Azure AD-auktorisering måste ditt säkerhets objekt tilldelas rollen som [ägare av lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) eller så måste den ges behörighet till `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure Resource Provider-åtgärden](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via en anpassad Azure-roll. Om du använder en SAS-token (signatur för delad åtkomst) måste denna token ge åtkomst till blobens Taggar via `t` SAS-behörigheten.

Om du vill lägga till taggar använder du `--blob-tags` alternativet tillsammans med ett URL-kodat nyckel/värde-par. Om du till exempel vill lägga till en nyckel `my tag` och ett värde `my tag value` lägger du till `--blob-tags='my%20tag=my%20tag%20value'` mål parametern. 

Avgränsa flera index-Taggar med hjälp av ett et-tecken ( `&` ).  Om du till exempel vill lägga till en nyckel `my second tag` och ett värde blir `my second tag value` den fullständiga alternativ strängen `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

I följande exempel visas hur du använder `--blob-tags` alternativet.

|    |     |
|--------|-----------|
| **Ladda upp en fil** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Ladda upp en katalog** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **Ladda upp katalog innehåll** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> Om du anger en katalog för källan, kommer alla blobar som kopieras till målet att ha samma taggar som du anger i kommandot.

## <a name="download-files"></a>Ladda ned filer

Du kan använda [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot för att ladda ned blobbar, kataloger och behållare till den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Ladda ned en fil
> * Ladda ned en katalog
> * Hämta innehållet i en katalog
> * Hämta vissa filer

> [!TIP]
> Du kan anpassa nedladdnings åtgärden genom att använda valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Expandera filer automatiskt.|**--Decompress**|
> |Ange hur detaljerad du vill att dina kopie-relaterade logg poster ska vara.|**--inloggnings nivå** = \[ VARNINGs \| fel \| information \| ingen\]|
> |Ange om och hur de konfliktskapande filerna och Blobbarna ska skrivas över vid målet.|**--Skriv över** = \[ Sant \| falskt \| ifSourceNewer- \| prompt\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Om `Content-md5` egenskap svärdet för en BLOB innehåller en hash beräknar AzCopy en MD5-hash för hämtade data och verifierar att MD5-hashen som lagras i blobens `Content-md5` egenskap matchar den beräknade hashen. Om dessa värden inte matchar, Miss lyckas nedladdningen om du inte åsidosätter det här beteendet genom att lägga till `--check-md5=NoCheck` eller `--check-md5=LogOnly` till kommandot Kopiera.

### <a name="download-a-file"></a>Ladda ned en fil

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

Du kan ladda ned innehållet i en katalog utan att kopiera själva katalogen med hjälp av jokertecknet (*).

> [!NOTE]
> För närvarande stöds det här scenariot endast för konton som inte har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att ladda ned filer i alla under kataloger.

### <a name="download-specific-files"></a>Hämta vissa filer

Du kan hämta vissa filer genom att använda fullständiga fil namn, partiella namn med jokertecken (*) eller med hjälp av datum och tider. 

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga fil namn

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-path` alternativet. Separera enskilda fil namn med hjälp av en semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

I det här exemplet överför AzCopy `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalogen och `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` filen. Du måste inkludera `--recursive` alternativet för att överföra alla filer i `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalogen.

Du kan också utesluta filer med hjälp av `--exclude-path` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Använd jokertecken

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-pattern` alternativet. Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av en semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta filer med hjälp av `--exclude-pattern` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

`--include-pattern`Alternativen och `--exclude-pattern` gäller endast för fil namn och inte till sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalog träd, använder du `–recursive` alternativet för att hämta hela katalog trädet och använder sedan `–include-pattern` och anger `*.txt` för att hämta alla textfiler.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Ladda ned filer som har ändrats efter datum och tid 

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-after` alternativet. Ange ett datum och en tid i ISO-8601-format (till exempel: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Mer detaljerad information finns i referens dokument för [AzCopy-kopiering](storage-ref-azcopy-copy.md) .

#### <a name="download-previous-versions-of-a-blob"></a>Hämta tidigare versioner av en BLOB

Om du har aktiverat [BLOB-versioner](../blobs/versioning-enable.md)kan du ladda ned en eller flera tidigare versioner av en blob. 

Skapa först en textfil som innehåller en lista med [versions-ID: n](../blobs/versioning-overview.md). Varje versions-ID måste visas på en separat rad. Exempel: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Använd sedan kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) med `--list-of-versions` alternativet. Ange platsen för text filen som innehåller listan över versioner (till exempel: `D:\\list-of-versions.txt` ).  

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

Namnet på varje Hämtad fil börjar med versions-ID följt av namnet på blobben. 

## <a name="copy-blobs-between-storage-accounts"></a>Kopiera blobar mellan lagringskonton

Du kan kopiera blobar till andra lagringskonton med hjälp av AzCopy. Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats. 

AzCopy använder [Server-till-Server-](/rest/api/storageservices/put-block-from-url) [API: er](/rest/api/storageservices/put-page-from-url), så data kopieras direkt mellan lagrings servrar. Dessa kopierings åtgärder använder inte datorns nätverks bandbredd. Du kan öka data flödet för dessa åtgärder genom att ange värdet för `AZCOPY_CONCURRENCY_VALUE` miljövariabeln. Mer information finns i [optimera data flöde](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Det här scenariot har följande begränsningar i den aktuella versionen.
>
> - Du måste lägga till en SAS-token till varje käll-URL. Om du anger autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (AD) kan du utelämna SAS-token från mål-URL: en. Kontrol lera att du har konfigurerat rätt roller i ditt mål konto. Se [alternativ 1: använd Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory).
>-  Premium Block-Blob Storage-konton stöder inte åtkomst nivåer. Utelämna åtkomst nivån för en BLOB från kopierings åtgärden genom `s2s-preserve-access-tier` att ange till `false` (till exempel: `--s2s-preserve-access-tier=false` ).

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Kopiera en blob till ett annat lagrings konto
> * Kopiera en katalog till ett annat lagrings konto
> * Kopiera en behållare till ett annat lagrings konto
> * Kopiera alla behållare, kataloger och filer till ett annat lagrings konto
> * Kopiera blobbar till ett annat lagrings konto med index Taggar

De här exemplen fungerar också med konton som har ett hierarkiskt namn område. [Med åtkomst till flera protokoll på data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) kan du använda samma URL-syntax ( `blob.core.windows.net` ) för dessa konton.

> [!TIP]
> Du kan ändra kopierings åtgärden genom att använda valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera blobbar som block, Page eller append-blobbar.|**--BLOB-Type** = \[ BlockBlob \| PageBlob- \| AppendBlob\]|
> |Kopiera till en speciell åtkomst nivå (till exempel Arkiv nivån).|**--Block-Blob-Tier** = \[ Inget \| varmt \| coolt \| Arkiv\]|
> |Expandera filer automatiskt.|**--Decompress** = \[ gzip- \| smal\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiera en blob till ett annat lagrings konto

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

> [!NOTE]
> Om käll-blobarna har index-Taggar och du vill behålla dessa taggar måste du tillämpa dem igen på mål-bloben. Information om hur du ställer in index taggar finns i avsnittet [Kopiera blobbar till ett annat lagrings konto med index Taggar](#copy-between-accounts-and-add-index-tags) i den här artikeln.  

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiera en katalog till ett annat lagrings konto

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kopiera en behållare till ett annat lagrings konto

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

> [!NOTE]
> Om käll-blobarna har index-Taggar och du vill behålla dessa taggar måste du tillämpa dem igen på mål-bloben. Information om hur du ställer in index taggar finns i avsnittet [Kopiera blobbar till ett annat lagrings konto med index Taggar](#copy-between-accounts-and-add-index-tags) i den här artikeln. 

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopiera alla behållare, kataloger och blobbar till ett annat lagrings konto

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

> [!NOTE]
> Om käll-blobarna har index-Taggar och du vill behålla dessa taggar måste du tillämpa dem igen på mål-bloben. Information om hur du ställer in index taggar finns i avsnittet **Kopiera blobbar till ett annat lagrings konto med index Taggar** nedan. 

<a id="copy-between-accounts-and-add-index-tags"></a>

### <a name="copy-blobs-to-another-storage-account-with-index-tags"></a>Kopiera blobbar till ett annat lagrings konto med index Taggar

Du kan kopiera blobar till ett annat lagrings konto och lägga till [BLOB-Taggar (för hands version)](../blobs/storage-manage-find-blobs.md) till mål-bloben.

Om du använder Azure AD-auktorisering måste ditt säkerhets objekt tilldelas rollen som [ägare av lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) eller så måste den ges behörighet till `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure Resource Provider-åtgärden](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via en anpassad Azure-roll. Om du använder en SAS-token (signatur för delad åtkomst) måste denna token ge åtkomst till blobens Taggar via `t` SAS-behörigheten.

Om du vill lägga till taggar använder du `--blob-tags` alternativet tillsammans med ett URL-kodat nyckel/värde-par. 

Om du till exempel vill lägga till en nyckel `my tag` och ett värde `my tag value` lägger du till `--blob-tags='my%20tag=my%20tag%20value'` mål parametern. 

Avgränsa flera index-Taggar med hjälp av ett et-tecken ( `&` ).  Om du till exempel vill lägga till en nyckel `my second tag` och ett värde blir `my second tag value` den fullständiga alternativ strängen `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

I följande exempel visas hur du använder `--blob-tags` alternativet.

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Katalog** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Container** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Konto** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> Om du anger en katalog, behållare eller ett konto för källan, kommer alla blobar som kopieras till målet att ha samma taggar som du anger i kommandot. 


## <a name="synchronize-files"></a>Synkronisera filer

Du kan synkronisera innehållet i ett lokalt fil system med en BLOB-behållare. Du kan också synkronisera behållare och virtuella kataloger med varandra. Synkroniseringen är enkelriktad. Med andra ord kan du välja vilken av dessa två slut punkter som är källan och vilken som är målet. Synkronisering använder också server-till-Server-API: er. Exemplen som presenteras i det här avsnittet fungerar också med konton som har ett hierarkiskt namn område. 

> [!NOTE]
> Den aktuella versionen av AzCopy synkroniseras inte mellan andra källor och destinationer (till exempel: File Storage eller Amazon Web Services (AWS) S3-buckets).

Kommandot [Sync](storage-ref-azcopy-sync.md) jämför fil namn och senaste ändrade tidsstämplar. Ange `--delete-destination` värdet för valfri flagga `true` eller `prompt` ta bort filer i mål katalogen om filerna inte längre finns i käll katalogen.

Om du ställer in `--delete-destination` flaggan på `true` AzCopy tar bort filer utan att ange någon prompt. Om du vill att en prompt ska visas innan AzCopy tar bort en fil, ställer du in `--delete-destination` flaggan på `prompt` .

> [!NOTE]
> För att förhindra oavsiktliga borttagningar, se till att aktivera funktionen för [mjuk borttagning](../blobs/soft-delete-blob-overview.md) innan du använder `--delete-destination=prompt|true` flaggan.

> [!TIP]
> Du kan anpassa din synkronisering genom att använda valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Ange hur strikta MD5-hashar ska verifieras vid hämtning.|**--kontrol lera-MD5** = \[ Nocheck \| \| FailIfDifferent \| FailIfDifferentOrMissing\]|
> |Undanta filer baserat på ett mönster.|**--Exkludera-sökväg**|
> |Ange hur detaljerad du vill att dina Sync-relaterade logg poster ska vara.|**--inloggnings nivå** = \[ VARNINGs \| fel \| information \| ingen\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Uppdatera en behållare med ändringar i ett lokalt fil system

I det här fallet är containern målet och det lokala filsystemet är källan. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Uppdatera ett lokalt filsystem med ändringar i en container

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

- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

- [Överföra data med AzCopy och Amazon S3-buckets](storage-use-azcopy-s3.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)