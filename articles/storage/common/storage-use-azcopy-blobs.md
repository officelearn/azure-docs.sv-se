---
title: Överföra data till eller från Azure Blob Storage med hjälp av AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling av AzCopy-exempel kommandon som hjälper dig att skapa behållare, kopiera filer och synkronisera kataloger mellan lokala fil system och behållare.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 2ab561c387467086a40aa6676af347a107c2c452
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641113"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Överföra data med AzCopy och Blob Storage

AzCopy är ett kommando rads verktyg som du kan använda för att kopiera data till, från eller mellan lagrings konton. Den här artikeln innehåller exempel kommandon som fungerar med Blob Storage.

## <a name="get-started"></a>Kom igång

Se artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att ladda ned AzCopy och Läs om hur du kan ange autentiseringsuppgifter för lagrings tjänsten.

> [!NOTE]
> I exemplen i den här artikeln förutsätter vi att du har autentiserat din `AzCopy login` identitet med hjälp av kommandot. AzCopy använder sedan ditt Azure AD-konto för att ge åtkomst till data i Blob Storage.
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till BLOB-data kan du lägga till denna token i resurs-URL: en i varje AzCopy-kommando.
>
> Till exempel: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Skapa en container

Du kan använda kommandot AzCopy `make` för att skapa en behållare. I exemplen i det här avsnittet skapas en `mycontainer`behållare med namnet.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Exempel** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Exempel** (hierarkiskt namn område) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Överföra filer

Du kan använda kommandot AzCopy `copy` för att ladda upp filer och kataloger från den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Ladda upp en fil
> * Ladda upp en katalog
> * Ladda upp filer med hjälp av jokertecken

> [!NOTE]
> AzCopy beräknar och lagrar inte filens MD5-hash-kod automatiskt. Om du vill att AzCopy ska göra det lägger `--put-md5` du till flaggan i varje Copy-kommando. På så sätt beräknar AzCopy en MD5-hash för nedladdade data och kontrollerar att MD5-hashen som lagras i blobens `Content-md5` egenskap matchar det beräknade hash-värdet.

### <a name="upload-a-file"></a>Ladda upp en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Exempel** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy som standard överför data till block-blobar. Använd flaggan `--blob-type=[BlockBlob|PageBlob|AppendBlob]`för att ladda upp filer som bifogade blobbar, eller Page blobbar.

### <a name="upload-a-directory"></a>Ladda upp en katalog

I det här exemplet kopieras en katalog (och alla filer i katalogen) till en BLOB-behållare. Resultatet är en katalog i behållaren med samma namn.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exempel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Om du vill kopiera till en katalog i behållaren anger du bara namnet på den katalogen i kommando strängen.

|    |     |
|--------|-----------|
| **Exempel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Om du anger namnet på en katalog som inte finns i behållaren, skapar AzCopy en ny katalog med det namnet.

### <a name="upload-the-contents-of-a-directory"></a>Ladda upp innehållet i en katalog

Du kan ladda upp innehållet i en katalog utan att kopiera den innehåller själva katalogen med jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Exempel** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att ladda upp filer i alla under kataloger.

## <a name="download-files"></a>Hämta filer

Du kan använda kommandot AzCopy `copy` för att ladda ned blobbar, kataloger och behållare till den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Ladda ned en fil
> * Ladda ned en katalog
> * Ladda ned filer med hjälp av jokertecken

> [!NOTE]
> Om egenskap svärdet för en BLOB innehåller en hash beräknar AzCopy en MD5-hash för hämtade data och verifierar att MD5-hashen som lagras i `Content-md5` blobens egenskap matchar den beräknade hashen. `Content-md5` Om dessa värden inte matchar, Miss lyckas nedladdningen om du inte åsidosätter det här `--check-md5=NoCheck` beteendet genom att lägga till eller `--check-md5=LogOnly` till kommandot Kopiera.

### <a name="download-a-file"></a>Ladda ned en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Exempel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Ladda ned en katalog

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Exempel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Det här exemplet resulterar i en katalog `C:\myDirectory\myBlobDirectory` med namnet som innehåller alla hämtade filer.

### <a name="download-the-contents-of-a-directory"></a>Hämta innehållet i en katalog

Du kan ladda ned innehållet i en katalog utan att kopiera den innehåller själva katalogen genom att använda jokertecknet (*).

> [!NOTE]
> För närvarande stöds det här scenariot endast för konton som inte har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Exempel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att ladda ned filer i alla under kataloger.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiera blobbar mellan lagrings konton

Du kan använda AzCopy för att kopiera blobar till andra lagrings konton. Kopierings åtgärden är synkron så när kommandot returnerar, vilket indikerar att alla filer har kopierats.

> [!NOTE]
> För närvarande stöds det här scenariot endast för konton som inte har ett hierarkiskt namn område. 

AzCopy använder [Server-till-Server-](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API: er](https://docs.microsoft.com/en-us/rest/api/storageservices/put-page-from-url), så data kopieras direkt mellan lagrings servrar. Dessa kopierings åtgärder använder inte datorns nätverks bandbredd.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Kopiera en blob till ett annat lagrings konto
> * Kopiera en katalog till ett annat lagrings konto
> * Kopiera en behållare till ett annat lagrings konto
> * Kopiera alla behållare, kataloger och filer till ett annat lagrings konto

> [!NOTE]
> I den aktuella versionen måste du lägga till en SAS-token till varje käll-URL. Om du anger autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (AD) kan du utelämna SAS-token från mål-URL: en. 

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiera en blob till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiera en katalog till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Kopiera en behållare till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Kopiera alla behållare, kataloger och filer till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synkronisera filer

Du kan synkronisera innehållet i ett lokalt fil system med en BLOB-behållare. Synkroniseringen är enkelriktad. Med andra ord kan du välja vilken av dessa två slut punkter som är källan och vilken som är målet.

> [!NOTE]
> För närvarande stöds det här scenariot endast för konton som inte har ett hierarkiskt namn område. Den aktuella versionen av AzCopy synkroniseras inte mellan andra källor och mål (till exempel: AWS (File Storage) eller Amazon Web Services () S3-buckets).

`sync` Kommandot jämför fil namn och senaste ändrade tidsstämplar. Ange värdet för `true` `prompt` valfri flagga eller ta bort filer i mål katalogen om filerna inte längre finns i käll katalogen. `--delete-destination`

Om du ställer in `--delete-destination` flaggan på `true` AzCopy tar bort filer utan att ange någon prompt. Om du vill att en prompt ska visas innan AzCopy tar bort en fil, `--delete-destination` ställer du `prompt`in flaggan på.

> [!NOTE]
> För att förhindra oavsiktliga borttagningar, se till att aktivera funktionen för [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) innan du använder `--delete-destination=prompt|true` flaggan.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Uppdatera en behållare med ändringar i ett lokalt fil system

I det här fallet är behållaren målet och det lokala fil systemet är källan.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exempel** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Uppdatera ett lokalt fil system med ändringar i en behållare

I det här fallet är det lokala fil systemet målet och behållaren är källan.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Exempel** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i någon av följande artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Självstudier: Migrera lokala data till moln lagring med hjälp av AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)

- [Överföra data med AzCopy och Amazon S3-buckets](storage-use-azcopy-s3.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)
