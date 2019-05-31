---
title: Överföra data till eller från Azure Blob storage med hjälp av AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling av AzCopy exempel kommandon som hjälper dig att skapa behållare, kopiera filer och synkronisera mappar mellan lokala filsystem och behållare.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 98e33f838ee9b6f506bf1dc01e1dd61ad587aa05
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299397"
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

Du kan använda AzCopy `copy` kommando för att ladda upp filer och mappar från din lokala dator.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Överför en fil
> * Ladda upp en mapp
> * Ladda upp filer med jokertecken

> [!NOTE]
> AzCopy inte automatiskt beräkna och lagra filens md5-hash-kod. Om du vill AzCopy för att göra det, lägger till den `--put-md5` flagga för att varje kopia-kommando. På så sätt kan när blobben som har hämtats, AzCopy beräknar en MD5-hash för nedladdade data och verifierar att MD5-hash som lagras i blobens `Content-md5` -egenskap stämmer med den beräknade hashen.

### <a name="upload-a-file"></a>Överför en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Exempel** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy som standard överför data till blockblobar. Ladda upp filer som Tilläggsblobbar och Sidblobbar använder du flaggan `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-folder"></a>Ladda upp en mapp

Det här exemplet kopierar en mapp (och alla filer i mappen) till en blob-behållare. Resultatet är en mapp i behållaren med samma namn.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exempel** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Om du vill kopiera till en mapp i behållaren, anger du namnet på mappen i kommandosträng.

|    |     |
|--------|-----------|
| **Exempel** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder" --recursive` |

Om du anger namnet på en mapp som inte finns i behållaren, skapar en ny mapp med det namnet i AzCopy.

### <a name="upload-the-contents-of-a-folder"></a>Ladda upp innehållet i en mapp

Du kan ladda upp innehållet i en mapp utan att kopiera mappen som innehåller själva med hjälp av jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-folder-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>` |
| **Exempel** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder"` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder"` |

> [!NOTE]
> Lägg till den `--recursive` flagga för att ladda upp filer i alla undermappar.

## <a name="download-files"></a>Hämta filer

Du kan använda AzCopy `copy` kommando för att hämta blobs, mappar och behållare till den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Hämta en fil
> * Ladda ned en mapp
> * Ladda ned filer med jokertecken

> [!NOTE]
> Om den `Content-md5` egenskapsvärdet för en blob innehåller ett hash-värde, AzCopy beräknar en MD5-hash för hämtade data och verifierar att MD5-hash som lagras i blobens `Content-md5` -egenskap stämmer med den beräknade hashen. Om värdena inte matchar nedladdningen inte såvida du inte åsidosätter det här beteendet genom att lägga till `--check-md5=NoCheck` eller `--check-md5=LogOnly` till kopieringskommandot.

### <a name="download-a-file"></a>Hämta en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Exempel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Ladda ned en mapp

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>" "<local-folder-path>" --recursive` |
| **Exempel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |

Det här exemplet resulterar i en mapp med namnet `C:\myFolder\myBlobFolder` som innehåller alla hämtade filer.

### <a name="download-the-contents-of-a-folder"></a>Ladda ned innehållet i en mapp

Du kan hämta innehållet i en mapp utan att kopiera mappen som innehåller själva med hjälp av jokertecknet (*).

> [!NOTE]
> Det här scenariot stöds för närvarande bara för konton som inte har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-folder-path>/"` |
| **Exempel** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder/*" "C:\myFolder"` |

> [!NOTE]
> Lägg till den `--recursive` flagga för att ladda ned filer i alla undermappar.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiera BLOB-objekt mellan lagringskonton

Du kan använda AzCopy för att kopiera BLOB-objekt till andra storage-konton. Kopieringsåtgärden är synkron så när kommandot returnerar, som anger att alla filer har kopierats.

> [!NOTE]
> Det här scenariot stöds för närvarande bara för konton som inte har ett hierarkiskt namnområde.

AzCopy använder den [placera Block från URL: en](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, så att data har kopierats direkt mellan lagringsservrar. Dessa kopieringsåtgärder använda inte bandbredd på datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Kopiera en blobb till ett annat lagringskonto
> * Kopiera en mapp till ett annat lagringskonto
> * Kopiera en behållare till ett annat lagringskonto
> * Kopiera alla behållare, mappar och filer till ett annat lagringskonto

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiera en blobb till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-folder-to-another-storage-account"></a>Kopiera en mapp till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" --recursive` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobFolder" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Kopiera en behållare till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-folders-and-files-to-another-storage-account"></a>Kopiera alla behållare, mappar och filer till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Exempel** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synkronisera filer

Du kan synkronisera innehållet i ett lokalt filsystem på en blobbehållare. Du kan också synkronisera en blob-behållare till ett lokalt filsystem på din dator. Synkronisering är enkelriktad. Med andra ord välja du vilken av dessa två slutpunkter är källan och vilket som är målet.

> [!NOTE]
> Den aktuella versionen av AzCopy inte synkroniseras mellan andra källor och mål (till exempel: Fillagring eller Amazon Web Services (AWS) S3 buckets).

Den `sync` kommandot Jämför filnamn och senast ändrad tidsstämplar. Ange den `--delete-destination` valfri flagga till ett värde av `true` eller `prompt` att ta bort filer i målmappen om dessa filer inte längre finns i källmappen.

Om du ställer in den `--delete-destination` flaggan till `true` AzCopy tar bort filer utan att ange en uppmaning. Om du vill att en fråga visas innan AzCopy tar bort en fil, anger du den `--delete-destination` flaggan till `prompt`.

> [!NOTE]
> Om du vill förhindra oavsiktliga borttagningar, se till att aktivera den [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funktionen innan du använder den `--delete-destination=prompt|true` flaggan.

### <a name="synchronize-a-container-to-a-local-file-system"></a>Synkronisera en behållare till ett lokalt filsystem

I det här fallet det lokala filsystemet blir källan och behållaren är målet.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exempel** | `azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy sync "C:\myFolder" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>Synkronisera ett lokalt filsystem till en behållare

I det här fallet behållaren blir källan och det lokala filsystemet är målet.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myFolder" --recursive` |
| **Exempel** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myFolder" --recursive` |
| **Exempel** (hierarkiskt namnområde) | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myFolder" --recursive` |

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel på någon av följande artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

- [Överföra data med AzCopy och Amazon S3 buckets](storage-use-azcopy-s3.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)