---
title: Azure Storage skalbarhets-och prestanda mål – lagrings konton
description: Lär dig mer om skalbarhets-och prestanda mål, inklusive kapacitet, begär ande frekvens och inkommande och utgående bandbredd för Azure Storage-konton.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 046c2308d5cef2df7e12b6185fc24b8df4f821dc
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326955"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Azure Storage skalbarhets-och prestanda mål för lagrings konton

Den här artikeln beskriver skalbarhets-och prestanda målen för Azure Storage-konton. De skalbarhets-och prestanda mål som anges här är avancerade mål, men kan nås. I samtliga fall beror den begär ande frekvensen och bandbredden som uppnås av ditt lagrings konto på storleken på objekt som lagras, åtkomst mönster som används och vilken typ av arbets belastning som programmet utför.

Se till att testa tjänsten för att avgöra om prestandan uppfyller dina krav. Undvik om möjligt plötsliga toppar i trafik hastigheten och se till att trafiken är väl distribuerad mellan partitioner.

När ditt program når gränsen för vad en partition kan hantera för arbets belastningen börjar Azure Storage returnera felkoden 503 (servern är upptagen) eller felkod 500 (åtgärds tids gräns). Om 503-fel uppstår bör du överväga att ändra ditt program så att det använder en exponentiell backoff-princip för återförsök. Med exponentiell backoff kan belastningen på partitionen minskas, och för att under lätta toppar i trafik till den partitionen.

## <a name="storage-account-scale-limits"></a>Skalnings gränser för lagrings konto

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Skalnings gränser för Premium-prestanda för lagrings konto

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Skalnings gränser för Storage Resource Provider

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Skalnings mål för Azure Blob Storage

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure filer skala mål

Mer information om skalnings-och prestanda mål för Azure Files och Azure File Sync finns i [Azure Files skalbarhets-och prestanda mål](../files/storage-files-scale-targets.md).

> [!IMPORTANT]
> Lagrings konto gränser gäller för alla resurser. Det går bara att skala upp till max för lagrings konton om det bara finns en resurs per lagrings konto.
>
> Standard fil resurser som är större än 5 TiB finns i för hands versionen och har vissa begränsningar.
> En lista över begränsningar och att publicera till för hands versionen av dessa större fil resurs storlekar finns i avsnittet [standard fil resurser](../files/storage-files-planning.md#standard-file-shares) i Azure Files Planning Guide.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Skala mål för Premium filer

Det finns tre typer av begränsningar att överväga för Premium-filer: lagrings konton, resurser och filer.

Exempel: En enda resurs kan uppnå 100 000 IOPS och en enda fil kan skala upp till 5 000 IOPS. Om du till exempel har tre filer i en resurs är Max IOPs som du kan hämta från resursen 15 000.

#### <a name="premium-file-share-limits"></a>Premium fil resurs gränser

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync skala mål

Azure File Sync har utformats med målet obegränsad användning, men obegränsad användning är inte alltid möjlig. Följande tabell visar gränserna för Microsofts testning och indikerar även vilka mål som är hårda gränser:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Skalnings mål för Azure Queue Storage

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Skalnings mål för Azure Table Storage

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Se också

- [Pris information om lagring](https://azure.microsoft.com/pricing/details/storage/)
- [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md)
- [Azure Storage replikering](../storage-redundancy.md)
- [Prestanda och skalbarhetschecklista för Microsoft Azure Storage](../storage-performance-checklist.md)
