---
title: Kända problem med Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om begränsningar och kända problem med Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: normesta
ms.openlocfilehash: 51230fe050c67253dd5b2bb3f23d03512df64ef6
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939255"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Kända problem med Azure Data Lake Storage Gen2

Den här artikeln innehåller kända problem och temporära begränsningar med Data Lake Storage Gen2.

## <a name="sdk-support-for-data-lake-storage-gen2-accounts"></a>SDK-stöd för Data Lake Storage Gen2 konton

Det är inte SDK som fungerar med Data Lake Storage Gen2 konton.

## <a name="blob-storage-apis"></a>API: er för BLOB storage

API: er för BLOB storage är ännu inte tillgängliga för Data Lake Storage Gen2 konton.

Dessa API: er har inaktiverats för att förhindra oavsiktliga data access problem som kan uppstå eftersom API: erna för Blob Storage är ännu inte kompatibla med Azure Data Lake Gen2 API: er.

Om du använde dessa API: er för att läsa in data innan de har inaktiverats, och du har en produktion behöver komma åt dessa data kan sedan kontakta Microsoft Support med följande information:

* Prenumerations-ID (GUID, inte namnet)

* Lagringskontonamn

* Om du aktivt påverkas i produktion och i så fall, för vilka storage-konton?

* Även om du inte påverkas aktivt i produktion, berätta för oss om du behöver dessa data ska kopieras till ett annat lagringskonto av någon anledning och varför?

Under dessa omständigheter kan vi återställa åtkomst till Blob-API: et under en begränsad tid så att du kan kopiera dessa data till ett lagringskonto som inte har funktionen för hierarkiskt namnområde aktiverad.

Ohanterade diskar för virtuella datorer (VM) är beroende av den inaktiverade API: er för Blob Storage, så om du vill aktivera ett hierarkiskt namnområde på ett lagringskonto, Överväg att placera ohanterade Virtuella datordiskar till ett lagringskonto som inte har funktionen för hierarkiskt namnområde aktiverad.

## <a name="api-interoperability"></a>API-samverkan

API: erna för Blob Storage och Azure Data Lake Gen2 API: er är inte kompatibla med varandra.

Om du har verktyg, program, tjänster eller skript som använder Blob-API: er och du vill använda dem för att arbeta med allt det innehåll som du överför till ditt konto, inte aktivera ett hierarkiskt namnområde på Blob storage-kontot förrän Blob API: er blir kompatibel med Azure Data Lake Gen2 API: er. Med hjälp av ett lagringskonto utan ett hierarkiskt namnområde innebär att du kan sedan inte har åtkomst till Data Lake Storage Gen2 specifika funktioner, till exempel katalog och ett filnamn systemets åtkomstkontrollistor.

## <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren

Om du vill visa eller hantera Data Lake Storage Gen2 konton med hjälp av Azure Storage Explorer, du måste ha minst version `1.6.0` i verktyget som är tillgängligt som en [kostnadsfri nedladdning](https://azure.microsoft.com/features/storage-explorer/).

Observera att versionen av Storage Explorer som är inbäddad i Azure-portalen har för närvarande inte stöd för Visa eller hantera Gen2 för Data Lake Storage-konton med funktionen för hierarkiskt namnområde aktiverad.

## <a name="blob-viewing-tool"></a>Verktyget för visning av BLOB

BLOB Visa verktyget på Azure-portalen har endast begränsat stöd för Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Program från tredje part

Program från tredje part kanske inte stöder Data Lake Storage Gen2.

Support är gottfinnande för varje program från tredje part-provider. För närvarande Blob-lagring API: er och Data Lake Storage Gen2 API: er kan inte användas för att hantera samma innehåll. Vi arbetar för att aktivera att är det möjligt att många tredjepartsverktyg stöder Data Lake Storage Gen2 automatiskt.

## <a name="azcopy-support"></a>AzCopy-stöd

AzCopy version 8 stöder inte Data Lake Storage Gen2.

Använd i stället den senaste förhandsversionen av AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) som stöder Data Lake Storage Gen2 slutpunkter.

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) inte ta emot händelser från Azure Data Lake Gen2 konton eftersom dessa konton inte ännu generera dem.  

## <a name="soft-delete-and-snapshots"></a>Mjuk borttagning och ögonblicksbilder

Mjuk borttagning och ögonblicksbilder är inte tillgängliga för Data Lake Storage Gen2 konton.

Alla versionshantering funktioner inklusive [ögonblicksbilder](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) och [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) är ännu inte tillgängliga för Storage-konton som har funktionen för hierarkiskt namnområde aktiverad.

## <a name="object-level-storage-tiers"></a>Objektet på lagringsnivåer

Objektet på lagringsnivåer (frekvent, lågfrekvent och Arkiv) är ännu inte tillgängliga för Azure Data Lake Storage Gen 2-konton, men de blir tillgängliga för Storage-konton som inte har funktionen för hierarkiskt namnområde aktiverad.

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Azure Blob Storage principer för hantering av livscykeln

Azure Blob Storage principer för hantering av livscykeln är ännu inte tillgängliga för Data Lake Storage Gen2 konton.

Dessa principer är tillgängliga för Storage-konton som inte har funktionen för hierarkiskt namnområde aktiverad.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Diagnostikloggar är inte tillgängliga för Data Lake Storage Gen2 konton.
