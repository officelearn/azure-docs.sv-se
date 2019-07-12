---
title: Överföra data till eller från Azure Blob storage med hjälp av AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling av AzCopy exempel kommandon som hjälper dig att skapa behållare, kopiera filer och synkronisera kataloger mellan lokala filsystem och behållare.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 83e32a1e8f77604330a9f3aba0e011a0a0851e2f
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625611"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Överföra data med AzCopy och Blob storage

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera data till, från eller mellan lagringskonton. Den här artikeln innehåller exempel på kommandon som fungerar med Blob storage.

## <a name="get-started"></a>Kom igång

Se den [Kom igång med AzCopy](storage-use-azcopy-v10.md) artikeln om du vill hämta AzCopy och lär dig mer om sätt att du kan ange autentiseringsuppgifter till storage-tjänsten.

> [!NOTE]
> Exemplen i den här artikeln förutsätter att du har autentiserats din identitet med hjälp av den `AzCopy login` kommando. AzCopy använder sedan din Azure AD-konto för att bevilja åtkomst till data i Blob storage.
>
> Om du föredrar att använda en SAS-token för att bevilja åtkomst till blob-data, kan du lägga till den token i resurs-URL: en i varje AzCopy-kommandot.
>
> Till exempel: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Skapa en container

Du kan använda AzCopy `make` kommando för att skapa en behållare. Exemplen i det här avsnittet Skapa en behållare med namnet `mycontainer`.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Exempel** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Exempel** (hierarkiskt namnområde) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Överföra filer

Du kan använda AzCopy `copy` kommando för att ladda upp filer och kataloger från din lokala dator.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Överför en fil
> * Ladda upp en katalog
> * Ladda upp filer med jokertecken

> [!NOTE]
> AzCopy inte automatiskt beräkna och lagra filens md5-hash-kod. Om du vill AzCopy för att göra det, lägger till den `--put-md5` flagga för att varje kopia-kommando. På så sätt kan när blobben som har hämtats, AzCopy beräknar en MD5-hash för nedladdade data och verifierar att MD5-hash som lagras i blobens `Content-md5` -egenskap stämmer med den beräknade hashen.

### <a name="upload-a-file"></a>Överför en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Exempel** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy som standard överför data till blockblobar. Ladda upp filer som Tilläggsblobbar och Sidblobbar använder du flaggan `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-directory"></a>Ladda upp en katalog

Det här exemplet kopierar en katalog (och alla filer i den katalogen) till en blob-behållare. Resultatet är en katalog i behållaren med samma namn.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exempel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Kopiera till en katalog i behållaren genom att ange namnet på katalogen bara i kommandosträng.

|    |     |
|--------|-----------|
| **Exempel** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Om du anger namnet på en katalog som inte finns i behållaren, skapar en ny katalog med det namnet i AzCopy.

### <a name="upload-the-contents-of-a-directory"></a>Ladda upp innehållet i en katalog

Du kan ladda upp innehållet i en katalog utan att kopiera katalogen som innehåller själva med hjälp av jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Exempel** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Lägg till den `--recursive` flagga för att ladda upp filer i alla underkataloger.

## <a name="download-files"></a>Hämta filer

Du kan använda AzCopy `copy` kommando för att hämta blobs, kataloger och behållare till den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Hämta en fil
> * Ladda ned en katalog
> * Ladda ned filer med jokertecken

> [!NOTE]
> Om den `Content-md5` egenskapsvärdet för en blob innehåller ett hash-värde, AzCopy beräknar en MD5-hash för hämtade data och verifierar att MD5-hash som lagras i blobens `Content-md5` -egenskap stämmer med den beräknade hashen. Om värdena inte matchar nedladdningen inte såvida du inte åsidosätter det här beteendet genom att lägga till `--check-md5=NoCheck` eller `--check-md5=LogOnly` till kopieringskommandot.

### <a name="download-a-file"></a>Hämta en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Exempel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Ladda ned en katalog

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Exempel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Det här exemplet resulterar i en katalog med namnet `C:\myDirectory\myBlobDirectory` som innehåller alla hämtade filer.

### <a name="download-the-contents-of-a-directory"></a>Ladda ned innehållet i en katalog

Du kan hämta innehållet i en katalog utan att kopiera katalogen som innehåller själva med hjälp av jokertecknet (*).

> [!NOTE]
> Det här scenariot stöds för närvarande bara för konton som inte har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Exempel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Lägg till den `--recursive` flagga för att ladda ned filer i alla underkataloger.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiera BLOB-objekt mellan lagringskonton

Du kan använda AzCopy för att kopiera BLOB-objekt till andra storage-konton. Kopieringsåtgärden är synkron så när kommandot returnerar, som anger att alla filer har kopierats.

> [!NOTE]
> Det här scenariot stöds för närvarande bara för konton som inte har ett hierarkiskt namnområde. 

AzCopy använder den [placera Block från URL: en](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, så att data har kopierats direkt mellan lagringsservrar. Dessa kopieringsåtgärder använda inte bandbredd på datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Kopiera en blobb till ett annat lagringskonto
> * Kopiera en katalog till ett annat lagringskonto
> * Kopiera en behållare till ett annat lagringskonto
> * Kopiera alla behållare, kataloger och filer till ett annat lagringskonto

> [!NOTE]
> I den aktuella versionen måste du lägga till en SAS-token till varje Käll-URL. Om du anger autentiseringsuppgifter med hjälp av Azure Active Directory (AD) kan utelämna du SAS-token från mål-URL. 

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiera en blobb till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiera en katalog till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Kopiera en behållare till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Kopiera alla behållare, kataloger och filer till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synkronisera filer

Du kan synkronisera innehållet i ett lokalt filsystem med en blob-behållare. Synkronisering är enkelriktad. Med andra ord välja du vilken av dessa två slutpunkter är källan och vilket som är målet.

> [!NOTE]
> Det här scenariot stöds för närvarande bara för konton som inte har ett hierarkiskt namnområde. Den aktuella versionen av AzCopy inte synkroniseras mellan andra källor och mål (till exempel: Fillagring eller Amazon Web Services (AWS) S3 buckets).

Den `sync` kommandot Jämför filnamn och senast ändrad tidsstämplar. Ange den `--delete-destination` valfri flagga till ett värde av `true` eller `prompt` att ta bort filer i målmappen om dessa filer inte längre finns i källkatalogen.

Om du ställer in den `--delete-destination` flaggan till `true` AzCopy tar bort filer utan att ange en uppmaning. Om du vill att en fråga visas innan AzCopy tar bort en fil, anger du den `--delete-destination` flaggan till `prompt`.

> [!NOTE]
> Om du vill förhindra oavsiktliga borttagningar, se till att aktivera den [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funktionen innan du använder den `--delete-destination=prompt|true` flaggan.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Uppdatera en behållare med ändringar i ett lokalt filsystem

I det här fallet behållaren är målet, och det lokala filsystemet är källan.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exempel** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Uppdatera ett lokalt filsystem med ändringar i en behållare

I det här fallet är målet för det lokala filsystemet och behållaren är källan.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Exempel** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel på någon av följande artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Självstudier: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

- [Överföra data med AzCopy och Amazon S3 buckets](storage-use-azcopy-s3.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)
