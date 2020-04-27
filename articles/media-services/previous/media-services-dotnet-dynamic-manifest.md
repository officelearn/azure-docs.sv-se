---
title: Skapa filter med Azure Media Services .NET SDK
description: I det här avsnittet beskrivs hur du skapar filter så att klienten kan använda dem för att strömma vissa delar av en ström. Media Services skapar dynamiska manifest för att uppnå denna selektiva strömning.
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
ms.author: juliako
ms.reviewer: cenkdin
ms.openlocfilehash: c60b223f91a151bf63cabc5e95816f2545022503
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "69016596"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Skapa filter med Media Services .NET SDK 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Från och med 2,17-versionen kan du med Media Services definiera filter för dina till gångar. Dessa filter är regler på Server sidan som gör det möjligt för kunderna att välja att göra saker som: endast uppspelning av en del av en video (i stället för att spela upp hela videon) eller ange endast en delmängd av ljud-och video åter givningar som kundens enhet kan hantera (i stället för alla renderingar som är associerade med till gången). Den här filtreringen av dina till gångar uppnås genom **dynamiskt manifest**s som skapas på kundens begäran att strömma en video som baseras på angivna filter.

Mer detaljerad information om filter och dynamiskt manifest finns i [Översikt över dynamiska manifest](media-services-dynamic-manifest-overview.md).

Den här artikeln visar hur du använder Media Services .NET SDK för att skapa, uppdatera och ta bort filter. 

Obs! Om du uppdaterar ett filter kan det ta upp till två minuter för strömnings slut punkten att uppdatera reglerna. Om innehållet har betjänats med det här filtret (och cachelagrats i proxyservrar och CDN-cacheminnen), kan uppdatering av det här filtret leda till Player-problem. Rensa alltid cacheminnet efter uppdatering av filtret. Om det här alternativet inte är möjligt bör du överväga att använda ett annat filter. 

## <a name="types-used-to-create-filters"></a>Typer som används för att skapa filter
Följande typer används när du skapar filter: 

* **IStreamingFilter**.  Den här typen baseras på följande REST API [filter](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Den här typen baseras på följande REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Den här typen baseras på följande REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** och **IFilterTrackPropertyCondition**. De här typerna baseras på följande REST-API: er [FilterTrackSelect och FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Skapa/uppdatera/läsa/ta bort globala filter
Följande kod visar hur du använder .NET för att skapa, uppdatera, läsa och ta bort till gångs filter.

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

## <a name="createupdatereaddelete-asset-filters"></a>Skapa/uppdatera/läsa/ta bort till gångs filter
Följande kod visar hur du använder .NET för att skapa, uppdatera, läsa och ta bort till gångs filter.

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


## <a name="build-streaming-urls-that-use-filters"></a>Bygg direkt uppspelnings-URL: er som använder filter
Information om hur du publicerar och levererar till gångar finns i [leverera innehåll till kund översikt](media-services-deliver-content-overview.md).

I följande exempel visas hur du lägger till filter till dina strömmande URL: er.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) v4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) v3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över dynamiska manifest](media-services-dynamic-manifest-overview.md)

