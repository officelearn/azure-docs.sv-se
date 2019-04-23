---
title: Hantera Azure CDN-Cachelagringsprincip i Azure Media Services | Microsoft Docs
description: Lär dig hur du hanterar Azure CDN-Cachelagringsprincip i Azure Media Services.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 516df2f6177303987fc0354dde647c1fc26820ef
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798612"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Hantera Azure CDN-Cachelagringsprincip i Azure Media Services
Azure Media Services tillhandahåller en HTTP-baserad anpassad strömning och progressiv nedladdning. HTTP-baserade direktuppspelning är mycket skalbara med fördelarna med cachelagring i proxy och CDN-lager samt klientcachelagring. Slutpunkter för direktuppspelning ger allmänna strömmande funktioner och konfiguration för HTTP-huvuden för cache. Slutpunkter för direktuppspelning anger HTTP Cache-Control: maxåldern och Expires-huvuden. Du kan få mer information för HTTP-cache rubriker från [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Standard Caching rubriker
Som standard tillämpar strömmande slutpunkter 3 dagars cache rubriker för på begäran strömmande data (faktiska media fragment/segment) och manifest(playlist). Slutpunkter för direktuppspelning gäller 3 dagars cache-huvuden för data (faktiska media fragment/segment) och 2 sekunder cachelagra rubrik för manifest(playlist) begäranden för direktsänd strömning. När direktsänd program övergår i (live-arkivet) på begäran, tillämpas på begäran strömmande cache-huvuden.

## <a name="azure-cdn-integration"></a>Integrering med Azure CDN
Azure Media Services innehåller [integrerad CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) för strömmande slutpunkter. Cache-control-huvuden gäller på samma sätt som slutpunkter för direktuppspelning till CDN aktiverat slutpunkter för direktuppspelning. Azure CDN använder slutpunkt för direktuppspelning konfigurerats cache värden för att definiera livstiden för internt cachelagrade objekt och också det här värdet används för att ange leveransen cache-huvuden. Det rekommenderas inte att ange värden för små cache när med CDN aktiverat slutpunkter för direktuppspelning. Små inställningsvärden minska prestanda och minska fördelen med CDN. Det går inte att ange cache-huvuden som är mindre än 600 sekunder för CDN aktiverat slutpunkter för direktuppspelning.

> [!IMPORTANT]
>Azure Media Services har fullständig integrering med Azure CDN. Du kan integrera alla tillgängliga Azure CDN-leverantörer till slutpunkten för direktuppspelning, inklusive standard och premium produkter med ett enda klick. Mer information finns i den här [meddelande](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Kostnaderna från direktuppspelning till CDN hämtar endast inaktiverade om CDN är aktiverat via strömningsslutpunkt API: er eller med hjälp av Azure-portalens slutpunkt strömningsavsnittet. Manuell integration eller direkt skapa en CDN-slutpunkt med hjälp av CDN-API: er eller portalen avsnittet Inaktivera inte dataavgifter.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurera cache-huvuden med Azure Media Services
Du kan använda Azure portal eller API: er för Azure Media Services för att konfigurera huvudvärden för cache.

1. Om du vill konfigurera cache-huvuden med hjälp av Azure portal, referera till [hur du hanterar Strömningsslutpunkter](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) avsnittet Konfigurera slutpunkt för direktuppspelning.
2. Azure Media Services REST API, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [StreamingEndpointCacheControl egenskaper](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Prioritetsordning för cache-konfiguration
1. Azure Media Services konfigurerats cache värdet åsidosätter standardvärdet.
2. Om det finns ingen manuell konfiguration, tillämpa standardvärdena.
3. Som standard 2 sekunder cache rubriker som gäller för live direktuppspelning manifest(playlist) oavsett Azure Media eller Azure Storage och åsidosätta för det här värdet är inte tillgänglig.

