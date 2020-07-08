---
title: Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om vilka Blob Storage-funktioner som du kan använda med Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 15e6cf7adfda995148a75ec21b8d8e5d8a5cab2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559073"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2

Blob Storage funktioner som [diagnostisk loggning](../common/storage-analytics-logging.md), [åtkomst nivåer](storage-blob-storage-tiers.md)och [Blob Storage hanterings principer för livs cykeln](storage-lifecycle-management-concepts.md) fungerar nu med konton som har ett hierarkiskt namn område. Därför kan du aktivera hierarkiska namn rymder på Blob Storage-kontona utan att förlora åtkomsten till dessa funktioner.

I den här tabellen visas de Blob Storage-funktioner som du kan använda med Azure Data Lake Storage Gen2. De objekt som visas i tabellerna ändras med tiden då stödet fortsätter att expandera. Mer information om specifika problem som är associerade med förhands gransknings statusen för en funktion finns i artikeln om [kända problem](data-lake-storage-known-issues.md) .

## <a name="supported-blob-storage-features"></a>Blob Storage-funktioner som stöds

> [!NOTE]
> Support nivån avser endast hur funktionen stöds med Data Lake Storage Gen2. 
>
> [BlockBlobStorage-konton för Premium-prestanda](storage-blob-create-account-block-blob.md) för data Lake Storage Gen2 finns för närvarande i offentlig för hands version. Support-nivåer för dessa typer av konton visas i kolumnen **BlockBlobStorage (Premium)** .

|Blob Storage funktion |Allmänt-syfte v2 |BlockBlobStorage (Premium) |Relaterade artiklar |
|---------------|-------------------|---|
|Frekvent åtkomstnivå|Allmänt tillgänglig|Stöds inte|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Lågfrekvent åtkomstnivå|Allmänt tillgänglig|Stöds inte|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Händelser|Allmänt tillgänglig|Förhandsgranskning|[Reagera på Blob Storage-händelser](storage-blob-event-overview.md)|
|Mått (klassisk)|Allmänt tillgänglig|Stöds inte|[Azure Storage analys mått (klassisk)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Mått i Azure Monitor|Allmänt tillgänglig|Förhandsgranskning|[Azure Storage mått i Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell-kommandon för Blob Storage|Allmänt tillgänglig|Förhandsgranskning|[Snabb start: Ladda upp, ladda ned och lista blobar med PowerShell](storage-quickstart-blobs-powershell.md)|
|Blob Storage Azure CLI-kommandon|Allmänt tillgänglig|Förhandsgranskning|[Snabb start: skapa, ladda ned och lista blobar med Azure CLI](storage-quickstart-blobs-cli.md)|
|API: er för Blob Storage|Allmänt tillgänglig|Förhandsgranskning|[Snabb start: klient biblioteket för Azure Blob Storage-V12 för .NET](storage-quickstart-blobs-dotnet.md)<br>[Snabb start: hantera blobbar med Java V12 SDK](storage-quickstart-blobs-java.md)<br>[Snabb start: hantera blobbar med python V12 SDK](storage-quickstart-blobs-python.md)<br>[Snabb start: hantera blobbar med Java Script V12 SDK i Node.js](storage-quickstart-blobs-nodejs.md)|
|Diagnostikloggar|Allmänt tillgänglig|Förhandsgranskning <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Analysloggning i Azure Storage](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Arkiv åtkomst nivå|Allmänt tillgänglig|Stöds inte|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Logga in Azure Monitor|Förhandsgranskning |Stöds inte ännu|[Övervaknings Azure Storage](../common/monitor-storage.md)|
|Ögonblicksbilder|Förhandsgranskning|Stöds inte ännu|[BLOB-ögonblicksbilder](snapshots-overview.md)|
|Statiska webbplatser|Förhandsgranskning|Stöds inte ännu|[Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website.md)|
|Oåterkalleligt lagrings utrymme|Förhandsgranskning|Stöds inte ännu|[Lagra affärs kritiska BLOB-data med oföränderlig lagring](storage-blob-immutable-storage.md)|
|Principer för livs cykel hantering|Förhandsgranskning|Stöds inte ännu|[Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)|
|Blobfuse|Förhandsgranskning|Stöds inte ännu|[Montera Blob Storage som ett fil system med blobfuse](storage-how-to-mount-container-linux.md)|
|Ändringsfeed|Stöds inte ännu|Stöds inte ännu|[Ändra feed-stöd i Azure Blob Storage](storage-blob-change-feed.md)|
|Redundansväxling av konto|Stöds inte ännu|Stöds inte ännu|[Haveri beredskap och redundansväxling av konto](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB container ACL|Stöds inte ännu|Stöds inte ännu|[Ange behållar-ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Anpassade domäner|Stöds inte ännu|Stöds inte ännu|[Mappa en anpassad domän till en Azure Blob Storage-slutpunkt](storage-custom-domain-name.md)|
|Mjuk borttagning|Stöds inte ännu|Stöds inte ännu|[Mjuk borttagning för Azure Storage-blobar](storage-blob-soft-delete.md)|

<div id="diagnostic-logging"><sup>1</sup> för Premium Block Blob Storage-konton går det inte att aktivera diagnostikloggar (klassisk) med hjälp av Azure Portal. Aktivera dem med hjälp av PowerShell.</div>

## <a name="see-also"></a>Se även

- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)