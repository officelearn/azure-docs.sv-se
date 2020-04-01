---
title: Publicera Azure Media Services-innehåll med .NET | Microsoft-dokument
description: Lär dig hur du skapar en positionerare som används för att skapa en url för direktuppspelning. Kodexempel skrivs i C# och använder Media Services SDK för .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 615a6afb0f7a3e133603db10e7c79add3322070c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476704"
---
# <a name="publish-media-services-content-using-net"></a>Publicera Media Services-innehåll med .NET  
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portalen](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Översikt
Du kan strömma en adaptiv bithastighet MP4-uppsättning genom att skapa en OnDemand-streaming locator och skapa en strömmande URL. Kodningen [av ett tillgångsavsnitt](media-services-encode-asset.md) visar hur du kodar till en adaptiv bithastighet MP4-uppsättning. 

> [!NOTE]
> Om innehållet är krypterat konfigurerar du principen för tillgångsleverans (enligt beskrivningen i [det här](media-services-dotnet-configure-asset-delivery-policy.md) avsnittet) innan du skapar en positionerare. 
> 
> 

Du kan också använda en OnDemand-direktuppspelningspositionerare för att skapa webbadresser som pekar på MP4-filer som kan hämtas successivt.  

Det här avsnittet visar hur du skapar en OnDemand-streamingpositionerare för att publicera din tillgång och skapa en smooth-, MPEG DASH- och HLS-strömningsadresser. Det visar också heta att bygga progressiva nedladdning webbadresser. 

## <a name="create-an-ondemand-streaming-locator"></a>Skapa en OnDemand-streamingpositionerare
Om du vill skapa ondemand-söksökaren och hämta webbadresser måste du göra följande:

1. Om innehållet är krypterat definierar du en åtkomstprincip.
2. Skapa en OnDemand-streamingpositionerare.
3. Om du planerar att strömma hämtar du manifestfilen för direktuppspelning (.ism) i tillgången. 
   
   Om du planerar att hämta successivt får du namnen på MP4-filer i tillgången.  
4. Skapa webbadresser till manifestfilen eller MP4-filerna. 


>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter. Principer för positionerare som är avsedda att finnas kvar under lång tid (principer som inte överförs). Mer information finns i [detta](media-services-dotnet-manage-entities.md#limit-access-policies) avsnitt.

### <a name="use-media-services-net-sdk"></a>Använda Media Services .NET SDK
Url:er för att skapa direktuppspelning 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

Utgångarna:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> Du kan också strömma ditt innehåll via en TLS-anslutning. För att göra den här metoden, se till att dina strömmande webbadresser börjar med HTTPS. För närvarande stöder AMS inte TLS med anpassade domäner.
> 
> 

Skapa progressiva nedladdningsadresser 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
Utgångarna:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Använda Media Services .NET SDK-tillägg
Följande kod anropar .NET SDK-tilläggsmetoder som skapar en positionerare och genererar url:er för jämn strömning, HLS och MPEG-DASH för adaptiv direktuppspelning.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
* [Ladda ner tillgångar](media-services-deliver-asset-download.md)
* [Konfigurera princip för tillgångsleverans](media-services-dotnet-configure-asset-delivery-policy.md)

