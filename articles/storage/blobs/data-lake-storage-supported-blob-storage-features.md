---
title: Blob-lagringsfunktioner som är tillgängliga i Azure Data Lake Storage Gen2 | Microsoft-dokument
description: Lär dig mer om vilka Blob-lagringsfunktioner du kan använda med Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b270ce3cd15dbd1e8dd53bd60376a87d6e08f75c
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637218"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Blob-lagringsfunktioner som är tillgängliga i Azure Data Lake Storage Gen2

Blob [Storage-funktioner som diagnostikloggning,](../common/storage-analytics-logging.md) [åtkomstnivåer](storage-blob-storage-tiers.md)och [livscykelhanteringsprinciper för Blob Storage](storage-lifecycle-management-concepts.md) fungerar nu med konton som har ett hierarkiskt namnområde. Därför kan du aktivera hierarkiska namnområden på dina Blob-lagringskonton utan att förlora åtkomsten till dessa funktioner.

I den här tabellen visas de Blob-lagringsfunktioner som du kan använda med Azure Data Lake Storage Gen2. De objekt som visas i dessa tabeller ändras med tiden när stödet fortsätter att expanderas.

## <a name="supported-blob-storage-features"></a>Blob-lagringsfunktioner som stöds

> [!NOTE]
> Supportnivå refererar endast till hur funktionen stöds med Data Lake Storage Gen2.

|Funktionen Blob Storage |Stödnivå |Relaterade artiklar |
|---------------|-------------------|---|
|Frekvent åtkomstnivå|Allmänt tillgänglig|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Lågfrekvent åtkomstnivå|Allmänt tillgänglig|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Händelser|Allmänt tillgänglig|[Reagera på Blob Storage-händelser](storage-blob-event-overview.md)|
|Mått (klassiskt)|Allmänt tillgänglig|[Azure Storage analytics-mått (Klassisk)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Mått i Azure Monitor|Allmänt tillgänglig|[Azure Storage-mått i Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell-kommandon för Blob-lagring|Allmänt tillgänglig|[Snabbstart: Ladda upp, ladda ned och lista blobbar med PowerShell](storage-quickstart-blobs-powershell.md)|
|Azure CLI-kommandon för Blob storage|Allmänt tillgänglig|[Snabbstart: Skapa, hämta och lista blobbar med Azure CLI](storage-quickstart-blobs-cli.md)|
|API:er för bloblagring|Allmänt tillgänglig|[Snabbstart: Azure Blob storage client library v12 för .NET](storage-quickstart-blobs-dotnet.md)<br>[Snabbstart: Hantera blobbar med Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Snabbstart: Hantera blobbar med Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Snabbstart: Hantera blobbar med JavaScript v12 SDK i Node.js](storage-quickstart-blobs-nodejs.md)|
|Arkivåtkomstnivå|Förhandsversion|[Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring](storage-blob-storage-tiers.md)|
|Principer för livscykelhantering|Förhandsversion|[Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)|
|Diagnostikloggar|Allmänt tillgänglig|[Loggning med Azure-lagringsanalys](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Ändringsfeed|Ännu inte stöds|[Ändra feedstöd i Azure Blob-lagring](storage-blob-change-feed.md)|
|Fellöse konto|Ännu inte stöds|[Katastrofåterställning och växlings över kontot](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob-behållare ACL|Ännu inte stöds|[Ange behållare ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Anpassade domäner|Ännu inte stöds|[Mappa en anpassad domän till en slutpunkt för Azure Blob-lagring](storage-custom-domain-name.md)|
|Oföränderlig lagring|Ännu inte stöds|[Lagra affärskritiska blob-data med oföränderlig lagring](storage-blob-immutable-storage.md)|
|Ögonblicksbilder|Ännu inte stöds|[Skapa och hantera en blob-ögonblicksbild i .NET](storage-blob-snapshots.md)|
|Mjuk borttagning|Ännu inte stöds|[Mjuk borttagning för Azure Storage-blobar](storage-blob-soft-delete.md)|
|Statiska webbplatser|Ännu inte stöds|[Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website.md)|
|Logga in i Azure Monitor|Ännu inte stöds|Ännu inte tillgängligt|
|Premium blockblobar|Ännu inte stöds|[Skapa ett BlockBlobStorage-konto](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Se även

- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)