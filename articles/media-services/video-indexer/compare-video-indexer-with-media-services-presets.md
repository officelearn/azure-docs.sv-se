---
title: Jämförelse av Video Indexer och Azure Media Services v3 förinställningar | Microsoft Docs
description: Det här avsnittet jämför Video Indexer och Azure Media Services v3 förinställningar.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: juliako
ms.openlocfilehash: 968614d183fe6857336ea92791ba4f1d279a5016
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198107"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Jämför Azure Media Services v3 förinställningar och Video Indexer 

Den här artikeln jämförs funktionerna i **Video Indexer API: er** och **API: er för Media Services v3**. 

För närvarande finns ett överlapp mellan funktioner som erbjuds av den [API: er för Video Indexer-v2](https://api-portal.videoindexer.ai/) och [API: er för Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Följande tabell ger de aktuella riktlinjerna för att förstå skillnader och likheter. 

## <a name="compare"></a>Jämför

|Funktion|Video Indexer API: er |Video Analyzer och ljud Analyzer förinställningar<br/>i API: er för Media Services v3|
|---|---|---|
|Media-insikter|[Förbättrad](video-indexer-output-json-v2.md) |[Grunderna](../latest/intelligence-concept.md)|
|Upplevelser|Se en fullständig lista över funktioner som stöds: <br/> [Översikt](video-indexer-overview.md)|Returnerar endast videoinsikter|
|Fakturering|[Prissättningen av Medietjänsterna](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Prissättningen av Medietjänsterna](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Efterlevnad|[Efterlevnad i Azure](https://aka.ms/AzureCompliance)|Media Services är kompatibelt med många certifieringar. Kolla in [Azure efterlevnad Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) och Sök efter ”Media Services” för att se om den överensstämmer med ett certifikat av intresse.|
|Kostnadsfri utvärderingsversion|Östra USA|Inte tillgängligt|
|Tillgänglighet |Västra USA, östra Asien, Nordeuropa|Se [Azure-status](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)

[Översikt över Media Services v3](../latest/media-services-overview.md)
