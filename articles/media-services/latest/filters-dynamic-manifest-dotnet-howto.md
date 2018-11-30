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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 23e83b98288f9ac1fe23e01b9a91d81daa3b0f47
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632389"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Skapa filter med Media Services .NET SDK

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran) kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

Det här avsnittet visar hur du använder Media Services .NET SDK för att definiera ett filter för en Video på begäran tillgången och skapa [kontofilter](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) och [tillgången filter](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

## <a name="prerequisites"></a>Förutsättningar 

- Granska [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto. 
- Få information som behövs för att [åtkomst API: er](access-api-cli-how-to.md)
- Granska [överföra, koda och strömma med Azure Media Services](stream-files-tutorial-with-api.md) att se hur du [börja använda .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Definiera ett filter  

I .NET, konfigurerar du spåra val med [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) och [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) klasser. 

Följande kod definierar ett filter som innehåller alla ljudspår som är engelska med EG 3 och alla video spår har bithastighet i 0-1000000 intervall.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Language, "en-us", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
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

## <a name="next-steps"></a>Nästa steg

[Stream-videor](stream-files-tutorial-with-api.md) 


