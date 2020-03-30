---
title: Strömmande positionerare i Azure Media Services | Microsoft-dokument
description: Den här artikeln ger en förklaring av vad streamingpositionerare är och hur de används av Azure Media Services.
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
ms.openlocfilehash: 3a9568e1a0307cd1713c511ef42c065424306548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302890"
---
# <a name="streaming-locators"></a>Positionerare för direktuppspelning

För att skapa videor i utdatatillgångar som klienter ska kunna spela upp måste du skapa en [positionerare för direktuppspelning](https://docs.microsoft.com/rest/api/media/streaminglocators) och sedan skapa direktuppspelnings-URL:er. För att skapa en webbadress måste du sammanfoga strömningsslutpunktens värdnamn och sökvägen för positioneraren för direktuppspelning. .NET-exempel finns i [Hämta en positionerare för direktuppspelning](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Processen att skapa en **positionerare för direktuppspelning** kallas för publicering. Som standard kan din **positionerare för direktuppspelning** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en **direktuppspelningspositionerare**måste du ange ett **tillgångsnamn** och ett **direktuppspelningsprincipnamn.** Mer information finns i följande avsnitt:

* [Tillgångar](assets-concept.md)
* [Principer för direktuppspelning](streaming-policy-concept.md)
* [Viktiga innehållsprinciper](content-key-policy-concept.md)

Du kan också ange start- och sluttid på din streamingpositionerare, vilket bara låter användaren spela upp innehållet mellan dessa tider (till exempel mellan 2019-05-05 till 2019-05-5).  

## <a name="considerations"></a>Överväganden

* **Streaming Locators** är inte uppdateringsbara. 
* Egenskaper för **direktuppspelningspositionerare** som är av typen Datetime är alltid i UTC-format.
* Du bör utforma en begränsad uppsättning principer för ditt Media Service-konto och återanvända dem för dina streamingpositionerare när samma alternativ behövs. Mer information finns i [Kvoter och begränsningar](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Skapa streamingpositionerare  

### <a name="not-encrypted"></a>Inte krypterad

Om du vill strömma filen i-det-klar (icke-krypterad), ange den fördefinierade rensa streaming principen: att "Predefined_ClearStreamingOnly" (i .NET, kan du använda PredefinedStreamingPolicy.ClearStreamingOnly uppräkning).

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

Om du behöver kryptera ditt innehåll med CENC-krypteringen ställer du in policyn på "Predefined_MultiDrmCencStreaming". Widevine-krypteringen kommer att tillämpas på en DASH-ström och PlayReady till Smooth. Nyckeln levereras till en uppspelningsklient baserat på de konfigurerade DRM-licenserna.

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

Om du också vill kryptera din HLS-ström med CBCS (FairPlay) använder du "Predefined_MultiDrmStreaming".

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associera filter med streamingpositionerare

Se [Filter: associera med streamingpositionerare](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filter, ordning, sida Streaming Locator entiteter

Se [Filtrering, beställning, växling av Media Services-entiteter](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Lista streamingpositioner efter tillgångsnamn

Om du vill hämta direktuppsökare baserat på det associerade tillgångsnamnet använder du följande åtgärder:

|Språk|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams tillgång lista-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[TillgångStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listaStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Se även

* [Tillgångar](assets-concept.md)
* [Principer för direktuppspelning](streaming-policy-concept.md)
* [Viktiga innehållsprinciper](content-key-policy-concept.md)
* [Självstudiekurs: Ladda upp, koda och strömma videor med .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Nästa steg

[Så här skapar du en streaminglokaliserare och skapar webbadresser](create-streaming-locator-build-url.md)
