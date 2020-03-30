---
title: Kopiera data från Amazon S3 till Azure Storage med azcopy | Microsoft-dokument
description: Överför data med AzCopy och Amazon S3 hinkar
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941508"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Kopiera data från Amazon S3 till Azure Storage med hjälp av AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobbar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att kopiera objekt, kataloger och buckets från Amazon Web Services (AWS) S3 till Azure blob storage med hjälp av AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Välj hur du ska ange auktoriseringsuppgifter

* Om du vill auktorisera med Azure Storage använder du Azure Active Directory (AD) eller en SAS-token (Shared Access Signature).

* Om du vill auktorisera med AWS S3 använder du en AWS-åtkomstnyckel och en hemlig åtkomstnyckel.

### <a name="authorize-with-azure-storage"></a>Auktorisera med Azure Storage

Se artikeln [Kom igång med AzCopy](storage-use-azcopy-v10.md) för att hämta AzCopy och välj hur du ska tillhandahålla auktoriseringsautentiseringsuppgifter till lagringstjänsten.

> [!NOTE]
> Exemplen i den här artikeln förutsätter att du `AzCopy login` har autentiserat din identitet med kommandot. AzCopy använder sedan ditt Azure AD-konto för att auktorisera åtkomst till data i Blob-lagring.
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till blob-data kan du lägga till den token i resurs-URL:en i varje AzCopy-kommando.
>
> Till exempel: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Auktorisera med AWS S3

Samla in din AWS-åtkomstnyckel och hemliga åtkomstnyckel och ställ sedan in dessa miljövariabler:

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Macos** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopiera objekt, kataloger och buckets

AzCopy använder [API:et för put block från URL,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) så data kopieras direkt mellan AWS S3 och lagringsservrar. Dessa kopieringsåtgärder använder inte datorns nätverksbandbredd.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Om du bestämmer dig för att ta bort data från S3-buckets efter en kopiering kontrollerar du att data har kopierats korrekt till ditt lagringskonto innan du tar bort data.

> [!TIP]
> Exemplen i det här avsnittet omger banargument med enstaka citattecken (''). Använd enstaka citattecken i alla kommandoskal utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe) bifogar du sökvägsargument med dubbla citattecken ("") i stället för enstaka citattecken ('').

 De här exemplen fungerar också med konton som har ett hierarkiskt namnområde. [Med åtkomst till flera protokoll i Datasjölagring](../blobs/data-lake-storage-multi-protocol-access.md) kan`blob.core.windows.net`du använda samma URL-syntax ( ) på dessa konton. 

### <a name="copy-an-object"></a>Kopiera ett objekt

Använd samma URL-syntax (`blob.core.windows.net`) för konton som har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Exempel** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Exempel** (hierarkiskt namnområde) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Exempel i den här artikeln använder url:er för sökvägsliknande url:er för AWS S3-buckets (till exempel: `http://s3.amazonaws.com/<bucket-name>`). 
>
> Du kan också använda webbadresser i virtuellt värdformat (till exempel: `http://bucket.s3.amazonaws.com`). 
>
> Mer information om virtuell hosting av buckets finns i [Virtual Hosting of Buckets]](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Kopiera en katalog

Använd samma URL-syntax (`blob.core.windows.net`) för konton som har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exempel** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exempel** (hierarkiskt namnområde)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Kopiera en bucket

Använd samma URL-syntax (`blob.core.windows.net`) för konton som har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Exempel** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Exempel** (hierarkiskt namnområde)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Kopiera alla buckets i alla regioner

Använd samma URL-syntax (`blob.core.windows.net`) för konton som har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exempel** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exempel** (hierarkiskt namnområde)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopiera alla buckets i en viss S3-region

Använd samma URL-syntax (`blob.core.windows.net`) för konton som har ett hierarkiskt namnområde.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exempel** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exempel** (hierarkiskt namnområde)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Hantera skillnader i namngivningsregler för objekt

AWS S3 har en annan uppsättning namngivningskonventioner för bucket-namn jämfört med Azure-blob-behållare. Du kan läsa om dem [här](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Om du väljer att kopiera en grupp buckets till ett Azure-lagringskonto kan kopieringen misslyckas på grund av att du namnger skillnader.

AzCopy hanterar två av de vanligaste problemen som kan uppstå; hinkar som innehåller punkter och buckets som innehåller på varandra följande bindestreck. AWS S3-bucketnamn kan innehålla punkter och på varandra följande bindestreck, men en behållare i Azure kan inte. AzCopy ersätter perioder med bindestreck och på varandra följande bindestreck med ett tal som `my----bucket` `my-4-bucket`representerar antalet på varandra följande bindestreck (till exempel: en bucket named blir . 

Dessutom, som AzCopy kopior över filer, det kontrollerar för namngivning kollisioner och försök att lösa dem. Om `bucket-name` det till exempel finns buckets `bucket.name`med namnet och , `bucket.name` AzCopy löser en bucket som först namnges till `bucket-name` och sedan till `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Hantera skillnader i objektmetadata

AWS S3 och Azure tillåter olika teckenuppsättningar i namnen på objektnycklar. Du kan läsa om de tecken som AWS S3 använder [här](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). På Azure-sidan följer blob-objektnycklarna namngivningsreglerna för [C#-identifierare](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Som en del `copy` av ett AzCopy-kommando kan `s2s-invalid-metadata-handle` du ange ett värde för valfri flagga som anger hur du vill hantera filer där metadata för filen innehåller inkompatibla nyckelnamn. I följande tabell beskrivs varje flaggvärde.

| Flagga värde | Beskrivning  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standardalternativ) Metadata ingår inte i det överförda objektet. AzCopy loggar en varning. |
| **FailIfInvalid (fel)** | Objekt kopieras inte. AzCopy loggar ett fel och inkluderar det felet i det misslyckade antalet som visas i överföringssammanfattningen.  |
| **Byt namn På Namn**  | AzCopy matchar den ogiltiga metadatanyckeln och kopierar objektet till Azure med hjälp av det lösta nyckelvärdeparet för metadata. Mer information om exakt vilka steg AzCopy tar för att byta namn på objektnycklar finns i avsnittet [Hur AzCopy byter namn på objektnycklar](#rename-logic) nedan. Om AzCopy inte kan byta namn på nyckeln kopieras inte objektet. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Så här byter AzCopy namn på objektnycklar

AzCopy utför följande steg:

1. Ersätter ogiltiga tecken med __.

2. Lägger `rename_` till strängen i början av en ny giltig nyckel.

   Den här nyckeln används för att spara det ursprungliga **metadatavärdet**.

3. Lägger `rename_key_` till strängen i början av en ny giltig nyckel.
   Den här nyckeln används för att spara **den**ursprungliga metadata ogiltignyckel .
   Du kan använda den här nyckeln för att försöka återställa metadata på Azure-sidan eftersom metadatanyckeln bevaras som ett värde på Blob-lagringstjänsten.

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i någon av dessa artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Överföra data med AzCopy- och bloblagring](storage-use-azcopy-blobs.md)

- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)