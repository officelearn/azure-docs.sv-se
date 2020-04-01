---
title: Leverera innehåll till kunder | Microsoft-dokument
description: Det här avsnittet innehåller en översikt över vad som ingår i att leverera ditt innehåll med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c8d32a6434db0fad18b9fe7c2d6e2117795eb651
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476734"
---
# <a name="deliver-content-to-customers"></a>Leverera innehåll till kunder
När du levererar ditt strömmande innehåll eller video-on-demand-innehåll till kunder är ditt mål att leverera högkvalitativ video till olika enheter under olika nätverksförhållanden.

För att uppnå detta mål kan du:

* Koda din ström till en videoström med flera bithastigheter (adaptiv bithastighet). Detta kommer att ta hand om kvalitet och nätverksförhållanden.
* Använd [dynamisk paketering](media-services-dynamic-packaging-overview.md) i Microsoft Azure Media Services för att dynamiskt paketera om flödet till olika protokoll. Detta tar hand om streaming på olika enheter. Media Services stöder leverans av följande adaptiva datastreamingteknik: <br/>
    * **HTTP Live Streaming** (HLS) - lägg till sökvägen "(format=m3u8-aapl)" till delen "/Manifest" av webbadressen för att tala om för den strömmande ursprungsservern att returnera HLS-innehåll för konsumtion på **Apple iOS-inbyggda** enheter (för mer information, se [positionerare](#locators) och [webbadresser](#URLs))
    * **MPEG-DASH** - lägg till sökvägen "(format=mpd-time-csf)" till delen "/Manifest" i url:en för att tala om för servern för strömmande ursprung att returnera tillbaka MPEG-DASH (för mer information, se [positionerare](#locators) och [webbadresser](#URLs)),
    * **Smidig streaming**.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

Den här artikeln innehåller en översikt över viktiga innehållsleveransbegrepp.

Mer om du vill kontrollera kända problem finns i [Kända problem](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamisk paketering
Med den dynamiska förpackning som Media Services tillhandahåller kan du leverera ditt adaptiva bithastighets-MP4- eller Smooth Streaming-kodat innehåll i direktuppspelningsformat som stöds av Media Services (MPEG-DASH, HLS, Smooth Streaming) utan att behöva paketera om till dessa direktuppspelningsformat. Vi rekommenderar att du levererar ditt innehåll med dynamiska förpackningar.

För att dra nytta av dynamiska förpackningar måste du koda din mezzanine (källa) fil i en uppsättning adaptiv-bitrate MP4-filer eller adaptiv bitrate Smooth Streaming filer.

Med dynamisk förpackning lagrar och betalar du för filerna i ett enda lagringsformat. Media Services kommer att bygga och tjäna rätt svar baserat på dina förfrågningar.

Dynamisk förpackning är tillgänglig för standard- och premium-slutpunkter för direktuppspelning. 

Mer information finns i [Dynamisk förpackning](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filter och dynamiska manifest
Du kan definiera filter för dina tillgångar med Media Services. Dessa filter är regler på serversidan som hjälper dina kunder att göra saker som att spela upp ett visst avsnitt i en video eller ange en delmängd av ljud- och videoåtergivningar som kundens enhet kan hantera (i stället för alla återgivningar som är associerade med tillgången). Den här filtrningen uppnås genom dynamiska manifest som *skapas* när kunden begär att strömma en video baserat på ett eller flera angivna filter.

Mer information finns i [Filter och dynamiska manifest](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a><a id="locators"/>Positionerare
Om du vill ge användaren en WEBBADRESS som kan användas för att strömma eller ladda ned ditt innehåll måste du först publicera din tillgång genom att skapa en positionerare. En positionerare tillhandahåller en startpunkt för att komma åt filerna i en tillgång. Media Services stöder två typer av lokaliserare:

* OnDemandOrigin locators. Dessa används för att strömma media (till exempel MPEG-DASH, HLS eller Smooth Streaming) eller gradvis hämta filer.
* SAS-URL-positionerare (Shared Access Signature). Dessa används för att hämta mediefiler till den lokala datorn.

En *åtkomstprincip* används för att definiera de behörigheter (till exempel läsa, skriva och lista) och varaktighet för vilken en klient har åtkomst för en viss tillgång. Observera att listbehörigheten (AccessPermissions.List) inte bör användas för att skapa en OnDemandOrigin-positionerare.

Positionerare har utgångsdatum. Azure-portalen anger ett utgångsdatum 100 år i framtiden för positionerare.

> [!NOTE]
> Om du använde Azure-portalen för att skapa positionerare före mars 2015, var dessa positionerare inställda på att upphöra att gälla efter två år.
> 
> 

Du uppdaterar ett utgångsdatum för en lokaliserare med [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator)- eller [.NET](https://go.microsoft.com/fwlink/?LinkID=533259)-API:er. Observera att URL:en ändras när du uppdaterar en SAS-lokaliserare.

Positionerare är inte utformade för att hantera åtkomstkontroll per användare. Du kan ge enskilda användare olika åtkomsträttigheter genom att använda DRM-lösningar (Digital Rights Management). Mer information finns i [Skydda media](https://msdn.microsoft.com/library/azure/dn282272.aspx).

När du skapar en positionerare kan det finnas en fördröjning på 30 sekunder på grund av nödvändiga lagrings- och spridningsprocesser i Azure Storage.

## <a name="adaptive-streaming"></a>Adaptiv strömning
Adaptiv bithastighetsteknik gör det möjligt för videospelarprogram att bestämma nätverksförhållandena och välja mellan flera bithastigheter. När nätverkskommunikationen försämras kan klienten välja en lägre bithastighet så att uppspelningen kan fortsätta med lägre videokvalitet. När nätverksförhållandena förbättras kan klienten växla till en högre bithastighet med förbättrad videokvalitet. Azure Media Services stöder följande adaptiva bithastighetstekniker: HTTP Live Streaming (HLS), Smooth Streaming och MPEG-DASH.

Om du vill ge användarna strömmande webbadresser måste du först skapa en OnDemandOrigin-positionerare. När du skapar positioneraren får du bassökvägen till den tillgång som innehåller det innehåll som du vill strömma. Men för att kunna strömma det här innehållet måste du ändra den här sökvägen ytterligare. Om du vill skapa en fullständig URL till manifestfilen för direktuppspelning måste du sammanfoga locatorns sökvägsvärde och filnamnet manifest (filename.ism). Lägg sedan till **/Manifest** och ett lämpligt format (om det behövs) till positionerarsökvägen.

> [!NOTE]
> Du kan också strömma ditt innehåll via en TLS-anslutning. Det gör du genom att se till att dina strömmande webbadresser börjar med HTTPS. Observera att AMS för närvarande inte stöder TLS med anpassade domäner.  
> 

Du kan bara strömma över TLS om slutpunkten för direktuppspelning som du levererar ditt innehåll från skapades från efter den 10 september 2014. Om dina strömmande webbadresser baseras på slutpunkter för direktuppspelning som skapats efter den 10 september 2014 innehåller webbadressen "streaming.mediaservices.windows.net". Strömmande webbadresser som innehåller "origin.mediaservices.windows.net" (det gamla formatet) stöder inte TLS. Om webbadressen är i det gamla formatet och du vill kunna strömma över TLS skapar du en ny slutpunkt för direktuppspelning. Använd webbadresser baserat på den nya slutpunkten för direktuppspelning för att strömma ditt innehåll via TLS.

## <a name="streaming-url-formats"></a><a id="URLs"/>Strömmande URL-format

### <a name="mpeg-dash-format"></a>MPEG-DASH-format
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4-format
{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/{lokalisator-ID}/{filnamn}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3-format
{strömmande slutpunktnamn-mediatjänster kontonamn}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming (HLS) format med ljud-bara filter
Som standard ingår ljudspår i HLS-manifestet. Detta krävs för Apple Store-certifiering för mobilnät. I det här fallet, om en klient inte har tillräcklig bandbredd eller är ansluten via en 2G-anslutning, växlar uppspelningen till endast ljud. Detta hjälper till att hålla innehåll streaming utan buffring, men det finns ingen video. I vissa fall kan spelarbuffering föredras framför endast ljud. Om du vill ta bort det ljudspår du använder lägger du till **ljud-only=false** i webbadressen.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Mer information finns i [stöd för dynamisk manifestsammansättning och ytterligare funktioner för HLS-utdata](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Jämnt direktuppspelningsformat
{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

Exempel:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Smooth Streaming 2.0 manifest (äldre manifest)
Som standard innehåller smooth streaming manifestformatet upprepa taggen (r-tag). Vissa spelare stöder dock inte r-taggen. Klienter med dessa spelare kan använda ett format som inaktiverar r-taggen:

{strömmande slutpunktnamn-mediatjänster kontonamn}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Progressiv nedladdning
Med progressiv nedladdning kan du börja spela upp media innan hela filen har hämtats. Du kan inte successivt ladda ner .ism* (ismv, isma, ismt eller ismc) filer.

Om du vill hämta innehåll successivt använder du typen OnDemandOrigin.To progressively download content, use the OnDemandOrigin type of locator. I följande exempel visas url:en som baseras på typen OnDemandOrigin för positionerare:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Du måste dekryptera alla lagringskrypterade resurser som du vill strömma från ursprungstjänsten för progressiv hämtning.

## <a name="download"></a>Ladda ned
Om du vill hämta innehållet till en klientenhet måste du skapa en SAS-positionerare. SAS-positioneraren ger dig åtkomst till Azure Storage-behållaren där filen finns. Om du vill skapa hämtnings-URL:en måste du bädda in filnamnet mellan värden och SAS-signaturen.

I följande exempel visas webbadressen som baseras på SAS-positioneraren:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Följande gäller:

* Du måste dekryptera alla lagringskrypterade resurser som du vill strömma från ursprungstjänsten för progressiv hämtning.
* En nedladdning som inte har slutförts inom 12 timmar kommer att misslyckas.

## <a name="streaming-endpoints"></a>Slutpunkter för direktuppspelning

En slutpunkt för direktuppspelning representerar en strömningstjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett CDN-nätverk (Content Delivery Network) för vidare distribution. Den utgående strömmen från en slutpunktstjänst för direktuppspelning kan vara en livestream eller en video-on-demand-tillgång i ditt Media Services-konto. Det finns två typer av slutpunkter för direktuppspelning, **standard** och **premium**. Mer information finns i [Översikt över strömningsslutpunkter](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

## <a name="known-issues"></a>Kända problem
### <a name="changes-to-smooth-streaming-manifest-version"></a>Ändringar i manifestversionen för Smooth Streaming
Före tjänstutgåvan från juli 2016 – när tillgångar som produceras av Media Encoder Standard, Media Encoder Premium Workflow eller den tidigare Azure Media Encoder strömmades med hjälp av dynamisk förpackning – det smooth streaming-manifest som returneras överensstämmer med version 2.0. I version 2.0 använder fragmentets varaktigheter inte de så kallade upprepningstaggarna (r). Ett exempel:


    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

I tjänstutgåvan från juli 2016 överensstämmer det genererade Smooth Streaming-manifestet med version 2.2, med fragmentvaraktigheter med hjälp av upprepade taggar. Ett exempel:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Vissa av de äldre Smooth Streaming-klienterna kanske inte stöder upprepningstaggarna och kommer inte att läsa in manifestet. För att minska problemet kan du använda den äldre manifestformatsparametern **(format=fmp4-v20)** eller uppdatera klienten till den senaste versionen, som stöder upprepade taggar. Mer information finns i [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Uppdatera Media Services-positionerare efter rullande lagringsnycklar](media-services-roll-storage-access-keys.md)

