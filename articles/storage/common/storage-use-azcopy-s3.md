---
title: Överföra data till Azure Storage från Amazon S3 buckets med hjälp av AzCopy v10 | Microsoft Docs
description: Överföra data med AzCopy och Amazon S3 buckets
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b18c4c039b615c7c88268b6e668df9f7fec9fabf
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687919"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Kopiera data från Amazon S3 buckets med hjälp av AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar och filer till eller från ett lagringskonto. Den här artikeln hjälper dig att kopiera objekt, kataloger och buckets från Amazon Web Services (AWS) S3 till Azure blob storage med hjälp av AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Välj hur du anger autentiseringsuppgifter

* Använda Azure Active Directory (AD) eller en signatur för delad åtkomst (SAS)-token för att auktorisera med Azure Storage.

* Använda en AWS-åtkomstnyckel och hemliga åtkomstnyckel för att auktorisera med AWS S3.

### <a name="authorize-with-azure-storage"></a>Auktorisera med Azure Storage

Se den [Kom igång med AzCopy](storage-use-azcopy-v10.md) artikel för att hämta AzCopy och välj hur du anger autentiseringsuppgifter till storage-tjänsten.

> [!NOTE]
> Exemplen i den här artikeln förutsätter att du har autentiserats din identitet med hjälp av den `AzCopy login` kommando. AzCopy använder sedan din Azure AD-konto för att bevilja åtkomst till data i Blob storage.
>
> Om du föredrar att använda en SAS-token för att bevilja åtkomst till blob-data, kan du lägga till den token i resurs-URL: en i varje AzCopy-kommandot.
>
> Till exempel: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Auktorisera med AWS S3

Samla in din AWS-åtkomstnyckel och hemliga åtkomstnyckel och ange sedan dessa miljövariabler:

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopiera objekt, kataloger och buckets

AzCopy använder den [placera Block från URL: en](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, så att data har kopierats direkt mellan AWS S3 och lagringsservrar. Dessa kopieringsåtgärder använda inte bandbredd på datorn.

### <a name="copy-an-object"></a>Kopiera ett objekt

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Exempel** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> Exemplen i den här artikeln använder sökväg-style URL: er för AWS S3 buckets (till exempel: `http://s3.amazonaws.com/<bucket-name>`). 
>
> Du kan också använda virtuella värd-style URL: er samt (till exempel: `http://bucket.s3.amazonaws.com`). 
>
> Mer information om virtuell värd buckets finns [virtuella som är värd för Buckets]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Kopiera en katalog

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **Exempel** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>Kopiera en bucket

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Exempel** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Kopiera alla buckets i alla regioner

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Exempel** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopiera alla buckets i en viss region för S3

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Exempel** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Hantera skillnader i regler för namngivning av objekt

AWS S3 har en annan uppsättning regler för namngivning av bucket namn jämfört med Azure blob-behållare. Du kan läsa om dem [här](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Om du vill kopiera en grupp med buckets till ett Azure storage-konto kan kopieringen misslyckas på grund av naming skillnader.

AzCopy hanterar två av de vanligaste problemen som kan uppstå; buckets med punkter och buckets med bindestreck i följd. AWS S3-bucket namn får innehålla punkter och bindestreck, men inte i en behållare i Azure. AzCopy ersätter perioder med bindestreck och bindestreck med ett tal som representerar antalet bindestreck (till exempel: en bucket med namnet `my----bucket` blir `my-4-bucket`. 

Dessutom som AzCopy kopierar filer, den söker efter namnkonflikter och försöker lösa dem. Exempel: om det finns buckets med namnet `bucket-name` och `bucket.name`, AzCopy löser en bucket med namnet `bucket.name` första till `bucket-name` och sedan till `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Hantera skillnader i metadata för objekt

Tillåt olika uppsättningar tecken i namnen på objektnycklar AWS S3 och Azure. Du kan läsa om tecknen att AWS S3 använder [här](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). På Azure-sidan blob objektnycklar följa namnregler för [ C# identifierare](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Som en del av en AzCopy `copy` kommandot, du kan ange ett värde för valfritt den `s2s-invalid-metadata-handle` flagga som anger hur du vill hantera filer där metadata för filen innehåller inkompatibla nyckelnamn. I följande tabell beskrivs varje flaggvärde.

| Flaggvärde | Beskrivning  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standardalternativet) Metadata ingår inte i det överförda objektet. AzCopy loggar en varning. |
| **FailIfInvalid** | Objekt kopieras inte. AzCopy loggar ett fel och innehåller felet i antal misslyckade som visas i sammanfattningen för överföring.  |
| **RenameIfInvalid**  | AzCopy matchar nyckeln ogiltiga metadata och kopierar objektet till Azure med hjälp av viktiga värde-par löst metadata. Mer exakt vilka steg som AzCopy tar att byta namn på objektnycklar finns i den [hur AzCopy byter namn på objektnycklar](#rename-logic) nedan. Om AzCopy inte att byta namn på nyckeln kan kopieras inte objektet. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Hur AzCopy byter namn på objektnycklar

AzCopy utför de här stegen:

1. Ersätter ogiltiga tecken med ”_”.

2. Lägger till strängen `rename_` i början av en ny giltig nyckel.

   Den här nyckeln används för att spara metadata **värdet**.

3. Lägger till strängen `rename_key_` i början av en ny giltig nyckel.
   Den här nyckeln används för att spara ursprungliga metadata ogiltig **nyckeln**.
   Du kan använda den här nyckeln för att försöka återskapa metadata i Azure-sidan eftersom metadatanyckel bevaras som ett värde på Blob storage-tjänsten.

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel på någon av följande artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Överföra data med AzCopy och blob storage](storage-use-azcopy-blobs.md)

- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)