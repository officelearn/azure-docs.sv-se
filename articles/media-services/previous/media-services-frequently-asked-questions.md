---
title: Vanliga frågor och svar om Azure Media Services | Microsoft Docs
description: Vanliga frågor och svar (FAQ)
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5374f7f4-c189-43ef-8b7f-f2f4141e2748
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: juliako
ms.openlocfilehash: a47163d06e24814ca5724d1fabea84058f8764cf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788538"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Den här artikeln tar upp vanliga frågor och svar som skapats av användare som Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Allmänna AMS vanliga frågor och svar

F: hur du för att strömma till Apple iOS-enheter

A: Lägg till ”(format = m3u8 aapl)” sökväg till ”/ Manifest”-delen i Webbadressen ska berätta för strömmande ursprungsservern att returnera den bakre HLS innehåll för användning på Apple iOS inbyggda enheter (Mer information, se (leverera innehåll) [ Media-services-leverera-innehåll-overview.md]),

F: hur du skala indexering?

S: reserverade enheter är desamma för kodning och indexering uppgifter. Följ instruktionerna på [så skala Encoding-reserverade enheter](media-services-scale-media-processing-overview.md). **Obs** indexeraren prestanda inte påverkas av reserverade enhetstyp.

F: Jag har överförts, kodat och publicerade en video. Vad är orsaken videon inte upp när jag försöker att strömma den?

S: en av de vanligaste orsakerna är att du inte har den strömningsslutpunkt från vilken du vill spela upp i den **kör** tillstånd.  

F: kan jag göra sammansättning på en direktsänd dataström?

S: sammansättning på live dataströmmar finns för närvarande inte i Azure Media Services så måste du skapa före på datorn.

F: kan jag använda Azure CDN med Liveströmning?

S: Media Services stöder integration med Azure CDN (Mer information finns i [hur du hanterar Strömningsslutpunkter i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md)).  Du kan använda direktsänd strömning med CDN. Azure Media Services tillhandahåller Smooth Streaming, HLS och MPEG-DASH-utdata. Alla dessa format använder HTTP för att överföra data och få fördelarna med HTTP-cachelagring. I direktsänd strömning faktiska ljud och data delas till fragment och den här enskilda fragment hämta cachelagras i CDN. Endast data måste uppdateras är manifestet data. CDN uppdaterar regelbundet manifestet data.

F: kan Azure Media services stöder lagra avbildningar?

S: Om du bara vill lagra JPEG eller PNG-avbildningar, bör du behålla dem i Azure Blob Storage. Det finns ingen fördel med att placera dem i ditt Media Services-konto om du inte vill hålla dem som är associerade med din Video eller ljud tillgångar. Eller om du kanske behöver använda avbildningar som överlägg i video kodaren. Media Encoder Standard stöder överlägg bilder ovanpå videor och som är vad listas JPEG och PNG som stöds indata format. Mer information finns i [skapar överlägg](media-services-advanced-encoding-with-mes.md#overlay).

F: hur kan jag kopiera resurser från ett Media Services-konto till en annan.

S: att kopiera resurser från ett Media Services-konto till en annan med .NET kan använda [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) tilläggsmetoden som är tillgängliga i den [Azure Media Services .NET SDK-tilläggen](https://github.com/Azure/azure-sdk-for-media-services-extensions/) databasen. Mer information finns i [detta](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) forum tråd.

F: Vad är tecken som stöds för namngivning av filer när du arbetar med AMS?

S: Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL: er för strömning innehållet (till exempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Därför tillåts procent-encoding inte. Värdet för den **namn** egenskapen får inte ha något av följande [procent-encoding-reserverade tecken](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # % ”. Dessutom det kan bara finnas ett '.' för filnamnstillägget.

F: hur du ansluter med hjälp av REST?

S: information om hur du ansluter till AMS API finns [åtkomst till Azure Media Services-API med Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md). 

F: hur kan rotera en video under kodningen.

A: det [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) stöder rotation av vinklar 270-90/180. Standardinställningen är ”automatisk” där försöker identifiera rotation metadata i filen inkommande MP4/MOV och kompensera för den. Inkludera följande **källor** element till en json-förinställningar definierats [här](media-services-mes-presets-overview.md):

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
