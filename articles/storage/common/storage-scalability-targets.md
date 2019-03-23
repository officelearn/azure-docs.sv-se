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
ms.openlocfilehash: ca9c4c959d21f26369600129f3897b7624dd84f2
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371182"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Azure Storage-skalbarhets- och prestandamål för storage-konton

Den här artikeln beskriver mål för skalbarhet och prestanda för Azure storage-konton. Skalbarhets- och prestandamål som anges här är avancerade mål, men kan nås. I samtliga fall, förfrågningar och bandbredden som uppnås genom din lagring konto beror på storleken på objekt som lagras, åtkomstmönster som används, och vilken typ av arbetsbelastning programmet utför.

Glöm inte att testa din tjänst för att avgöra om prestandan uppfyller dina krav. Om möjligt undviker plötsliga toppar i mängden trafik och se till att trafik är väl distribuerade över partitioner.

När programmet når gränsen för en partition kan hantera din arbetsbelastning, börjar Azure Storage att returnera Felkod 503 (servern är upptagen) eller felkoden 500 (tidsgräns för åtgärd)-svar. Om 503-fel inträffar, Överväg att ändra dina program att använda en exponentiell backoff-princip för återförsök. Exponentiell backoff möjliggör att belastningen på partitionen att minska och för att underlätta ut trafiktoppar till partitionen.

## <a name="standard-performance-storage-account-scale-limits"></a>Skalningsgränserna för standardprestanda storage-konto

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

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync skala mål

Azure File Sync har utformats med målet att obegränsad användning, men obegränsad användning är inte alltid möjligt. I följande tabell anger gränserna för Microsofts testning och anger även vilken riktar sig gränser:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Skala prestandamål för Azure storage-kö

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Skala prestandamål för Azure storage-tabell

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Se även

- [Prisinformation om lagring](https://azure.microsoft.com/pricing/details/storage/)
- [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md)
- [Azure Storage Replication](../storage-redundancy.md)
- [Prestanda och skalbarhetschecklista för Microsoft Azure Storage](../storage-performance-checklist.md)