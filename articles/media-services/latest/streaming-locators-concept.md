---
title: Strömmande positionerare i Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad som finns i strömmande positionerare och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 41b2d0ad1e072fb2bf5860ae80f8f25f886b37f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582679"
---
# <a name="streaming-locators"></a>Positionerare för direktuppspelning

För att skapa videor i utdatatillgångar som klienter ska kunna spela upp måste du skapa en [positionerare för direktuppspelning](https://docs.microsoft.com/rest/api/media/streaminglocators) och sedan skapa direktuppspelnings-URL:er. För att skapa en webbadress måste du sammanfoga strömningsslutpunktens värdnamn och sökvägen för positioneraren för direktuppspelning. .NET-exempel finns i [Hämta en positionerare för direktuppspelning](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Processen att skapa en **positionerare för direktuppspelning** kallas för publicering. Som standard kan din **positionerare för direktuppspelning** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en **strömmande positionerare**måste du ange ett **till gångs** namn och ett namn för en **strömmande princip** . Mer information finns i följande avsnitt:

* [Tillgångar](assets-concept.md)
* [Principer för direktuppspelning](streaming-policy-concept.md)
* [Viktiga innehållsprinciper](content-key-policy-concept.md)

Du kan också ange start-och slut tid för din plats för strömning, som endast tillåter att användaren spelar upp innehållet mellan dessa tider (till exempel mellan 5/1/2019 och 5/5/2019).  

## <a name="considerations"></a>Överväganden

* Det går inte att uppdatera **strömmande positionerare** . 
* Egenskaperna för **strömmande positionerare** som är av typen datetime är alltid i UTC-format.
* Du bör utforma en begränsad uppsättning principer för ditt Media Service-konto och återanvända dem för dina strömmande positionerare när samma alternativ behövs. Mer information finns i [kvoter och begränsningar](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Skapa strömmande positionerare  

### <a name="not-encrypted"></a>Inte krypterad

Om du vill strömma filen i klartext (icke-krypterad) anger du den fördefinierade principen för att rensa direkt uppspelning: till Predefined_ClearStreamingOnly (i .NET kan du använda PredefinedStreamingPolicy. ClearStreamingOnly-uppräkningen).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Krypterade 

Om du behöver Kryptera ditt innehåll med CENC-kryptering ställer du in principen på "Predefined_MultiDrmCencStreaming". Widevine-krypteringen tillämpas på en STRECKs ström och PlayReady som är smidigare. Nyckeln skickas till en uppspelnings klient baserat på de konfigurerade DRM-licenserna.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Om du också vill kryptera din HLS-ström med CBCS (FairPlay) använder du Predefined_MultiDrmStreaming.

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associera filter med strömmande positionerare

Se [filter: associera med strömmande positionerare](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filter, ordning, lokaliserare för sidans strömmande enheter

Se [filtrering, sortering, sid indelning för Media Services entiteter](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Lista över strömmande positionerare efter till gångs namn

Använd följande åtgärder om du vill hämta strömmande positionerare baserade på det associerade till gångs namnet:

|Språk|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[AZ AMS Asset List-streaming-Locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Se även

* [Tillgångar](assets-concept.md)
* [Principer för direktuppspelning](streaming-policy-concept.md)
* [Viktiga innehållsprinciper](content-key-policy-concept.md)
* [Självstudie: Ladda upp, koda och strömma videor med .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Nästa steg

[Så här skapar du en strömmande lokaliserare och skapar URL: er](create-streaming-locator-build-url.md)
