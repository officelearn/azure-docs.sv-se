---
title: Hantera slutpunkter för direktuppspelning med Azure Media Services v3
description: Den här artikeln visar hur du hanterar slutpunkter för direktuppspelning med Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461052"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Hantera slutpunkter för direktuppspelning med Media Services v3

När ditt Media Services-konto skapas läggs en **default** [standardslutpunkt](streaming-endpoint-concept.md) för direktuppspelning till ditt konto i tillståndet **Stoppad.** Om du vill börja strömma ditt innehåll och dra nytta av [dynamisk paketering](dynamic-packaging-overview.md) och [dynamisk kryptering](content-protection-overview.md)måste slutpunkten för direktuppspelning som du vill strömma innehåll från vara i **körläge.**

Den här artikeln visar hur du kör [startkommandot](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) på din slutpunkt för direktuppspelning med hjälp av olika tekniker. 
 
> [!NOTE]
> Du faktureras bara när slutpunkten för direktuppspelning körs.
    
## <a name="prerequisites"></a>Krav

Recension: 

* [Koncept för Media Services](concepts-overview.md)
* [Streaming Endpoint-koncept](streaming-endpoint-concept.md)
* [Dynamisk paketering](dynamic-packaging-overview.md)

## <a name="use-rest"></a>Använda REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Mer information finns i: 

* [Starta en StreamingEndpoint-referensdokumentation.](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* Att starta en slutpunkt för direktuppspelning är en asynkron åtgärd. 

    Information om hur du övervakar tidskrävande åtgärder finns i [Tidskrävande åtgärder](media-services-apis-overview.md).
* Den här [Postman-samlingen](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) innehåller exempel på flera REST-åtgärder, inklusive om hur du startar en slutpunkt för direktuppspelning.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen 
 
1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Gå till ditt Azure Media Services-konto.
1. Välj **Strömmande slutpunkter**i den vänstra rutan .
1. Markera den slutpunkt för direktuppspelning som du vill starta och välj sedan **Start**.

## <a name="use-the-azure-cli"></a>Använda Azure CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Mer information finns i [start av az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>Använda SDK:er

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Se hela [Java-kodexemplet](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Se hela [.NET-kodexemplet](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Nästa steg

* [Media Services v3 OpenAPI Specifikation (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Åtgärder för direktuppspelning av slutpunkter](https://docs.microsoft.com/rest/api/media/streamingendpoints)
