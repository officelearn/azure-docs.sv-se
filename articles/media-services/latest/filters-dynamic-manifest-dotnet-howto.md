---
title: Skapa filter med Azure Media Services .NET SDK
description: Det här avsnittet beskriver hur du skapar filter så att klienten kan använda dem till stream vissa delar av en dataström. Media Services skapar dynamiska manifest för att uppnå den här selektiva strömning.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 8c786f46308848c6b9182453510744942a8eb9e8
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472421"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Skapa filter med Media Services .NET SDK

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran) kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

Det här avsnittet visar hur du använder Media Services .NET SDK för att definiera ett filter för en Video på begäran tillgången och skapa [kontofilter](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) och [tillgången filter](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

## <a name="prerequisites"></a>Nödvändiga komponenter 

- Granska [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto. 
- Få information som behövs för att [åtkomst API: er](access-api-cli-how-to.md)
- Granska [överföra, koda och strömma med Azure Media Services](stream-files-tutorial-with-api.md) att se hur du [börja använda .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Definiera ett filter  

I .NET, konfigurerar du spåra val med [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) och [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) klasser. 

Följande kod definierar ett filter som innehåller alla ljudspår som är EG-3 och alla video spår har bithastighet i 0-1000000 intervall.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Skapa kontofilter

Följande kod visar hur du använder .NET för att skapa ett kontofilter som innehåller alla spåra val [definieras ovan](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Skapa filter för tillgången

Följande kod visar hur du använder .NET för att skapa ett filter för tillgången som innehåller alla spåra val [definieras ovan](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Associera filter med Strömningspositionerare

Du kan ange en lista över tillgång eller konto filter som skulle gälla för dina Strömningspositionerare. Den [dynamisk Paketeraren (slutpunkt för direktuppspelning)](dynamic-packaging-overview.md) gäller den här listan över filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar en [dynamiska manifest](filters-dynamic-manifest-overview.md), som grundar sig på filter i URL: en + filter som du anger på Strömningspositionerare. Vi rekommenderar att du använder den här funktionen om du vill använda filter men inte vill exponera filternamn i URL: en.

Följande C# kod visar hur du skapar en positionerare för direktuppspelning och ange `StreamingLocator.Filters`. Det här är en valfri egenskap som tar en `IList<string>` namn på filter.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Stream med hjälp av filter

När du har definierat filter kan klienterna använda dem i strömnings-URL. Filter kan tillämpas på protokoll för direktuppspelning med anpassningsbar bithastighet: Apple HTTP Live Streaming (HLS), MPEG-DASH och Smooth Streaming.

I följande tabell visas några exempel på URL: er med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Nästa steg

[Stream-videor](stream-files-tutorial-with-api.md) 


