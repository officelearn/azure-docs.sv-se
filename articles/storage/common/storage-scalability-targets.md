---
title: Azure skalbarhets- och prestandamål i Storage - konton
description: Läs mer om skalbarhets- och prestandamål, inklusive kapaciteten, förfrågningar och inkommande och utgående bandbredd för Azure storage-konton.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 96322c730300e360ed03f4b623db2a7f18825f55
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267709"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Azure Storage-skalbarhets- och prestandamål för storage-konton

Den här artikeln beskriver mål för skalbarhet och prestanda för Azure storage-konton. Skalbarhets- och prestandamål som anges här är avancerade mål, men kan nås. I samtliga fall, förfrågningar och bandbredden som uppnås genom din lagring konto beror på storleken på objekt som lagras, åtkomstmönster som används, och vilken typ av arbetsbelastning programmet utför.

Glöm inte att testa din tjänst för att avgöra om prestandan uppfyller dina krav. Om möjligt undviker plötsliga toppar i mängden trafik och se till att trafik är väl distribuerade över partitioner.

När programmet når gränsen för en partition kan hantera din arbetsbelastning, börjar Azure Storage att returnera Felkod 503 (servern är upptagen) eller felkoden 500 (tidsgräns för åtgärd)-svar. Om 503-fel inträffar, Överväg att ändra dina program att använda en exponentiell backoff-princip för återförsök. Exponentiell backoff möjliggör att belastningen på partitionen att minska och för att underlätta ut trafiktoppar till partitionen.

## <a name="storage-account-scale-limits"></a>Skalningsgränserna för Storage-konto

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Skala lagringskontogränser för Premium prestanda

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Skalningsgränserna för Storage resource provider

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Skala prestandamål i Azure Blob storage

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure filer skala mål

Mer information om mål för skalbarhet och prestanda för Azure Files och Azure File Sync finns [skalbarhets- och prestandamål i Azure Files](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Premium-filer skala mål

Det finns tre typer av begränsningar att överväga för premium-filer: storage-konton, resurser och filer.

Exempel: En enda resurs kan uppnå 100 000 IOPS och en enskild fil kan skala upp till 5 000 IOPS. Därför är till exempel, om du har tre filer i en resurs, de högsta IOPs som du kan hämta från resursen 15 000.

#### <a name="premium-file-share-limits"></a>Resurs-gränser för Premium-fil

> [!IMPORTANT]
> Lagringskontogränser gäller för alla resurser. Skala upp till är max för storage-konton endast möjligt om det finns bara en resurs per lagringskonto.

|Område  |Mål  |
|---------|---------|
|Minstorlek                        |100 giB      |
|Maxstorlek                        |100 TiB      |
|Minimistorlek öka/minska    |1 GiB      |
|Baslinjen IOPS    |1 IOPS per GiB upp till 100 000|
|IOPS bursting    |3 x IOPS per GiB upp till 100 000|
|Minsta bandbredd                     |100        |
|Bandbredd |0,1 MB/s per GiB upp till 5 GiB/s     |
|Högsta antalet ögonblicksbilder        |200       |

#### <a name="premium-file-limits"></a>Gränser för Premium-fil

|Område  |Mål  |
|---------|---------|
|Storlek                  |1 TiB         |
|Max IOPS per fil     |5 000         |
|Samtidiga handtag    |2,000         |

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync skala mål

Azure File Sync har utformats med målet att obegränsad användning, men obegränsad användning är inte alltid möjligt. I följande tabell anger gränserna för Microsofts testning och anger även vilken riktar sig gränser:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Skala prestandamål för Azure storage-kö

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Skala prestandamål för Azure storage-tabell

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Se även

- [Prisinformation om Lagring](https://azure.microsoft.com/pricing/details/storage/)
- [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md)
- [Azure Storage Replication](../storage-redundancy.md)
- [Prestanda och skalbarhetschecklista för Microsoft Azure Storage](../storage-performance-checklist.md)