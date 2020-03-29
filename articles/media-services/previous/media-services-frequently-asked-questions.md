---
title: Vanliga frågor och svar i Azure Media Services
description: Den här artikeln innehåller svar på vanliga frågor om Azure Media Services.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: bdb5a43ad6669bfcd6e93ef4e3bf1646314e4606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705878"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Media Services v2 vanliga frågor och svar

Den här artikeln tar upp vanliga frågor som tagits upp av användarcommunityn för Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Allmänna vanliga frågor om AMS

F: Hur streamar du till Apple iOS-enheter?

S: lägg till sökvägen "(format=m3u8-aapl)" i delen "/Manifest" i webbadressen för att tala om för servern för strömmande ursprung att returnera HLS-innehåll för konsumtion på Apple iOS-inbyggda enheter (mer information finns [i leverera innehåll](media-services-deliver-content-overview.md))

F: Hur skalar du indexering?

S: De reserverade enheterna är desamma för kodnings- och indexeringsuppgifter. Följ instruktionerna om [Hur du skalar kodning reserverade enheter](media-services-scale-media-processing-overview.md). **Observera** att indexerarens prestanda inte påverkas av reserverad enhetstyp.

F: Jag laddade upp, kodade och publicerade en video. Vad skulle vara anledningen till att videon inte spelas upp när jag försöker streama den?

S: En av de vanligaste orsakerna är att du inte har den slutpunkt för direktuppspelning som du försöker spela upp i läget **Kör.**  

F: Kan jag skriva på en livestream?

S: Sammansättning i livestreamar erbjuds för närvarande inte i Azure Media Services, så du måste förkomla på datorn.

F: Kan jag använda Azure CDN med livestreaming?

S: Media Services stöder integrering med Azure CDN (mer information finns [i Så här hanterar du slutpunkter för direktuppspelning i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md)).  Du kan använda livestreaming med CDN. Azure Media Services tillhandahåller Smooth Streaming-, HLS- och MPEG-DASH-utdata. Alla dessa format använder HTTP för att överföra data och få fördelar med HTTP-cachelagring. I live streaming faktiska video / ljud data delas till fragment och denna enskilda fragment få cachelagrade i CDN. Endast data behöver uppdateras är manifestdata. CDN uppdaterar regelbundet manifestdata.

F: Har Azure Media-tjänster stöd för lagring av avbildningar?

S: Om du bara vill lagra JPEG- eller PNG-avbildningar bör du behålla dem i Azure Blob Storage. Det finns ingen fördel med att placera dem i ditt Media Services-konto om du inte vill att de ska vara kopplade till dina video- eller ljudtillgångar. Eller om du kanske har ett behov av att använda bilderna som överlägg i videokodaren. Media Encoder Standard stöder överlagring av bilder ovanpå videor, och det är vad den listar JPEG och PNG som stöds indataformat. Mer information finns i [Skapa överlägg](media-services-advanced-encoding-with-mes.md#overlay).

F: Hur kopierar jag tillgångar från ett Media Services-konto till ett annat?

S: Om du vill kopiera resurser från ett Media Services-konto till ett annat med .NET använder du [IAsset.Copy-tilläggsmetoden](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) som är tillgänglig i Azure [Media Services .NET SDK Extensions-databasen.](https://github.com/Azure/azure-sdk-for-media-services-extensions/) Mer information finns i [den här](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) forumtråden.

F: Vilka tecken stöds för att namnge filer när du arbetar med AMS?

S: Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL:er för direktuppspelningsinnehållet (till exempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Därför är procentkodning inte tillåtet. Värdet för egenskapen **Name** kan inte ha något av följande [procent-kodningsreservat tecken:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'();:@&=+$,/?%#[]". Dessutom kan det bara finnas en "." för filnamnstillägget.

F: Hur ansluter du med REST?

S: Information om hur du ansluter till AMS-API:et finns i [Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

F: Hur roterar jag en video under kodningen?

S: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) stöder rotation med vinklar på 90/180/270. Standardbeteendet är "Auto", där den försöker identifiera rotationsmetadata i den inkommande MP4/MOV-filen och kompensera för den. Inkludera följande **källelement** i en av de json-förinställningar som definieras [här:](media-services-mes-presets-overview.md)

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
