---
title: Kända problem med Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om begränsningar och kända problem med Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: 27adc0eeeabed2b1f2e86f301a60604a3d358b82
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464728"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Kända problem med Azure Data Lake Storage Gen2

Den här artikeln visar en lista över funktioner och verktyg som ännu inte stöds eller bara delvis stöds med storage-konton som har ett hierarkiskt namnområde (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>API: er för BLOB storage

BLOB storage-API: er har inaktiverats för att förhindra oavsiktliga data access problem som kan uppstå eftersom API: erna för Blob Storage är ännu inte kompatibla med Azure Data Lake Gen2 API: er.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Vad du gör med befintliga verktyg, program och tjänster

Om något av dessa Använd Blob-API: er och du vill använda dem för att arbeta med allt det innehåll som du överför till ditt konto kan inte aktivera sedan ett hierarkiskt namnområde på Blob storage-kontot förrän Blob API: er bli kompatibla med Azure Data Lake Gen2 API: er.

Med hjälp av ett lagringskonto utan ett hierarkiskt namnområde innebär att du kan sedan inte har åtkomst till Data Lake Storage Gen2 specifika funktioner, till exempel katalog och ett filnamn systemets åtkomstkontrollistor.

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>Vad du gör med ohanterade diskar för virtuella datorer (VM)

Dessa är beroende av den inaktiverade API: er för Blob Storage, så om du vill aktivera ett hierarkiskt namnområde på ett lagringskonto fundera över placeras i ett lagringskonto som inte har funktionen för hierarkiskt namnområde aktiverad.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Vad du gör om du använde Blob API: er för att läsa in data innan Blob API: er har inaktiverats

Om du använde dessa API: er för att läsa in data innan de har inaktiverats, och du har en produktion behöver komma åt dessa data kan sedan kontakta Microsoft Support med följande information:

> [!div class="checklist"]
> * Prenumerations-ID (GUID, inte namnet).
> * Lagringskontonamn.
> * Om du aktivt påverkas i produktion och i så fall, för vilka storage-konton?
> * Även om du inte påverkas aktivt i produktion, berätta för oss om du behöver dessa data ska kopieras till ett annat lagringskonto av någon anledning och varför?

Under dessa omständigheter kan vi återställa åtkomst till Blob-API: et under en begränsad tid så att du kan kopiera dessa data till ett lagringskonto som inte har funktionen för hierarkiskt namnområde aktiverad.

## <a name="all-other-features-and-tools"></a>Alla andra funktioner och verktyg

I följande tabell visas alla andra funktioner och verktyg som ännu inte stöds eller bara delvis stöds med storage-konton som har ett hierarkiskt namnområde (Azure Data Lake Storage Gen2).

| Funktionen / -verktyget    | Mer information    |
|--------|-----------|
| **API: er för Data Lake Storage Gen2 storage-konton** | Stöds delvis <br><br>Du kan använda Data Lake Storage Gen2 **REST** API: er, men API: er i andra Blob SDK: er, till exempel .NET, Java och Python-SDK: er är inte tillgängliga ännu.|
| **AzCopy** | Version supportavtal <br><br>Använd den senaste versionen av AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Tidigare versioner av AzCopy, till exempel AzCopy v8.1 stöds inte.|
| **Azure Blob storage-principer för hantering av livscykeln** | Stöds inte än |
| **Azure Content Delivery Network (CDN)** | Stöds inte än|
| **Azure Event Grid** | Stöds inte än |
| **Azure search** |Stöds inte än|
| **Azure Storage Explorer** | Version supportavtal <br><br>Använd endast version `1.6.0` eller högre. <br>Version `1.6.0` är tillgänglig som en [kostnadsfri nedladdning](https://azure.microsoft.com/features/storage-explorer/).|
| **BLOB-behållare ACL: er** |Stöds inte än|
| **Blobfuse** |Stöds inte än|
| **Anpassade domäner** |Stöds inte än|
| **Diagnostikloggar** |Stöds inte än|
| **Utforskaren för System** | Begränsat stöd |
| **Oföränderlig lagring** |Stöds inte än <br><br>Oföränderlig storage ger möjlighet att lagra data i en [mask (Skriv en gång, läsa många)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) tillstånd.|
| **På objektnivå nivåer** |Stöds inte än <br><br>Exempel: Premium, nivåerna frekvent, lågfrekvent och Arkiv.|
| **Stöd för PowerShell och CLI** | Begränsad funktionalitet <br><br>Du kan skapa ett konto med hjälp av Powershell eller CLI. Du kan inte utföra åtgärder eller ställa in åtkomstkontrollistor för filsystem, kataloger och filer.|
| **Serverstatiska webbplatser** |Stöds inte än <br><br>Mer specifikt möjlighet att dela dem till [Serverstatiska webbplatser](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Program från tredje part** | Begränsat stöd <br><br>Program från tredje part som använder REST API: er för att arbeta fortsätter att fungera om du använder dem med Data Lake Storage Gen2. <br>Om du har ett program som använder Blob-API: er har antagligen programmet problem om du använder programmet med Data Lake Storage Gen2. Mer information finns i den [Blob-lagring som API: er är inaktiverade för Data Lake Storage Gen2 lagringskonton](#blob-apis-disabled) i den här artikeln.|
| **Funktioner för versionshantering** |Stöds inte än <br><br>Detta inkluderar [ögonblicksbilder](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) och [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|
|

