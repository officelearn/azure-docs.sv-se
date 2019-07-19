---
title: Kända problem med Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om begränsningar och kända problem med Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.openlocfilehash: 4a8c69dc06b2de08016ae282413402061cdb89d1
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314391"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Kända problem med Azure Data Lake Storage Gen2

Den här artikeln innehåller funktioner och verktyg som inte stöds eller som bara delvis stöds med lagrings konton som har ett hierarkiskt namn område (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>API: er för BLOB storage

API: er för Blob Storage inaktive ras för att förhindra Operability problem som kan uppstå på grund av att Blob Storage API: er inte är kompatibla med Azure Data Lake API: er

> [!NOTE]
> Om du registrerar dig för den offentliga för hands versionen av åtkomst till flera protokoll på Data Lake Storage, kan BLOB-API: er och Data Lake Storage Gen2-API: er köras på samma data. Läs mer i åtkomst till [flera protokoll på data Lake Storage](data-lake-storage-multi-protocol-access.md).

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Vad du kan göra med befintliga verktyg, program och tjänster

Om någon av dessa använder BLOB-API: er och du vill använda dem för att arbeta med allt innehåll som du överför till ditt konto, har du två alternativ.

* **Alternativ 1**: Aktivera inte ett hierarkiskt namn område på ditt Blob Storage-konto förrän BLOB-API: er blir kompatibla med Azure Data Lake Gen2-API: er. Om du använder ett lagrings konto utan ett hierarkiskt namn område har du inte åtkomst till Data Lake Storage Gen2 vissa funktioner, till exempel katalog-och fil Systems åtkomst kontrol listor.

* **Alternativ 2**: Registrera dig för den offentliga för hands versionen av [åtkomst till flera protokoll på data Lake Storage](data-lake-storage-multi-protocol-access.md). Verktyg och program som anropar BLOB-API: er, samt funktioner för blob-lagring, till exempel diagnostikloggar, kan arbeta med konton som har ett hierarkiskt namn område.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Vad du gör om du använde BLOB API: er för att läsa in data innan BLOB-API: er inaktiverades

Om du använde dessa API: er för att läsa in data innan de har inaktiverats, och du har en produktion behöver komma åt dessa data kan sedan kontakta Microsoft Support med följande information:

> [!div class="checklist"]
> * Prenumerations-ID (GUID, inte namnet).
> * Lagrings konto namn.
> * Om du aktivt påverkas av produktion, och i så fall, för vilka lagrings konton?.
> * Även om du inte påverkas aktivt i produktion, berätta för oss om du behöver dessa data ska kopieras till ett annat lagringskonto av någon anledning och varför?

Under dessa omständigheter kan vi återställa åtkomsten till BLOB API under en begränsad tids period, så att du kan kopiera dessa data till ett lagrings konto som inte har funktionen hierarkiskt namn område aktive rad.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Problem och begränsningar med att använda BLOB-API: er på konton som har ett hierarkiskt namn område

Om du registrerar dig för den offentliga för hands versionen av åtkomst till flera protokoll på Data Lake Storage, kan BLOB-API: er och Data Lake Storage Gen2-API: er köras på samma data.

I det här avsnittet beskrivs problem och begränsningar med att använda BLOB-API: er och Data Lake Storage Gen2 API: er för att använda samma data.

Dessa BLOB REST-API: er stöds inte:

* [Lägg till BLOB (sida)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Placerings sida](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Hämta sid intervall](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [BLOB för stegvis kopiering](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Lägg till sida från URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Lägg till BLOB (append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Lägg till block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Lägg till block från URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

* Du kan inte använda både BLOB-API: er och Data Lake Storage-API: er för att skriva till samma instans av en fil.

* Om du skriver till en fil med hjälp av Data Lake Storage Gen2 API: er visas inte filens block för anrop till BLOB-API: t [Get block List](https://docs.microsoft.comrest/api/storageservices/get-block-list) .

* Du kan skriva över en fil med hjälp av antingen Data Lake Storage Gen2-API: er eller BLOB-API: er. Detta påverkar inte fil egenskaperna.

* När du använder [list](https://docs.microsoft.com/rest/api/storageservices/list-blobs) -bloben utan att ange en avgränsare, kommer resultatet att inkludera både kataloger och blobbar.

  Om du väljer att använda en avgränsare använder du bara ett snedstreck (`/`). Detta är den enda avgränsare som stöds.

* Om du använder [Delete BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API för att ta bort en katalog tas katalogen bara bort om den är tom.

  Det innebär att du inte kan använda BLOB API-borttagningarna rekursivt.

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>Problem med ohanterade virtuella dator diskar (VM)

Ohanterade VM-diskar stöds inte i konton som har ett hierarkiskt namn område. Om du vill aktivera ett hierarkiskt namn område på ett lagrings konto placerar du ohanterade virtuella dator diskar i ett lagrings konto där funktionen för hierarkiskt namn område inte är aktive rad.


## <a name="support-for-other-blob-storage-features"></a>Stöd för andra Blob Storage-funktioner

I följande tabell visas alla andra funktioner och verktyg som inte stöds eller som bara delvis stöds med lagrings konton som har ett hierarkiskt namn område (Azure Data Lake Storage Gen2).

| Funktion/verktyg    | Mer information    |
|--------|-----------|
| **API: er för Data Lake Storage Gen2 lagrings konton** | Stöds delvis <br><br>åtkomst till flera protokoll på Data Lake Storage är för närvarande en offentlig för hands version. I den här för hands versionen kan du använda BLOB-API: er i .NET, Java, python SDK: er med konton som har ett hierarkiskt namn område.  SDK: er innehåller ännu inga API: er som gör att du kan interagera med kataloger eller ange åtkomst kontrol listor (ACL: er). Om du vill utföra dessa funktioner kan du använda Data Lake Storage Gen2 **rest** -API: er. |
| **AzCopy** | Versions-/regionsspecifika stöd <br><br>Använd endast den senaste versionen av AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Tidigare versioner av AzCopy, till exempel AzCopy v 8.1, stöds inte.|
| **Hanterings principer för Azure Blob Storage-livscykel** | Stöds bara om du registrerar dig i [multi-Protocol-åtkomst på data Lake Storage för](data-lake-storage-multi-protocol-access.md) hands version. Nivåerna låg frekvent åtkomst och Arkiv lag ring stöds endast av för hands versionen. Borttagning av BLOB-ögonblicksbilder stöds inte ännu. |
| **Azure Content Delivery Network (CDN)** | Stöds inte ännu|
| **Azure Search** |Stöds inte ännu|
| **Azure Storage Explorer** | Versions-/regionsspecifika stöd <br><br>Använd endast version `1.6.0` eller högre. <br>Versionen `1.6.0` är tillgänglig som en [kostnads fri nedladdning](https://azure.microsoft.com/features/storage-explorer/).|
| **Antal ACL: er för BLOB-behållare** |Stöds inte ännu|
| **Blobfuse** |Stöds inte ännu|
| **Anpassade domäner** |Stöds inte ännu|
| **Fil system Utforskaren** | Begränsat stöd |
| **Oåterkalleligt lagrings utrymme** |Stöds inte ännu <br><br>Oföränderlig lagring ger möjlighet att lagra data i en [mask (Skriv en gång, läsa många)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Objekt nivå nivåer** |Låg frekvent lagrings nivå och Arkiv lag ring stöds bara om du registrerar dig i [multi-Protocol-åtkomst på data Lake Storage för](data-lake-storage-multi-protocol-access.md) hands version. <br><br> Alla andra åtkomst nivåer stöds inte ännu.|
| **Stöd för PowerShell och CLI** | Begränsade funktioner <br><br>Hanterings åtgärder som att skapa ett konto stöds. Data Plans åtgärder som att ladda upp och ladda ned filer finns i offentlig för hands version som en del av åtkomsten till [flera protokoll på data Lake Storage](data-lake-storage-multi-protocol-access.md). Att arbeta med kataloger och ange åtkomst kontrol listor (ACL) stöds inte ännu. |
| **Statiska webbplatser** |Stöds inte ännu <br><br>Mer specifikt möjlighet att betjäna filer till [statiska webbplatser](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Program från tredje part** | Begränsat stöd <br><br>Program från tredje part som använder REST-API: er för arbete fortsätter att fungera om du använder dem med Data Lake Storage Gen2. <br>Program som anropar BLOB-API: er fungerar förmodligen om du registrerar dig i den offentliga för hands versionen av [åtkomst till flera protokoll på data Lake Storage](data-lake-storage-multi-protocol-access.md). 
| **Versions funktioner** |Stöds inte ännu <br><br>Detta inkluderar [ögonblicks bilder](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) och [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|


