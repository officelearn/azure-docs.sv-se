---
title: Skapa filter med Azure Media Services .NET SDK
description: Det här avsnittet beskriver hur du skapar filter så att klienten kan använda dem till stream vissa delar av en dataström. Media Services skapar dynamiska manifest för att uppnå den här selektiva strömning.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;cenkdin
ms.openlocfilehash: 05b899658b5c58e15b2f30ab759eb49319979fee
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313812"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Skapa filter med Media Services .NET SDK 
> [!div class="op_single_selector"]
> * [NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Från och med 2.17, kan Media Services du definiera filter för dina tillgångar. Dessa filter är serversidan regler som gör att kunderna kan välja att till exempel: uppspelning endast en del av en video (i stället för hela videon), eller ange endast en delmängd av ljud och video återgivningar som din kunds enheten kan hantera (i stället för alla återgivningar som är associerade med tillgången). Den här filtrering av dina tillgångar uppnås via **dynamiska Manifest**s som skapas på din kunds begäran för direktuppspelning av video baserat på angivna filter.

Mer detaljerad information om filter och dynamiska Manifest, finns i [dynamiska manifest översikt](media-services-dynamic-manifest-overview.md).

Den här artikeln visar hur du använder Media Services .NET SDK för att skapa, uppdatera och ta bort filter. 

Observera att om du uppdaterar ett filter, det kan ta upp till två minuter innan strömningsslutpunkt om du vill uppdatera reglerna. Om innehållet har behandlats med hjälp av det här filtret och cachelagras i proxyservrar och CDN cacheminnen, kan uppdaterar det här filtret resultera i player-fel. Alltid Rensa cachen när du har uppdaterat filtret. Överväg att använda ett annat filter om det här alternativet inte är möjligt. 

## <a name="types-used-to-create-filters"></a>Typer som används för att skapa filter
Följande typer används när du skapar filter: 

* **IStreamingFilter**.  Den här typen är baserad på följande REST API [Filter](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Den här typen är baserad på följande REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Den här typen är baserad på följande REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** och **IFilterTrackPropertyCondition**. Dessa typer är baserade på följande REST API: er [FilterTrackSelect och FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Skapa/uppdatera/läsa/ta bort globala filter
Följande kod visar hur du använder .NET för att skapa, uppdatera, läsa och ta bort tillgången filter.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>Skapa/uppdatera/läsa/ta bort tillgången filter
Följande kod visar hur du använder .NET för att skapa, uppdatera, läsa och ta bort tillgången filter.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>Skapa strömmande URL: er som använder filter
Information om hur du publicerar och leverera dina tillgångar, finns i [leverera innehåll till kunder översikt](media-services-deliver-content-overview.md).

I följande exempel visas hur du lägger till filter till din strömmande URL: er.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över dynamiska manifest](media-services-dynamic-manifest-overview.md)

