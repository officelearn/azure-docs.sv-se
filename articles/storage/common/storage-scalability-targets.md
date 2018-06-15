---
title: Azure Storage skalbarhets- och prestandamål | Microsoft Docs
description: Läs mer om skalbarhets- och prestandamål för Azure Storage, inklusive kapacitet, förfrågningar och inkommande och utgående bandbredd för både standard och premium storage-konton. Förstå prestandamål för partitioner inom varje Azure Storage-tjänster.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: e393bb9e7615b893699caf5a931ede5803046892
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239692"
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Skalbarhets- och prestandamål i Azure Storage
## <a name="overview"></a>Översikt
Den här artikeln beskriver avsnitt skalbarhet och prestanda för Azure Storage. En sammanfattning av andra Azure gränser finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../../azure-subscription-service-limits.md).

> [!NOTE]
> Alla lagringskonton körs på den nya flat nätverkstopologin och stöd för skalbarhet och prestanda mål som beskrivs i den här artikeln, oavsett när de har skapats. Läs mer på Azure Storage platt nätverk-arkitektur och skalbarhet [Microsoft Azure Storage: A hög Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Skalbarhets- och prestandamål som listas här är avancerade mål, men den kan nås. I samtliga fall, förfrågningar och bandbredden som uppnås genom din lagring konto beror på storleken på objekt som lagras, åtkomstmönster som används, och vilken typ av arbetsbelastning programmet utför. Se till att testa tjänsten för att avgöra om dess prestanda uppfyller dina krav. Om möjligt undviker plötsliga toppar i mängden trafik och kontrollera att trafik som är korrekt distribuerad mellan partitioner.
> 
> När programmet når gränsen för en partition kan hantera för din arbetsbelastning, börjar Azure Storage att returnera Felkod 503 (servern är upptagen) eller felkoden 500 (åtgärden Timeout) svar. Om dessa fel inträffar, bör sedan ditt program använda en princip för exponentiell backoff för återförsök. Exponentiell backoff möjliggör att belastningen på partitionen minska och övergång toppar i trafiken till den aktuella partitionen.
> 
> 

Om behov av programmet överskrider skalbarhetsmål för ett enda lagringskonto, kan du skapa ditt program att använda flera lagringskonton. Du kan sedan partitionera dataobjekt mellan dessa lagringskonton. Se [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) för information om Volympriser.

## <a name="scalability-targets-for-a-storage-account"></a>Skalbarhetsmål för ett lagringskonto
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob storage skala mål
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure filer skala mål
Mer information om mål för skalning och prestanda för Azure-filer och Azure filsynkronisering finns [skalbarhets- och prestandamål för Azure-filer](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure filsynkronisering skala mål
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure Queue storage skala mål
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table storage skala mål
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Se även
* [Storage-priser](https://azure.microsoft.com/pricing/details/storage/)
* [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../../azure-subscription-service-limits.md)
* [Azure Storage-replikering](../storage-redundancy.md)
* [Prestanda och skalbarhetschecklista för Microsoft Azure Storage](../storage-performance-checklist.md)
* [Microsoft Azure Storage: En högtillgänglig lagring molntjänst med stark konsekvens](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

