---
title: Hantera Azure CDN-cachelagringsprincipen i Azure Media Services | Microsoft-dokument
description: I den här artikeln beskrivs hur du hanterar Azure CDN-cachelagringsprincip i Azure Media Services.
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
ms.openlocfilehash: dc0482fbcbb1c9d1618ec18e1f48b03f686a6573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74892591"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Hantera Azure CDN-cachelagringsprincipen i Azure Media Services
Azure Media Services tillhandahåller HTTP-baserad adaptiv direktuppspelning och progressiv nedladdning. HTTP-baserad direktuppspelning är mycket skalbar med fördelarna med cachelagring i proxy- och CDN-lager samt cachelagring på klientsidan. Slutpunkter för direktuppspelning ger allmänna strömningsfunktioner och även konfiguration för HTTP-cachehuvuden. Slutpunkter för direktuppspelning anger HTTP-cachekontroll: maxålder och förfallna rubriker. Du kan få mer information om HTTP-cachehuvuden från [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Standard-cachelagringshuvuden
Som standard använder direktuppspelningsslutpunkter 3 dagars cachehuvuden för strömmande data på begäran (faktiska mediefragment/segment) och manifest(spellista). För direktuppspelning använder slutpunkter för direktuppspelning 3 dagars cachehuvuden för data (faktiska mediefragment/segment) och 2 sekunders cachehuvud för manifest(spellista). När live-programmet övergår till on-demand (live-arkiv) gäller sedan cachehuvuden för direktuppspelning på begäran.

## <a name="azure-cdn-integration"></a>Azure CDN-integrering
Azure Media Services tillhandahåller [integrerad CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) för direktuppspelningsslutpunkter. Cache-kontrollhuvuden gäller på samma sätt som slutpunkter för direktuppspelning på CDN-aktiverade slutpunkter för direktuppspelning. Azure CDN använder strömmande slutpunktskonfigurerade cachevärden för att definiera livslängden för de internt cachelagrade objekten och använder även det här värdet för att ange leveranscachens huvuden. När du använder CDN-aktiverade slutpunkter för direktuppspelning rekommenderas inte att ange små cachevärden. Om du ställer in små värden minskar prestanda och minskar nyttan med CDN. Det är inte tillåtet att ange cachehuvuden som är mindre än 600 sekunder för CDN-aktiverade slutpunkter för direktuppspelning.

> [!IMPORTANT]
>Azure Media Services har fullständig integrering med Azure CDN. Med ett enda klick kan du integrera alla tillgängliga Azure CDN-leverantörer till din slutpunkt för direktuppspelning, inklusive standard- och premiumprodukter. Mer information finns i det här [meddelandet](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Dataavgifter från slutpunkten för direktuppspelning till CDN inaktiveras bara om CDN är aktiverat via slutpunkts-API:er för direktuppspelning eller använda Azure-portalens slutpunktsavsnitt för direktuppspelning. Manuell integrering eller direkt skapa en CDN-slutpunkt med CDN API:er eller portalavsnittet inaktiverar inte dataavgifterna.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurera cachehuvuden med Azure Media Services
Du kan använda Azure Portal- eller Azure Media Services API:er för att konfigurera cachehuvudvärden.

1. Information om hur du konfigurerar cachehuvuden med Azure-portalen finns i avsnittet [Hantera slutpunkter](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) för direktuppspelning Konfigurera slutpunkten för direktuppspelning.
2. REST-API för Azure Media Services, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [StreamingEndpointCacheControl-egenskaper](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Prioritet för cachekonfiguration
1. Azure Media Services-konfigurerat cachevärde åsidosätter standardvärdet.
2. Om det inte finns någon manuell konfiguration gäller standardvärdena.
3. Som standard gäller cachehuvuden för 2 sekunder för livestreaming manifest(spellista) oavsett Azure Media- eller Azure Storage-konfiguration och åsidosättande av det här värdet är inte tillgängligt.

