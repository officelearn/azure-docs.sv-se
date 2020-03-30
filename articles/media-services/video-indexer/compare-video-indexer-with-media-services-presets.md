---
title: Jämförelse av videoindexerare och Azure Media Services v3-förinställningar
description: I den här artikeln jämförs videoindexerfunktioner och Azure Media Services v3-förinställningar.
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
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: dcfc6ea4afe23424e72c625518356be52f62bc81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602186"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Jämför Azure Media Services v3-förinställningar och videoindexerare 

I den här artikeln jämförs funktionerna i **VIDEO Indexer API:er** och **Media Services v3 API:er**. 

För närvarande finns det en överlappning mellan funktioner som erbjuds av [Video Indexer API:er](https://api-portal.videoindexer.ai/) och [Api:erna för Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Följande tabell innehåller den aktuella riktlinjen för att förstå skillnader och likheter. 

## <a name="compare"></a>Jämför

|Funktion|API:er för videoindexerare |Förinställningar för videoanalysator och ljudanalysator<br/>i Media Services v3 API:er|
|---|---|---|
|Medieinsikter|[Optimerad](video-indexer-output-json-v2.md) |[Grunder](../latest/intelligence-concept.md)|
|Erfarenheter|Se hela listan över funktioner som stöds: <br/> [Översikt](video-indexer-overview.md)|Returnerar endast videostatistik|
|Fakturering|[Prissättning av Medietjänster](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Prissättning av Medietjänster](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Efterlevnad|De senaste efterlevnadsuppdateringarna finns i [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) och söker efter "Video Indexer" för att se om det följer ett certifikat av intresse.|De senaste efterlevnadsuppdateringarna finns i [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) och söker efter "Medietjänster" för att se om det följer ett certifikat av intresse.|
|Kostnadsfri utvärderingsversion|USA, östra|Inte tillgängligt|
|Regional tillgänglighet|Se [Cognitive Services tillgänglighet efter region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Se [Tillgänglighet för Medietjänster per region](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)

[Översikt över Media Services v3](../latest/media-services-overview.md)
