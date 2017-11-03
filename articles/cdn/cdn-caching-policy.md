---
title: Hantera Azure CDN cachelagring principen i Azure Media Services | Microsoft Docs
description: "Lär dig hur du hanterar Azure CDN cachelagring principen i Azure Media Services."
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: 
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 0c479a58f4158bb1a72dc43432507160f65d2791
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Hantera Azure CDN cachelagring principen i Azure Media Services
Azure Media Services innehåller HTTP-baserade anpassningsbar strömning och progressiv hämtning. HTTP-baserade direktuppspelning är mycket skalbart med fördelarna med cachelagring i proxy- och CDN lager samt cachelagring på klientsidan. Strömningsslutpunkter innehåller allmänna strömmande funktioner och konfiguration för cache-HTTP-huvuden. Strömningsslutpunkter anger HTTP Cache-Control: maximal ålder och Expires-huvuden. Du kan få mer information om HTTP-huvuden cache från [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Standard cachelagring rubriker
Som standard gäller strömmande slutpunkter 3 dag cache-huvuden för strömning på begäran-data (faktiska media fragment/segment) och manifest(playlist). För direktsänd strömning strömningsslutpunkter gäller 3 dag cache-huvuden för data (faktiska media fragment/segment) och 2 sekunder cache-huvud för manifest(playlist) begäranden. När levande programmet övergår i (arkivera live) på begäran gäller på begäran strömmande cache-huvuden.

## <a name="azure-cdn-integration"></a>Azure CDN-integrering
Azure Media Services innehåller [integrerad CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) för strömmande slutpunkter. Cache-control huvuden gäller på samma sätt som strömningsslutpunkter till CDN aktiverad strömningsslutpunkter. Azure CDN använder strömningsslutpunkt konfigurerad cache-värden för att definiera livslängden för internt cachelagrade objekt och även det här värdet används för att ange leveransen cache-rubriker. Det rekommenderas inte att ange värden för små cachen när CDN aktiverat strömningsslutpunkter. Inställningsvärden små minska prestandan och minska fördelen med CDN. Det går inte att ange cache rubriker som är mindre än 600 sekunder för CDN aktiverat strömningsslutpunkter.

> [!IMPORTANT]
>Azure Media Services har fullständig integrering med Azure CDN. Du kan integrera alla tillgängliga Azure CDN providrar (Akamai och Verizon) att din strömningsslutpunkt inklusive CDN Standard och Premium produkter med en enda klickning. Mer information finns i det här [meddelande](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Avgifter för data från strömmande slutpunkten för Innehållsleveransnätverk hämtar endast inaktiverade om CDN aktiveras via strömningsslutpunkt API: er eller med Azure-hanteringsportalen strömmande slutpunkt. Manuell integrering eller direkt skapa en CDN-slutpunkten med hjälp av CDN-API: er eller portal avsnittet inaktiveras inte avgifterna data.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurera cache-huvuden med Azure Media Services
Du kan använda Azure-hanteringsportalen eller Azure Media Services API: er för att konfigurera värden i huvudet cache.

1. Konfigurera cache-rubriker med hjälp av hanteringsportalen Se [hur du hanterar Strömningsslutpunkter](../media-services/media-services-portal-manage-streaming-endpoints.md) avsnittet Konfigurera Strömningsslutpunkten.
2. Azure Media Services REST API [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK [StreamingEndpointCacheControl egenskaper](http://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Prioritetsordningen för cache-konfiguration
1. Azure Media Services konfigurerats cache värdet åsidosätter standardvärdet.
2. Om det finns ingen manuell konfiguration, används standardvärden.
3. Som standard 2 sekunder cache huvuden som gäller för live strömmande manifest(playlist) oavsett Azure Media eller Azure Storage-konfiguration och åsidosätts av det här värdet är inte tillgänglig.

