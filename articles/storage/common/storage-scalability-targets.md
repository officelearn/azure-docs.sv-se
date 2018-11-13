---
title: Skalbarhets- och prestandamål i Azure Storage
description: Läs mer om skalbarhets- och prestandamål, inklusive kapaciteten, förfrågningar och inkommande och utgående bandbredd för standard Azure storage-konton.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/08/2018
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 93e09f3ab6780eb9ce7fa29b4554b53d796b6837
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564961"
---
# <a name="azure-storage-scalability-and-performance-targets-for-standard-storage-accounts"></a>Azure Storage-skalbarhets- och prestandamål för standard storage-konton

Den här artikeln beskriver mål för skalbarhet och prestanda för standard Azure storage-konton. Skalbarhets- och prestandamål som anges här är avancerade mål, men kan nås. I samtliga fall, förfrågningar och bandbredden som uppnås genom din lagring konto beror på storleken på objekt som lagras, åtkomstmönster som används, och vilken typ av arbetsbelastning programmet utför. 

Glöm inte att testa din tjänst för att avgöra om prestandan uppfyller dina krav. Om möjligt undviker plötsliga toppar i mängden trafik och se till att trafik är väl distribuerade över partitioner.

När programmet når gränsen för en partition kan hantera din arbetsbelastning, börjar Azure Storage att returnera Felkod 503 (servern är upptagen) eller felkoden 500 (tidsgräns för åtgärd)-svar. Om 503-fel inträffar, Överväg att ändra dina program att använda en exponentiell backoff-princip för återförsök. Exponentiell backoff möjliggör att belastningen på partitionen att minska och för att underlätta ut trafiktoppar till partitionen.

## <a name="standard-storage-account-scale-limits"></a>Skalningsgränserna för standard storage-konto
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

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
* [Prisinformation om lagring](https://azure.microsoft.com/pricing/details/storage/)
* [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md)
* [Azure Storage-replikering](../storage-redundancy.md)
* [Prestanda och skalbarhetschecklista för Microsoft Azure Storage](../storage-performance-checklist.md)

