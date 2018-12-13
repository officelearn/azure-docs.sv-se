---
title: Kända problem med Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om begränsningar och kända problem med Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: normesta
ms.openlocfilehash: d42d05046f4ef133aa3dfb52bbe4e2b72255ccce
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322900"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Kända problem med Azure Data Lake Storage Gen2

Den här artikeln innehåller kända problem och temporära begränsningar med Azure Data Lake Storage Gen2.

## <a name="api-interoperability"></a>API-samverkan

API: erna för Blob Storage och Azure Data Lake Gen2 API: er är inte kompatibla med varandra.

Om du vill använda samma verktyg för att arbeta med allt det innehåll som du överför till ditt konto kan inte aktivera hierarkisk namnområden på Blob storage-kontot förrän dessa API: er bli kompatibla med varandra. Med hjälp av ett lagringskonto utan hierarkiskt namnområde åtkomstkontrollistor innebär att du kan sedan inte har åtkomst till Data Lake Storage Gen2 specifika funktioner, till exempel katalogen och filsystem.

## <a name="blob-storage-apis"></a>API: er för BLOB storage

API: er för BLOB storage är ännu inte tillgänglig för Azure Data Lake Storage Gen2 konton.

Dessa API: er har inaktiverats för att förhindra oavsiktliga data access problem som kan uppstå eftersom API: erna för Blob Storage är ännu inte kompatibla med Azure Data Lake Gen2 API: er.

Om du använde dessa API: er för att läsa in data innan de har inaktiverats, och du har en produktion behöver komma åt dessa data kan sedan kontakta Microsoft Support med följande information:

* Prenumerations-ID (GUID, inte namnet)

* Lagringskontonamn

* Om du aktivt påverkas i produktion och i så fall, för vilka storage-konton?

* Även om du inte påverkas aktivt i produktion, berätta för oss om du behöver dessa data ska kopieras till ett annat lagringskonto av någon anledning och varför?

Under dessa omständigheter kan vi återställa åtkomst till Blob-API: et under en begränsad tid så att du kan kopiera dessa data till ett lagringskonto som inte har hierarkisk aktiverade namnområden.

Ohanterade diskar för virtuella datorer (VM) är beroende av den inaktiverade API: er för Blob Storage, så om du vill aktivera hierarkisk namnområden på ett lagringskonto, Överväg att placera ohanterade Virtuella datordiskar till ett lagringskonto som inte har hierarkisk aktiverade namnområden.

## <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren

Om du vill visa eller hantera Data Lake Storage Gen2 konton med hjälp av Azure Storage Explorer, du måste ha minst version `1.6.0` i verktyget som är tillgängligt som en [kostnadsfri nedladdning](https://azure.microsoft.com/features/storage-explorer/).

Observera att versionen av Storage Explorer som är inbäddad i Azure Portal har för närvarande inte stöd för Visa eller hantera Data Lake Storage Gen2 konton med hierarkisk aktiverade namnområden.

## <a name="blob-viewing-tool"></a>Verktyget för visning av BLOB

Endast har BLOB Visa verktyget på Azure-portalen begränsad support för Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Program från tredje part

Program från tredje part kanske inte stöder Azure Data Lake Storage Gen2.

Support är gottfinnande för varje program från tredje part-provider. För närvarande Blob-lagring API: er och går inte att använda Azure Data Lake Storage Gen2 API: er för att hantera samma innehåll. Vi arbetar för att aktivera att är det möjligt att många tredjepartsverktyg stöder Azure Data Lake Storage Gen2 automatiskt.

## <a name="azcopy-support"></a>AzCopy-stöd

AzCopy version 8 stöder inte Azure Data Lake Storage Gen2.

Använd i stället den senaste förhandsversionen av AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) eftersom den har stöd för Azure Data Lake Storage Gen2 slutpunkter.

## <a name="oauth-authentication"></a>OAuth-autentisering

Tjänster, till exempel HDInsight och Azure Data Factory integrera inte ännu med Azure Active Directory (Azure AD) OAuth-token ägarautentisering.

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) inte ta emot händelser från Azure Data Lake Gen2 konton eftersom dessa konton inte ännu generera dem.  

## <a name="soft-delete-and-snapshots"></a>Mjuk borttagning och ögonblicksbilder

Mjuk borttagning och ögonblicksbilder är inte tillgängliga för Azure Data Lake Storage Gen2 konton.

Alla versionshantering funktioner inklusive [ögonblicksbilder](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) och [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) är ännu inte tillgängliga för Storage-konton som har hierarkisk aktiverade namnområden.

## <a name="object-level-storage-tiers"></a>Objektet på lagringsnivåer

Objektet på lagringsnivåer (frekvent, lågfrekvent och Arkiv) är ännu inte tillgängliga för Azure Data Lake Storage Gen 2-konton, men de blir tillgängliga för Storage-konton som inte har hierarkisk blanksteg aktiverat.

## <a name="azure-blob-storage-lifecycle-management-preview-policies"></a>Azure Blob Storage livscykeln för hantering (förhandsversion)-principer

Azure Blob Storage livscykeln för hantering (förhandsversion)-principer är ännu inte tillgängliga för Azure Data Lake Storage Gen2 konton.

Dessa principer är tillgängliga för Storage-konton som inte har hierarkisk blanksteg aktiverat.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Diagnostikloggar är inte tillgängliga för Azure Data Lake Storage Gen2 konton.

Kontakta Azure-supporten om du vill begära diagnostikloggar. Ange dem med ditt kontonamn och tidsperioden som du behöver loggar.
