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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 51aa33e4ff387a1030dac42bce8d12cf72343b35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466739"
---
# <a name="streaming-locators"></a>Positionerare för direktuppspelning

För att skapa videor i utdatatillgångar som klienter ska kunna spela upp måste du skapa en [positionerare för direktuppspelning](https://docs.microsoft.com/rest/api/media/streaminglocators) och sedan skapa direktuppspelnings-URL:er. .NET-exempel finns i [Hämta en positionerare för direktuppspelning](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Processen att skapa en **positionerare för direktuppspelning** kallas för publicering. Som standard kan din **positionerare för direktuppspelning** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en **Strömningspositionerare**, måste du ange den [tillgången](https://docs.microsoft.com/rest/api/media/assets) namn och [Streaming princip](https://docs.microsoft.com/rest/api/media/streamingpolicies) namn. Du kan antingen använda en av principerna för direktuppspelning fördefinierade eller skapat en anpassad princip. De fördefinierade principerna som är tillgänglig för tillfället är: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' and 'Predefined_MultiDrmStreaming'. När du använder en anpassad bör strömning principen du utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ och protokoll krävs. 

Om du vill ange alternativ för kryptering på din stream skapar den [innehåll nyckel princip](https://docs.microsoft.com/rest/api/media/contentkeypolicies) som konfigurerar hur innehållsnyckeln levereras om du vill avsluta klienter via komponenten nyckel leverans av Media Services. Associera din Strömningspositionerare med den **innehåll nyckel princip** och innehållsnyckeln. Du kan låta Media Services för att skapa nyckeln. I följande .NET-exempel visas hur du konfigurerar AES-kryptering med en token begränsning i Media Services v3: [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). **Innehålls-principer för nycklar** är uppdateringsbar, kanske du vill uppdatera principen om du behöver göra en rotation av. Det kan ta upp till 15 minuter för leverans av nyckel-cacheminne för att uppdatera och hämta den uppdaterade policyn. Det rekommenderas att inte skapa en ny princip för Content-nyckel för varje Strömningspositionerare. Du bör försöka återanvända de befintliga principerna när samma alternativ behövs.

> [!IMPORTANT]
> * Egenskaper för **positionerare för direktuppspelning** som är av typen är alltid i UTC-format för datum/tid.
> * Du bör utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ behövs. 

## <a name="associate-filters-with-streaming-locators"></a>Associera filter med positionerare för direktuppspelning

Du kan ange en lista över [tillgång eller konto filter](filters-concept.md), vilket skulle gälla för dina [Strömningspositionerare](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). Den [dynamisk Paketeraren](dynamic-packaging-overview.md) gäller den här listan över filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar en [dyanamic manifest](filters-dynamic-manifest-overview.md), som grundar sig på filter i URL: en + filter som du anger på Strömningspositionerare. Vi rekommenderar att du använder den här funktionen om du vill använda filter men inte vill exponera filternamn i URL: en.

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrera, ordning, sidan Strömningspositionerare entiteter

Se [filtrering, sortering, växling av Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Ladda upp, koda och strömma videor med hjälp av .NET](stream-files-tutorial-with-api.md)
* [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
