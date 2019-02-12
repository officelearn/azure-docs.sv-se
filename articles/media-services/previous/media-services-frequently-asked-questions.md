---
title: Vanliga frågor och svar om Azure medietjänster | Microsoft Docs
description: Vanliga frågor och svar (FAQ)
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: d1a7ae1e66caaaf17e3c4a38b09eaa2d900604b3
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004493"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Den här artikeln tar upp vanliga frågor och svar har aktiverats av användargruppen för Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Allmänna AMS vanliga frågor och svar

F: Hur du för att strömma till Apple iOS-enheter?

S: Lägg till ”(format = m3u8-aapl)” sökväg till ”/ Manifest”-delen i Webbadressen ska berätta för strömmande ursprungsservern att returnera tillbaka HLS-innehåll för användning på Apple iOS ursprungliga enheter (Mer information finns i [leverans av innehåll](media-services-deliver-content-overview.md)),

F: Hur du skala indexering?

S: Reserverade enheter är desamma för kodning och indexering uppgifter. Följ instruktionerna på [så skala Encoding-reserverade enheter](media-services-scale-media-processing-overview.md). **Obs** indexeraren prestanda inte påverkas av typ av reserverad enhet.

F: Jag överfört, kodat och publicerat en video. Vad skulle vara orsaken till videon agerar inte när jag försöker att strömma det?

S: En av de vanligaste orsakerna är du inte har den strömningsslutpunkt från vilken du vill spela upp den **kör** tillstånd.  

F: Kan jag göra sammansättning i en direktsänd dataström?

S: Sammansättning på direktsända strömmar erbjuds för närvarande inte i Azure Media Services, så skulle du behöva före compose på datorn.

F: Kan jag använda Azure CDN med liveuppspelning?

S: Media Services stöder integration med Azure CDN (Mer information finns i [hur du hanterar Strömningsslutpunkter i ett Medietjänstkonto](media-services-portal-manage-streaming-endpoints.md)).  Du kan använda direktsänd strömning med CDN. Azure Media Services tillhandahåller Smooth Streaming, HLS och MPEG-DASH-utdata. Alla dessa format använder HTTP för att överföra data och få fördelarna med HTTP-cachelagring. I direktsänd strömning faktiska ljud och data delas till fragment och den här enskilda fragment hämta cachelagras i CDN. Databehov som ska uppdateras är manifestet data. CDN uppdaterar regelbundet manifest data.

F: Stöder lagra avbildningar i Azure Media services?

S: Om du bara vill lagra JPEG eller PNG-bilder, bör du behålla dem i Azure Blob Storage. Det finns några fördelar med att placera dem i ditt Media Services-konto om du inte vill ha dem som är associerade med din Video eller ljud tillgångar. Eller om du kanske behöver använda avbildningarna som överlägg i video encoder. Media Encoder Standard har stöd för överlägg avbildningar ovanpå videor, och det är vad visas en lista med JPEG- och PNG eftersom stöds ange format. Mer information finns i [skapar överlägg](media-services-advanced-encoding-with-mes.md#overlay).

F: Hur kopierar jag tillgångar från ett Media Services-konto till en annan?

S: Kopiera tillgångar från ett Media Services-konto till en annan med hjälp av .NET, använda [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) tilläggsmetod som är tillgängliga i den [Azure Media Services .NET SDK-tilläggen](https://github.com/Azure/azure-sdk-for-media-services-extensions/) lagringsplats. Mer information finns i [detta](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) forumtråden.

F: Vilka är tecken som stöds för namngivning av filer när du arbetar med AMS?

S: Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL: er för strömning innehållet (till exempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Därför stöds procent-kodning inte. Värdet för den **namn** egenskapen får inte innehålla något av följande [procent-encoding-reserverade tecken](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # % ”. Dessutom det får bara finnas ett ””. för filnamnstillägg.

F: Hur du ansluter med hjälp av REST?

S: Information om hur du ansluter till AMS API finns i [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

F: Hur kan jag för att rotera en video under kodningen?

S: Den [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) stöder rotation av vinklar för 90/180/270. Standardinställningen är ”automatisk”, där den försöker identifiera rotation metadata i den importerade filen för MP4/MOV och kompensera för den. Inkluderar följande **källor** element till en json-förinställningar definierats [här](media-services-mes-presets-overview.md):

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
