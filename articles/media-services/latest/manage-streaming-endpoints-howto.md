---
title: Hantera slut punkter för direkt uppspelning med Azure Media Services v3
description: Den här artikeln visar hur du hanterar strömnings slut punkter med Azure Media Services v3.
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
ms.openlocfilehash: 5dd3cc1efd25f7ec09f897c67bebebedb84d9570
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370553"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Hantera slut punkter för direkt uppspelning med Media Services v3

När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av **standardtyp** till i kontot med tillståndet **Stoppad**. Om du vill börja strömma ditt innehåll och dra nytta av [dynamisk paketering](dynamic-packaging-overview.md) och [dynamisk kryptering](content-protection-overview.md), måste den strömmande slut punkten från vilken du vill strömma innehåll vara i **Kör** tillstånd.

Den här artikeln visar hur du startar slut punkten för direkt uppspelning.
 
> [!NOTE]
> Du faktureras bara när slut punkten för direkt uppspelning är i körnings läge.
    
## <a name="prerequisites"></a>Förutsättningar

Beakta 

* [Media Services begrepp](concepts-overview.md)
* [Koncept för strömnings slut punkt](streaming-endpoint-concept.md)
* [Dynamisk paketering](dynamic-packaging-overview.md)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Navigera till ditt Azure Media Services-konto.
1. Välj **strömmande slut punkter**till vänster.
1. Välj den slut punkt för direkt uppspelning som du vill starta, klicka på **Start**.

## <a name="use-the-java-sdk"></a>Använda Java SDK

```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Se det fullständiga [Java Code-exemplet](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

## <a name="use-the-net-sdk"></a>Använda .NET SDK

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Se det fullständiga [.NET-kod exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

## <a name="use-cli"></a>Använda CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Mer information finns i [AZ AMS streaming-Endpoint start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-rest"></a>Använda REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Mer information finns i: 

* Referens dokumentationen för att [starta en StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) -referens.
* Att starta en slut punkt för direkt uppspelning är en asynkron åtgärd. 

    Mer information om hur du övervakar långvariga åtgärder finns i [tids krävande åtgärder](media-services-apis-overview.md)
* Den här [Postman-samlingen](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) innehåller exempel på flera rest-åtgärder, inklusive hur du startar en slut punkt för direkt uppspelning.

## <a name="next-steps"></a>Nästa steg

* [Media Services v3 OpenAPI-specifikation (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Slut punkts åtgärder för direkt uppspelning](https://docs.microsoft.com/rest/api/media/streamingendpoints)
