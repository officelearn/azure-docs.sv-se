---
title: Leverera innehåll till kunder | Microsoft Docs
description: Det här avsnittet ger en översikt över vad som är involverad i att leverera ditt innehåll med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: juliako
ms.openlocfilehash: 1d1506e26beec3cc48a904ddeb9bbb4e7656a08e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788895"
---
# <a name="deliver-content-to-customers"></a>Leverera innehåll till kunder
När du levererar vi din strömning eller video-on-demand-innehåll till kunder, är målet att leverera video med hög kvalitet till olika enheter under olika nätverksförhållanden.

För att åstadkomma detta kan du:

* Koda strömmen till en video-ström med flera bithastigheter (anpassningsbar bithastighet). Detta hand tar om kvalitet och nätverk.
* Använda Microsoft Azure Media Services [dynamisk paketering](media-services-dynamic-packaging-overview.md) till dynamiskt Ompaketera din ström till olika protokoll. Detta hand tar om strömning på olika enheter. Media Services stöder leverans av de följande strömning med anpassningsbar bithastighet tekniker: <br/>
    * **HTTP Live Streaming** (HLS) - Lägg till ”(format = m3u8 aapl)” sökväg till ”/ Manifest”-delen i Webbadressen ska berätta för strömmande ursprungsservern att returnera den bakre HLS innehåll för användning på **Apple iOS** inbyggda enheter (Mer information Se [lokaliserare](#locators) och [URL: er](#URLs)),
    * **MPEG-DASH** -Lägg till ”(format = mpd-tid-csf)” sökväg till ”/ Manifest”-delen i Webbadressen ska berätta för strömmande ursprungsservern att returnera tillbaka MPEG-DASH (Mer information finns i [lokaliserare](#locators) och [URL: er](#URLs)),
    * **Smooth Streaming**.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

Den här artikeln ger en översikt över viktiga innehållsleverans begrepp.

Kända problem finns i [kända problem](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamisk paketering
Med dynamisk paketering som Media Services tillhandahåller, du kan leverera ditt innehåll MP4 eller Smooth Streaming-kodade med anpassad bithastighet i strömningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) utan att behöva Ompaketera till dessa strömningsformat. Vi rekommenderar att leverera ditt innehåll med dynamisk paketering.

Om du vill dra nytta av dynamisk paketering, måste du koda din mezzaninfil (källa) till en uppsättning MP4-filer med anpassningsbar bithastighet eller Smooth Streaming-filer.

Med dynamisk paketering, lagra och betala för filerna i ett enda lagringsformat. Media Services skapar och ger lämplig respons baserat på dina önskemål.

Dynamisk paketering är tillgänglig för standard- och premium strömningsslutpunkter. 

Mer information finns i [dynamisk paketering](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filter och dynamiska manifest
Du kan definiera filter för dina tillgångar med Media Services. Dessa filter är serversidan regler som ger kunderna exempelvis spela upp ett visst avsnitt av en video eller ange en delmängd av ljud och video återgivningar som kundens enhet kan hantera (i stället för alla återgivningar som är associerade med tillgången). Den här filtreringen uppnås genom *dynamiska manifesten* som skapas när kunden begär för direktuppspelning av video baserat på en eller flera specifika filter.

Mer information finns i [filter och dynamiska manifesten](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Lokaliserare
Om du vill ge din användare en URL som kan användas för att strömma eller hämta ditt innehåll, måste du först publicera din tillgång genom att skapa en positionerare. En positionerare ger en startpunkt för att komma åt filer i en tillgång. Media Services stöder två typer av lokaliserare:

* OnDemandOrigin-positionerare. Dessa används för att strömma media (till exempel MPEG DASH, HLS eller Smooth Streaming) eller hämta progressivt filer.
* Lokaliserare för delad åtkomst (SAS)-signaturen URL. Dessa används för att hämta filer till den lokala datorn.

En *princip* används för att definiera behörigheter (till exempel läsa, skriva och listan) och varaktighet som en klient har åtkomst för en viss resurs. Observera att listan behörighet (AccessPermissions.List) inte kan användas i att skapa en positionerare OrDemandOrigin.

Lokaliserare har förfallodatum. Azure-portalen anger ett utgångsdatum 100 år i framtiden för lokaliserare.

> [!NOTE]
> Om du använder Azure-portalen för att skapa lokaliserare före mars 2015, dessa lokaliserare har ställts in att gälla efter två år.
> 
> 

Du uppdaterar ett utgångsdatum för en lokaliserare med [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator)- eller [.NET](http://go.microsoft.com/fwlink/?LinkID=533259)-API:er. Observera att URL:en ändras när du uppdaterar en SAS-lokaliserare.

Lokaliserare är inte avsedda att hantera åtkomstkontroll per användare. Du kan ge olika åtkomsträttigheter till enskilda användare med hjälp av Digital Rights Management (DRM) lösningar. Mer information finns i [skydda Media](http://msdn.microsoft.com/library/azure/dn282272.aspx).

När du skapar en positionerare kan finnas det en fördröjning på grund av lagring och spridningen processer i Azure Storage på 30 sekunder.

## <a name="adaptive-streaming"></a>Anpassad strömning
Anpassningsbar bithastighet teknik kan videospelarprogram vill fastställa nätverksförhållandena och välja bland flera bithastighet. När nätverkskommunikation försämrar kan klienten välja en lägre bithastighet så uppspelning kan fortsätta med lägre bildkvaliteten. Som nätverksförhållanden förbättra kan klienten växla till en högre bithastighet med förbättrad bildkvaliteten. Azure Media Services stöder följande tekniker för anpassningsbar bithastighet: HTTP Live Streaming (HLS), Smooth Streaming och MPEG-DASH.

För att ge användare URL: er för strömning måste skapa du först en OnDemandOrigin-positionerare. Att skapa positioneraren ger rotsökvägen till den tillgång som innehåller det innehåll som du vill strömma. Men om du vill kunna strömma innehållet behöver du ändra den här sökvägen ytterligare. Om du vill skapa en fullständig URL till den strömmande manifestfilen måste du sammanfoga värdet för den positionerare sökväg och manifestet (filename.ism) namn. Lägg sedan till **/Manifest** och ett lämpligt format (vid behov) till sökvägen lokaliserare.

> [!NOTE]
> Du kan också strömma ditt innehåll via en SSL-anslutning. Gör detta genom att kontrollera att din strömmande URL: er som börjar med HTTPS. Observera att för närvarande AMS inte stöder SSL med anpassade domäner.  
> 

Du kan bara strömma via SSL om den strömningsslutpunkt från vilken du kan leverera ditt innehåll skapades efter 10 September 2014. Om din strömmande URL: er som är baserade på strömningsslutpunkter som skapats efter 10 September 2014 innehåller URL: en ”streaming.mediaservices.windows.net”. Strömmande URL: er som innehåller ”origin.mediaservices.windows.net” (det gamla formatet) stöder inte SSL. Om URL: en är i formatet gamla och du vill kunna strömma via SSL, skapa en ny strömmande slutpunkt. Använd webbadresserna baserat på den nya strömmande slutpunkten för att strömma ditt innehåll via SSL.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Strömmande URL-format

### <a name="mpeg-dash-format"></a>MPEG-DASH-format
{strömmande slutpunkten namn media services-konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4-format
{strömmande slutpunkten namn media services-konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3-format
{strömmande slutpunkten namn media services-konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming (HLS)-format med ljuddata filter
Som standard endast ljud spårar ingår i HLS manifest. Detta krävs för Apple Store certifikatutfärdare för mobila nätverk. Om en klient inte har tillräckligt med bandbredd eller är ansluten via en anslutning 2G, växlar i det här fallet uppspelning till ljuddata. Detta hjälper till att hålla innehåll strömning utan buffert, men bild ingen. I vissa fall kanske player buffring prioriterade över ljuddata. Om du vill ta bort ljuddata spåra lägger du till **ljuddata = false** i URL: en.

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3ljuddata = false)

Mer information finns i [dynamisk sammansättning i Manifest support och HLS utdata ytterligare funktioner](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Smooth Streaming-format
{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

Exempel:

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Smooth Streaming 2.0 manifestet (äldre manifestet)
Som standard innehåller Smooth Streaming manifestet format taggen upprepningar (r-tagg). Dock stöder inte vissa spelare r-taggen. Klienter med dessa spelare kan använda ett format som inaktiverar r-taggen:

{strömmande slutpunkten namn media services-konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Progressiv hämtning
Du kan starta uppspelning innan hela filen har hämtats med progressiv nedladdning. Du kan inte progressivt hämta .ism * (ismv isma, ismt eller ismc) filer.

För att progressivt hämta innehåll, använder du den OnDemandOrigin typen av lokaliserare. I följande exempel visas den URL som baseras på OnDemandOrigin typ av lokaliserare:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Du måste dekryptera krypterade lagring tillgångar som du vill strömma från tjänsten ursprung för progressiv nedladdning.

## <a name="download"></a>Ladda ned
Om du vill hämta ditt innehåll till en klientenhet, måste du skapa en SAS-lokaliserare. SAS-lokaliserare ger dig tillgång till Azure Storage-behållare där filen finns. Du måste bädda in filnamnet mellan värden och den SAS-signaturen för att skapa nedladdnings-URL.

I följande exempel visas den URL som baseras på SAS-positionerare:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Följande gäller:

* Du måste dekryptera krypterade lagring tillgångar som du vill strömma från tjänsten ursprung för progressiv nedladdning.
* En fil som inte har slutförts inom 12 timmar misslyckas.

## <a name="streaming-endpoints"></a>Slutpunkter för direktuppspelning

En strömningsslutpunkt som representerar en strömmande tjänst som kan leverera innehåll direkt till ett klientprogram player eller till ett nätverk för innehållsleverans (CDN) för vidare distribution. Utgående dataströmmen från en strömmande slutpunkt-tjänst kan vara en direktsänd dataström eller en video-on-demand tillgångar i Media Services-kontot. Det finns två typer av strömningsslutpunkter, **standard** och **premium**. Mer information finns i [Översikt över strömningsslutpunkter](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

## <a name="known-issues"></a>Kända problem
### <a name="changes-to-smooth-streaming-manifest-version"></a>Ändringar av Smooth Streaming manifest version
Innan versionen av tjänsten juli 2016--när tillgångar produceras av Media Encoder Standard Media Encoder Premium arbetsflöde eller tidigare Azure Media Encoder har strömmats med hjälp av dynamisk paketering--Smooth Streaming manifestet returnerade skulle överensstämmer med version 2.0. I version 2.0 Använd inte fragment varaktigheter s.k. Upprepa (r)-taggar. Exempel:

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

I juli 2016 service release uppfyller genererade Smooth Streaming manifestet till version 2.2, fragment varaktigheter med Upprepa taggar. Exempel:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Vissa av äldre Smooth Streaming-klienter stöder inte upprepa taggar och kommer inte att kunna läsa in manifestet. Om du vill undvika det här problemet kan du använda formatparametern äldre manifestet **(format = fmp4 v20)** eller uppdatera klienten till den senaste versionen som har stöd för Upprepa taggar. Mer information finns i [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Uppdatera Media Services lokaliserare efter rullande lagringsnycklar](media-services-roll-storage-access-keys.md)

