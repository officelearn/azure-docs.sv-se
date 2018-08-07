---
title: Azure Storage skalbarhets- och prestandamål | Microsoft Docs
description: Läs mer om mål för skalbarhet och prestanda för Azure Storage, inklusive kapaciteten, förfrågningar och inkommande och utgående bandbredd för både standard och premium storage-konton. Förstå prestandamål för partitioner i var och en av Azure Storage-tjänsterna.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 1db7219beead4d7ee228baff4c802c105419ba1c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524611"
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Skalbarhets- och prestandamål i Azure Storage
## <a name="overview"></a>Översikt
Den här artikeln beskrivs i avsnitt för skalbarhet och prestanda för Azure Storage. En sammanfattning av andra Azure-gränser finns [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md).

> [!NOTE]
> Alla lagringskonton kör ny fast nätverkstopologi och stöd för de mål för skalbarhet och prestanda som beskrivs i den här artikeln, oavsett när de har skapats. Läs mer på Azure Storage platt nätverk-arkitektur och skalbarhet, [Microsoft Azure Storage: A med hög Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Skalbarhets- och prestandamål som anges här är avancerade mål, men kan nås. I samtliga fall, förfrågningar och bandbredden som uppnås genom din lagring konto beror på storleken på objekt som lagras, åtkomstmönster som används, och vilken typ av arbetsbelastning programmet utför. Glöm inte att testa din tjänst för att avgöra om prestandan uppfyller dina krav. Om möjligt undviker plötsliga toppar i mängden trafik och se till att trafik är väl distribuerade över partitioner.
> 
> När programmet når gränsen för en partition kan hantera din arbetsbelastning, börjar Azure Storage att returnera Felkod 503 (servern är upptagen) eller felkoden 500 (tidsgräns för åtgärd)-svar. Om dessa fel inträffar, sedan ditt program bör använda en exponentiell backoff-princip för återförsök. Exponentiell backoff möjliggör att belastningen på partitionen att minska och för att underlätta ut trafiktoppar till partitionen.
> 
> 

Om programmets behov överstiger det för skalbarhetsmål för ett enda lagringskonto, kan du skapa ditt program att använda flera lagringskonton. Du kan sedan partitionera dina dataobjekt över dessa lagringskonton. Se [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) för information om Volympriser.

## <a name="scalability-targets-for-a-storage-account"></a>Skalbarhetsmål för ett lagringskonto
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Skala prestandamål i Azure Blob storage
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure filer skala mål
Mer information om mål för skalbarhet och prestanda för Azure Files och Azure File Sync finns [skalbarhets- och prestandamål i Azure Files](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync skala mål
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
* [Microsoft Azure Storage: En med hög tillgänglighet Molnlagringstjänst med stark konsekvens](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

