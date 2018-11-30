---
title: Kända problem med Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om begränsningar och kända problem med Azure Data Lake Storage Gen2
services: storage
author: normesta
manager: twooley
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: normesta
ms.openlocfilehash: fa37c23f936173c19f32b76dffab8908176ebd75
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621854"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Kända problem med Azure Data Lake Storage Gen2

Den här artikeln innehåller kända problem och temporära begränsningar med Azure Data Lake Storage Gen2.

## <a name="api-interoperability"></a>API-samverkan

API: erna för Blob Storage och Azure Data Lake Gen2 API: er är inte kompatibla med varandra.

Om du vill använda samma verktyg för att arbeta med allt det innehåll som du överför till ditt konto kan inte aktivera hierarkisk namnområden på Blob storage-kontot förrän dessa API: er bli kompatibla med varandra. Med hjälp av ett lagringskonto utan hierarkiskt namnområde åtkomstkontrollistor innebär att du kan sedan inte har åtkomst till Data Lake Storage Gen2 specifika funktioner, till exempel katalogen och filsystem.

## <a name="blob-storage-apis"></a>API: er för BLOB storage

API: er för BLOB storage är ännu inte tillgängliga för Azure Data Lake Storage Gen 2-konton.

Dessa API: er har inaktiverats för att förhindra oavsiktliga data access problem som kan uppstå eftersom API: erna för Blob Storage är ännu inte kompatibla med Azure Data Lake Gen2 API: er.

Ohanterade diskar för virtuella datorer (VM) är beroende av dessa API: er, så om du vill aktivera hierarkisk namnområden på ett lagringskonto, Överväg att placera ohanterade Virtuella datordiskar till ett lagringskonto som inte har hierarkisk aktiverade namnområden.

## <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren

Vissa funktioner i Storage Explorer fungerar inte ännu med Azure Data Lake Storage Gen2 filsystem. Dessa begränsningar gäller för både den [fristående version](https://azure.microsoft.com/features/storage-explorer/) av Azure Storage Explorer samt den version som visas i Azure-portalen.

## <a name="blob-viewing-tool"></a>Verktyget för visning av BLOB

Endast har BLOB Visa verktyget på Azure-portalen begränsad support för Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Program från tredje part

Program från tredje part kanske inte stöder Azure Data Lake Storage Gen2.

Support är gottfinnande för varje program från tredje part-provider. För närvarande Blob-lagring API: er och går inte att använda Azure Data Lake Storage Gen2 API: er för att hantera samma innehåll. Vi arbetar för att aktivera att är det möjligt att många tredjepartsverktyg stöder Azure Data Lake Storage Gen2 automatiskt.

## <a name="azcopy-support"></a>AzCopy-stöd

AzCopy version 8 stöder inte Azure Data Lake Storage Gen2.

Använd i stället den senaste förhandsversionen av AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) eftersom den har stöd för Azure Data Lake Storage Gen2 slutpunkter.

## <a name="oauth-authentication"></a>OAuth-autentisering

Tjänster som Azure Databricks, HDInsight och Azure Data Factory integrera inte ännu med Azure Active Directory (Azure AD) OAuth-token ägarautentisering.

## <a name="access-control-lists-acl"></a>Åtkomstkontrollistor (ACL)

Katalog- och filnivå åtkomstkontrollistor (ACL) är svåra att hantera. Det finns inga gränssnittsbaserade verktyg som du kan använda för att hämta och ange de åtkomstkontrollistor.

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) inte ta emot händelser från Azure Data Lake Gen2 konton eftersom dessa konton inte ännu generera dem.  

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Du kan inte använda rollbaserad åtkomstkontroll filsystemsobjekt i ett Azure Data Lake Storage Gen2-konto.

## <a name="sql-data-warehouse-polybase"></a>SQL Data Warehouse PolyBase

När Storage-brandväggar är aktiverade på ett Azure Storage-konto, SQL Data Warehouse [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017) kan inte komma åt dessa konton.

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
