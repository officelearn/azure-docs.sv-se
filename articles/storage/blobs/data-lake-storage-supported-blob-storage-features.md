---
title: Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om vilka Blob Storage-funktioner som du kan använda med Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: cfc7e12efb0e5e4613bb4cc5f2f9a35383f37546
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857252"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2

Blob Storage funktioner som [diagnostisk loggning](../common/storage-analytics-logging.md), [åtkomst nivåer](storage-blob-storage-tiers.md)och [Blob Storage hanterings principer för livs cykeln](storage-lifecycle-management-concepts.md) fungerar nu med konton som har ett hierarkiskt namn område. Därför kan du aktivera hierarkiska namn rymder på Blob Storage-kontona utan att förlora åtkomsten till dessa funktioner.

I den här tabellen visas de Blob Storage-funktioner som du kan använda med Azure Data Lake Storage Gen2. De objekt som visas i tabellerna ändras med tiden då stödet fortsätter att expandera.

## <a name="supported-blob-storage-features"></a>Blob Storage-funktioner som stöds

> [!NOTE]
> Support nivån avser endast hur funktionen stöds med Data Lake Storage Gen2.

|Blob Storage funktion |Support nivå |Relaterade artiklar |
|---------------|-------------------|---|
|Frekvent åtkomstnivå|Allmänt tillgänglig|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Lågfrekvent åtkomstnivå|Allmänt tillgänglig|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Händelser|Allmänt tillgänglig|[Reagera på Blob Storage-händelser](storage-blob-event-overview.md)|
|Mått (klassisk)|Allmänt tillgänglig|[Azure Storage analys mått (klassisk)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Mått i Azure Monitor|Allmänt tillgänglig|[Azure Storage-mått i Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell-kommandon för Blob Storage|Allmänt tillgänglig|[Snabb start: Ladda upp, ladda ned och lista blobar med PowerShell](storage-quickstart-blobs-powershell.md)|
|Blob Storage Azure CLI-kommandon|Allmänt tillgänglig|[Snabb start: skapa, ladda ned och lista blobar med Azure CLI](storage-quickstart-blobs-cli.md)|
|API: er för Blob Storage|Allmänt tillgänglig|[Snabb start: klient biblioteket för Azure Blob Storage-V12 för .NET](storage-quickstart-blobs-dotnet.md)<br>[Snabb start: hantera blobbar med Java V12 SDK](storage-quickstart-blobs-java.md)<br>[Snabb start: hantera blobbar med python V12 SDK](storage-quickstart-blobs-python.md)<br>[Snabb start: hantera blobbar med Java Script V12 SDK i Node. js](storage-quickstart-blobs-nodejs.md)|
|Arkiv åtkomst nivå|Förhandsgranskning|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Principer för livs cykel hantering|Förhandsgranskning|[Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)|
|Diagnostikloggar|Allmänt tillgänglig|[Analysloggning i Azure Storage](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Ändringsfeed|Stöds inte ännu|[Ändra feed-stöd i Azure Blob Storage](storage-blob-change-feed.md)|
|Redundansväxling av konto|Stöds inte ännu|[Haveri beredskap och redundans för lagrings konto](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB container ACL|Stöds inte ännu|[Ange behållar-ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Anpassade domäner|Stöds inte ännu|[Mappa en anpassad domän till en Azure Blob Storage-slutpunkt](storage-custom-domain-name.md)|
|Oåterkalleligt lagrings utrymme|Stöds inte ännu|[Lagra affärs kritiska BLOB-data med oföränderlig lagring](storage-blob-immutable-storage.md)|
|Ögonblicksbilder|Stöds inte ännu|[Skapa och hantera en BLOB-ögonblicksbild i .NET](storage-blob-snapshots.md)|
|Mjuk borttagning|Stöds inte ännu|[Mjuk borttagning för Azure Storage-blobar](storage-blob-soft-delete.md)|
|Statiska webbplatser|Stöds inte ännu|[Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website.md)|
|Logga in Azure Monitor|Stöds inte ännu|Ännu inte tillgängligt|
|Premium block-blobar|Stöds inte ännu|[Skapa ett BlockBlobStorage-konto](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Se även

- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)