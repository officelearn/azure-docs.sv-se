---
title: Strömning positionerare i Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad positionerare för direktuppspelning är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: be66dcf8115258b6f593ec913e75785a3f8dbe1f
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743488"
---
# <a name="streaming-locators"></a>Positionerare för direktuppspelning

Att göra videor i utdata tillgången som är tillgängliga för klienter för uppspelning, måste du skapa en [Strömningspositionerare](https://docs.microsoft.com/rest/api/media/streaminglocators) och sedan skapa strömmande URL: er. .NET-exempel finns i [få en Strömningspositionerare](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Processen att skapa en **positionerare för direktuppspelning** kallas för publicering. Som standard kan din **positionerare för direktuppspelning** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en **Strömningspositionerare**, måste du ange den [tillgången](https://docs.microsoft.com/rest/api/media/assets) namn och [Streaming princip](https://docs.microsoft.com/rest/api/media/streamingpolicies) namn. Du kan antingen använda en av principerna för direktuppspelning fördefinierade eller skapat en anpassad princip. De fördefinierade principerna som är tillgänglig för tillfället är: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' and 'Predefined_MultiDrmStreaming'. När du använder en anpassad bör strömning principen du utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ och protokoll krävs. 

Om du vill ange alternativ för kryptering på din stream skapar den [innehåll nyckel princip](https://docs.microsoft.com/rest/api/media/contentkeypolicies) som konfigurerar hur innehållsnyckeln levereras om du vill avsluta klienter via komponenten nyckel leverans av Media Services. Associera din Strömningspositionerare med den **innehåll nyckel princip** och innehållsnyckeln. Du kan låta Media Services för att skapa nyckeln. I följande .NET-exempel visas hur du konfigurerar AES-kryptering med en token begränsning i Media Services v3: [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). **Innehålls-principer för nycklar** är uppdateringsbar, kanske du vill uppdatera principen om du behöver göra en rotation av. Det kan ta upp till 15 minuter för leverans av nyckel-cacheminne för att uppdatera och hämta den uppdaterade policyn. Det rekommenderas att inte skapa en ny princip för Content-nyckel för varje Strömningspositionerare. Du bör försöka återanvända de befintliga principerna när samma alternativ behövs.

> [!IMPORTANT]
> * Egenskaper för **positionerare för direktuppspelning** som är av typen är alltid i UTC-format för datum/tid.
> * Du bör utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ behövs. 

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Se [filtrering, sortering, växling av Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Självstudier: Ladda upp, koda och strömma videor med hjälp av .NET](stream-files-tutorial-with-api.md)
* [Använda DRM dynamisk kryptering och licens video-on-demand](protect-with-drm.md)
