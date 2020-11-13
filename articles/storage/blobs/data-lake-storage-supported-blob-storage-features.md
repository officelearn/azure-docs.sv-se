---
title: Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om vilka Blob Storage-funktioner som du kan använda med Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b670ec3cee2ebd1cc98ea2e04fb2b0dfd90e4e1a
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594223"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2

Blob Storage funktioner som [diagnostisk loggning](../common/storage-analytics-logging.md), [åtkomst nivåer](storage-blob-storage-tiers.md)och [Blob Storage hanterings principer för livs cykeln](storage-lifecycle-management-concepts.md) fungerar nu med konton som har ett hierarkiskt namn område. Därför kan du aktivera hierarkiska namn rymder på Blob Storage-kontona utan att förlora åtkomsten till dessa funktioner.

I den här tabellen visas de Blob Storage-funktioner som du kan använda med Azure Data Lake Storage Gen2. De objekt som visas i tabellerna ändras med tiden då stödet fortsätter att expandera. Mer information om specifika problem som är associerade med förhands gransknings statusen för en funktion finns i artikeln om [kända problem](data-lake-storage-known-issues.md) .

## <a name="supported-blob-storage-features"></a>Blob Storage-funktioner som stöds

Följande tabell visar hur varje Blob Storage-funktion stöds med Data Lake Storage Gen2. Det finns en kolumn för prestanda nivåerna standard och [Premium](premium-tier-for-data-lake-storage.md) . 

|Blob Storage funktion |Standard |Premium |Relaterade artiklar |
|---------------|-------------------|---|
|Frekvent lagringsnivå|Allmänt tillgänglig|Stöds inte|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Lågfrekvent åtkomstnivå|Allmänt tillgänglig|Stöds inte|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Händelser|Allmänt tillgänglig|Allmänt tillgänglig|[Reagera på Blob Storage-händelser](storage-blob-event-overview.md)|
|Mått (klassisk)|Allmänt tillgänglig|Allmänt tillgänglig|[Azure Storage analys mått (klassisk)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Mått i Azure Monitor|Allmänt tillgänglig|Förhandsgranskning|[Azure Storage-mått i Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell-kommandon för Blob Storage|Allmänt tillgänglig|Allmänt tillgänglig|[Snabb start: Ladda upp, ladda ned och lista blobar med PowerShell](storage-quickstart-blobs-powershell.md)|
|Blob Storage Azure CLI-kommandon|Allmänt tillgänglig|Allmänt tillgänglig|[Snabb start: skapa, ladda ned och lista blobar med Azure CLI](storage-quickstart-blobs-cli.md)|
|API: er för Blob Storage|Allmänt tillgänglig|Allmänt tillgänglig|[Snabbstart: Azure Blob Storage-klientbiblioteket v12 för .NET](storage-quickstart-blobs-dotnet.md)<br>[Snabb start: hantera blobbar med Java V12 SDK](storage-quickstart-blobs-java.md)<br>[Snabb start: hantera blobbar med python V12 SDK](storage-quickstart-blobs-python.md)<br>[Snabb start: hantera blobbar med Java Script V12 SDK i Node.js](storage-quickstart-blobs-nodejs.md)|
|Diagnostikloggar|Allmänt tillgänglig|Förhandsgranskning |[Analysloggning i Azure Storage](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Arkiv åtkomst nivå|Allmänt tillgänglig|Stöds inte|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Policyer för livs cykel hantering (skiktning)|Allmänt tillgänglig|Stöds inte ännu|[Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)|
|Principer för livs cykel hantering (ta bort BLOB)|Allmänt tillgänglig|Allmänt tillgänglig|[Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)|
|Logga in Azure Monitor|Förhandsgranskning |Förhandsgranskning|[Övervaknings Azure Storage](../common/monitor-storage.md)|
|Ögonblicksbilder|Förhandsgranskning|Förhandsgranskning|[BLOB-ögonblicksbilder](snapshots-overview.md)|
|Statiska webbplatser|Förhandsgranskning|Förhandsgranskning|[Hantering av statisk webbplats i Azure Storage](storage-blob-static-website.md)|
|Oåterkalleligt lagrings utrymme|Förhandsgranskning|Förhandsgranskning|[Lagra affärs kritiska BLOB-data med oföränderlig lagring](storage-blob-immutable-storage.md)|
|Mjuk borttagning av behållare|Förhandsgranskning|Förhandsgranskning|[Mjuk borttagning för behållare (förhands granskning)](soft-delete-container-overview.md)|
|BLOB-mjuk borttagning|Stöds inte ännu|Stöds inte ännu|[Mjuk borttagning för blobar](storage-blob-soft-delete.md)|
|Blobfuse|Allmänt tillgänglig|Allmänt tillgänglig|[Montera Blob Storage som ett fil system med blobfuse](storage-how-to-mount-container-linux.md)|
|Redundansväxling av konto|Stöds inte ännu|Stöds inte ännu|[Haveri beredskap och redundansväxling av konto](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB container ACL|Stöds inte<div role="complementary" aria-labelledby="blob-container-ACL"><sup>1</sup></div>|Stöds inte<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|Se den relaterade anteckningen under den här tabellen.|
|Kund-tillhandahållna nycklar|Stöds inte ännu|Stöds inte ännu|[Ange en krypterings nyckel för en begäran till Blob Storage](encryption-customer-provided-keys.md)|
|Anpassade domäner|Stöds inte ännu|Stöds inte ännu|[Mappa en anpassad domän till en Azure Blob Storage-slutpunkt](storage-custom-domain-name.md)|
|Krypteringsomfång|Stöds inte ännu|Stöds inte ännu|[Skapa och hantera krypterings omfång (förhands granskning)](encryption-scope-manage.md)|
|Ändringsfeed|Stöds inte ännu|Stöds inte ännu|[Ändra feed-stöd i Azure Blob Storage](storage-blob-change-feed.md)|
|Objektreplikering|Stöds inte ännu|Stöds inte ännu|[Konfigurera objekt replikering för block-blobar](object-replication-configure.md)|
|BLOB-versioner|Stöds inte ännu|Stöds inte ännu|[Aktivera och hantera BLOB-versioner](versioning-enable.md)|

<div id="blob-container-ACL"><sup>1</sup> du kan ange ACL: er i rotmappen för behållaren, men inte själva behållaren.</div><br>

<div id="preview-form"><sup>2</sup> Om du vill använda ögonblicks bilder, oföränderligt lagrings utrymme eller statiska webbplatser med Data Lake Storage Gen2 måste du registrera dig i förhands granskningen genom att fylla i det här <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formuläret</a>.  </div>

## <a name="see-also"></a>Se även

- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
