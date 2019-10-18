---
title: Kända problem med Azure Data Lake Storage Gen2 | Microsoft Docs
description: Läs mer om begränsningar och kända problem med Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f635360c5a6da19d60f3992878a8950b03c5f748
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513878"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Kända problem med Azure Data Lake Storage Gen2

Den här artikeln innehåller funktioner och verktyg som inte stöds eller som bara delvis stöds med lagrings konton som har ett hierarkiskt namn område (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Blob Storage-API: er

Blob Storage-API: er har inaktiverats för att förhindra att funktions Operability problem kan uppstå eftersom Blob Storage API: er inte är kompatibla med Azure Data Lake Gen2-API: er

> [!NOTE]
> Med den offentliga för hands versionen av åtkomst till flera protokoll på Data Lake Storage kan BLOB-API: er och Data Lake Storage Gen2-API: er köras på samma data. Läs mer i åtkomst till [flera protokoll på data Lake Storage](data-lake-storage-multi-protocol-access.md).

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Vad du kan göra med befintliga verktyg, program och tjänster

Om någon av dessa använder BLOB-API: er och du vill använda dem för att arbeta med allt innehåll i ditt konto, har du två alternativ.

* **Alternativ 1**: Aktivera inte ett hierarkiskt namn område på ditt Blob Storage konto förrän [åtkomsten till flera protokoll på data Lake Storage](data-lake-storage-multi-protocol-access.md) är allmänt tillgänglig, och blob-API: er blir helt kompatibla med Azure Data Lake Gen2-API: er. [Åtkomst till flera protokoll på data Lake Storage](data-lake-storage-multi-protocol-access.md) är för närvarande en offentlig för hands version.  Om du använder ett lagrings konto **utan** ett hierarkiskt namn område kan du inte komma åt data Lake Storage Gen2 vissa funktioner, till exempel åtkomst kontrol listor för kataloger och behållare.

* **Alternativ 2**: Aktivera hierarkiska namn områden. Med den offentliga för hands versionen av [åtkomst till flera protokoll på data Lake Storage](data-lake-storage-multi-protocol-access.md)kan verktyg och program som anropar BLOB-API: er, samt Blob Storage funktioner, till exempel diagnostikloggar, arbeta med konton som har ett hierarkiskt namn område. Se till att läsa den här artikeln om kända problem och begränsningar.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Vad du gör om du använde BLOB API: er för att läsa in data innan BLOB-API: er inaktiverades

Om du använde dessa API: er för att läsa in data innan de har inaktiverats, och du har ett produktions krav för att komma åt dessa data, kan du kontakta Microsoft Support med följande information:

> [!div class="checklist"]
> * Prenumerations-ID (GUID, inte namnet).
> * Lagrings konto namn.
> * Om du aktivt påverkas av produktion, och i så fall, för vilka lagrings konton?.
> * Även om du inte aktivt påverkas av produktionen kan du berätta för oss om du behöver kopiera dessa data till ett annat lagrings konto av någon anledning, och varför?

Under dessa omständigheter kan vi återställa åtkomsten till BLOB API under en begränsad tids period, så att du kan kopiera dessa data till ett lagrings konto som inte har funktionen hierarkiskt namn område aktive rad.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Problem och begränsningar med att använda BLOB-API: er på konton som har ett hierarkiskt namn område

Med den offentliga för hands versionen av åtkomst till flera protokoll på Data Lake Storage kan BLOB-API: er och Data Lake Storage Gen2-API: er köras på samma data.

I det här avsnittet beskrivs problem och begränsningar med att använda BLOB-API: er och Data Lake Storage Gen2 API: er för att använda samma data.

* Du kan inte använda både BLOB-API: er och Data Lake Storage-API: er för att skriva till samma instans av en fil.

* Om du skriver till en fil med hjälp av Data Lake Storage Gen2 API: er visas inte filens block för anrop till BLOB-API: t [Get block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) .

* Du kan skriva över en fil med hjälp av antingen Data Lake Storage Gen2-API: er eller BLOB-API: er. Detta påverkar inte fil egenskaperna.

* När du använder [list-bloben](https://docs.microsoft.com/rest/api/storageservices/list-blobs) utan att ange en avgränsare, kommer resultatet att inkludera både kataloger och blobbar.

  Om du väljer att använda en avgränsare använder du bara ett snedstreck (`/`). Detta är den enda avgränsare som stöds.

* Om du använder [Delete BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API för att ta bort en katalog tas katalogen bara bort om den är tom.

  Det innebär att du inte kan använda BLOB API-borttagningarna rekursivt.

Dessa BLOB REST-API: er stöds inte:

* [Lägg till BLOB (sida)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Placerings sida](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Hämta sid intervall](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [BLOB för stegvis kopiering](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Lägg till sida från URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Lägg till BLOB (append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Lägg till block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Lägg till block från URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>Problem med ohanterade virtuella dator diskar (VM)

Ohanterade VM-diskar stöds inte i konton som har ett hierarkiskt namn område. Om du vill aktivera ett hierarkiskt namn område på ett lagrings konto placerar du ohanterade virtuella dator diskar i ett lagrings konto där funktionen för hierarkiskt namn område inte är aktive rad.


## <a name="support-for-other-blob-storage-features"></a>Stöd för andra Blob Storage funktioner

I följande tabell visas alla andra funktioner och verktyg som inte stöds eller som bara delvis stöds med lagrings konton som har ett hierarkiskt namn område (Azure Data Lake Storage Gen2).

| Funktion/verktyg    | Mer information    |
|--------|-----------|
| **API: er för Data Lake Storage Gen2 lagrings konton** | Stöds delvis <br><br>Åtkomst till flera protokoll på Data Lake Storage är för närvarande en offentlig för hands version. I den här för hands versionen kan du använda BLOB-API: er i .NET, Java, python SDK: er med konton som har ett hierarkiskt namn område.  SDK: er innehåller ännu inga API: er som gör att du kan interagera med kataloger eller ange åtkomst kontrol listor (ACL: er). Om du vill utföra dessa funktioner kan du använda Data Lake Storage Gen2 **rest** -API: er. |
| **AzCopy** | Versions-/regionsspecifika stöd <br><br>Använd endast den senaste versionen av AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Tidigare versioner av AzCopy, till exempel AzCopy v 8.1, stöds inte.|
| **Hanterings principer för Azure Blob Storage Lifecycle** | Stöds av [multi-Protocol-åtkomst på data Lake Storage för](data-lake-storage-multi-protocol-access.md) hands version. Nivåerna låg frekvent åtkomst och Arkiv lag ring stöds endast av för hands versionen. Borttagning av BLOB-ögonblicksbilder stöds inte ännu. |
| **Azure Content Delivery Network (CDN)** | Stöds inte ännu|
| **Azure Search** |Stöds av [multi-Protocol-åtkomst på data Lake Storage för](data-lake-storage-multi-protocol-access.md) hands version.|
| **Azure Storage Explorer** | Versions-/regionsspecifika stöd <br><br>Använd endast version `1.6.0` eller högre. <br>Version `1.6.0` är tillgänglig som en [kostnads fri nedladdning](https://azure.microsoft.com/features/storage-explorer/).|
| **Antal ACL: er för BLOB-behållare** |Stöds inte ännu|
| **Blobfuse** |Stöds inte ännu|
| **Anpassade domäner** |Stöds inte ännu|
| **Fil system Utforskaren** | Begränsat stöd |
| **Diagnostikloggning** |Diagnostikloggar stöds av [multi-Protocol-åtkomst på data Lake Storage för](data-lake-storage-multi-protocol-access.md) hands version. <br><br>Aktivering av loggar i Azure Portal stöds inte för närvarande. Här är ett exempel på hur du aktiverar loggarna med hjälp av PowerShell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Se till att ange `Blob` som värdet för parametern `-ServiceType` som visas i det här exemplet. <br><br>Azure Storage Explorer kan för närvarande inte användas för att Visa diagnostikloggar. Om du vill visa loggar använder du AzCopy eller SDK: er.
| **Oåterkalleligt lagrings utrymme** |Stöds inte ännu <br><br>Oföränderlig lagring ger möjlighet att lagra data i en [mask (Skriv en gång, läsa många)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Objekt nivå nivåer** |Låg frekvent lagrings nivå och Arkiv lag ring stöds av [multi-Protocol-åtkomst på data Lake Storage för](data-lake-storage-multi-protocol-access.md) hands version. <br><br> Alla andra åtkomst nivåer stöds inte ännu.|
| **Stöd för PowerShell och CLI** | Begränsade funktioner <br><br>Hanterings åtgärder som att skapa ett konto stöds. Data Plans åtgärder som att ladda upp och ladda ned filer finns i offentlig för hands version som en del av [åtkomsten till flera protokoll på data Lake Storage](data-lake-storage-multi-protocol-access.md). Att arbeta med kataloger och ange åtkomst kontrol listor (ACL) stöds inte ännu. |
| **Statiska webbplatser** |Stöds inte ännu <br><br>Mer specifikt möjlighet att betjäna filer till [statiska webbplatser](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Program från tredje part** | Begränsat stöd <br><br>Program från tredje part som använder REST-API: er för arbete fortsätter att fungera om du använder dem med Data Lake Storage Gen2. <br>Program som anropar BLOB-API: er kommer förmodligen att fungera med den allmänt tillgängliga för hands versionen av [multi-Protocol-åtkomst på data Lake Storage](data-lake-storage-multi-protocol-access.md). |
|**Mjuk borttagning** |Stöds inte ännu|
| **Versions funktioner** |Stöds inte ännu <br><br>Detta inkluderar [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)och andra versions funktioner, till exempel [ögonblicks bilder](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


