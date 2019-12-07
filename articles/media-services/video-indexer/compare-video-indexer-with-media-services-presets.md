---
title: Jämförelse av Video Indexer och Azure Media Services v3-för inställningar | Microsoft Docs
description: I den här artikeln jämförs Video Indexer-funktioner och Azure Media Services v3-för inställningar.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 055e210efbb01268654e7823a0fc1320e9c27646
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892829"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Jämför Azure Media Services v3 för inställningar och Video Indexer 

I den här artikeln jämförs funktionerna i **video Indexer API** : er och **Media Services v3-API: er**. 

För närvarande finns det en överlappning mellan funktioner som erbjuds av [video Indexer-API](https://api-portal.videoindexer.ai/) : er och [Media Services v3-API: er](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Följande tabell innehåller den aktuella rikt linjen för att förstå skillnaderna och likheter. 

## <a name="compare"></a>Jämför

|Funktion|Video Indexer-API: er |Inställningar för video analys och ljud analys<br/>i Media Services v3-API: er|
|---|---|---|
|Medie insikter|[Förbättring](video-indexer-output-json-v2.md) |[Grunderna](../latest/intelligence-concept.md)|
|Erfarenheter|Se den fullständiga listan över funktioner som stöds: <br/> [Översikt](video-indexer-overview.md)|Returnerar endast video insikter|
|Fakturering|[Media Services priser](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services priser](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Efterlevnad|[Iso 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1, 2, 3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)och [HiTRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) Certified. För de senaste uppdateringarna går du till [aktuella certifierings status för video Indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Media Services är kompatibel med många certifieringar. Kolla i [Azure Compliance-erbjudanden. pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) och Sök efter "Media Services" för att se om det överensstämmer med ett certifikat av intresse.|
|Kostnadsfri utvärderingsversion|USA, östra|Inte tillgänglig|
|Tillgänglighet för regioner|USA, östra 2, södra centrala USA, västra USA 2, norra Europa, Västeuropa, Sydostasien, Asien, östra och östra Australien.  De senaste uppdateringarna finns på sidan [produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) .|Se [Azure-status](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)

[Översikt över Media Services v3](../latest/media-services-overview.md)
