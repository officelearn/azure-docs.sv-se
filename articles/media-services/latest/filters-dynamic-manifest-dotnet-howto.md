---
title: Skapa filter med Azure Media Services v3 .NET SDK
description: I det här avsnittet beskrivs hur du skapar filter så att klienten kan använda dem för att strömma specifika avsnitt i en ström. Media Services skapar dynamiska manifest för att uppnå denna selektiva strömning.
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
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: ef04b1b7b5030189482e89e26e4565397cbdd7c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779254"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Skapa filter med Media Services .NET SDK

När du levererar ditt innehåll till kunder (strömma livehändelser eller Video on Demand) kan din klient behöva mer flexibilitet än vad som beskrivs i standardtillgångens manifestfil. Med Azure Media Services kan du definiera kontofilter och tillgångsfilter för ditt innehåll. 

Detaljerad beskrivning av den här funktionen och scenarier där den används finns i [Dynamiska manifest](filters-dynamic-manifest-overview.md) och [filter](filters-concept.md).

Det här avsnittet visar hur du använder Media Services .NET SDK för att definiera ett filter för en video på begäran-tillgång och skapa [kontofilter](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) och [tillgångsfilter](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Se till att granska [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Krav 

- Granska [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppsnamnet och mediatjänstkontonamnet. 
- Få information som behövs för att [komma åt API:er](access-api-cli-how-to.md)
- Granska [Ladda upp, koda och strömma med Azure Media Services](stream-files-tutorial-with-api.md) för att se hur du börjar använda [.NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Definiera ett filter  

I .NET konfigurerar du spårval med [klasserna FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) och [FilterTrackPropertyCondition.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) 

Följande kod definierar ett filter som innehåller alla ljudspår som är EC-3 och alla videospår som har bithastighet i intervallet 0-1000000.

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

Följande kod visar hur du använder .NET för att skapa ett kontofilter som innehåller alla spårval [som definierats ovan](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Skapa tillgångsfilter

Följande kod visar hur du använder .NET för att skapa ett tillgångsfilter som innehåller alla spårval [som definierats ovan](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Associera filter med streamingpositionerare

Du kan ange en lista över tillgångs- eller kontofilter som gäller för din streamingpositionerare. Den [dynamiska paketeraren (Streaming Endpoint)](dynamic-packaging-overview.md) tillämpar den här listan med filter tillsammans med de som klienten anger i URL:en. Den här kombinationen genererar ett [dynamiskt manifest](filters-dynamic-manifest-overview.md), som baseras på filter i URL + filter som du anger på strömningspositioneraren. Vi rekommenderar att du använder den här funktionen om du vill använda filter men inte vill visa filternamnen i URL:en.

Följande C#-kod visar hur du skapar `StreamingLocator.Filters`en streamingpositionerare och anger . Det här är en `IList<string>` valfri egenskap som tar ett av filternamnen.

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
      
## <a name="stream-using-filters"></a>Strömma med hjälp av filter

När du har definierat filter kan klienterna använda dem i webbadressen för direktuppspelning. Filter kan användas för adaptiv bitrate streaming protokoll: Apple HTTP Live Streaming (HLS), MPEG-DASH och Smooth Streaming.

I följande tabell visas några exempel på webbadresser med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Nästa steg

[Strömma videor](stream-files-tutorial-with-api.md) 


