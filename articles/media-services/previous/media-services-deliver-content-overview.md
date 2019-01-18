---
title: Leverera innehåll till kunder | Microsoft Docs
description: Det här avsnittet ger en översikt över vad ingår i att leverera innehåll med Azure Media Services.
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
ms.openlocfilehash: 3e968c71b87f7227dc4bd2e3f4ee486719b65e40
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388156"
---
# <a name="deliver-content-to-customers"></a>Leverera innehåll till kunder
När du levererar ditt streaming eller video-on-demand-innehåll till kunder, är målet att leverera video med hög kvalitet till olika enheter under olika nätverksförhållanden.

För att åstadkomma detta kan du:

* Koda strömmen till en video-ström med flera bithastigheter (anpassningsbar bithastighet). Detta hand tar om kvalitet och nätverk.
* Använda Microsoft Azure Media Services [dynamisk paketering](media-services-dynamic-packaging-overview.md) dynamiskt Ompaketera din ström till olika protokoll. Detta hand tar om strömning på olika enheter. Media Services har stöd för leverans av de följande strömningstekniker med anpassningsbar bithastighet: <br/>
    * **HTTP Live Streaming** (HLS) – Lägg till ”(format = m3u8-aapl)” sökväg till ”/ Manifest”-delen i Webbadressen ska berätta för strömmande ursprungsservern att returnera tillbaka HLS-innehåll för användning på **Apple iOS** ursprungliga enheter (Mer information Se [positionerare](#locators) och [URL: er](#URLs)),
    * **MPEG-DASH** – Lägg till ”(format = mpd-time-csf)” sökväg till ”/ Manifest”-delen i Webbadressen ska berätta för strömmande ursprungsservern att returnera tillbaka MPEG-DASH (Mer information finns i [positionerare](#locators) och [URL: er](#URLs)),
    * **Smooth Streaming**.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

Den här artikeln ger en översikt över viktiga innehållsleverans begrepp.

Du kan kontrollera kända problem [kända problem](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamisk paketering
Med dynamisk paketering som Media Services tillhandahåller, du kan leverera innehåll med anpassad bithastighet MP4 eller Smooth Streaming-kodade i strömningsformat som stöds av Media Services (MPEG-DASH, HLS, Smooth Streaming) utan att behöva Ompaketera till dessa strömningsformat. Vi rekommenderar att leverera innehåll med dynamisk paketering.

Om du vill dra nytta av dynamisk paketering, måste du koda din mezzaninfil (källa) till en uppsättning MP4-filer med anpassningsbar bithastighet eller Smooth Streaming-filer.

Med dynamisk paketering behöver lagra och betala för filerna i ett enda lagringsformat. Media Services skapar och ger lämplig respons baserat på dina önskemål.

Dynamisk paketering är tillgänglig för standard och premium-slutpunkter för direktuppspelning. 

Mer information finns i [dynamisk paketering](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filter och dynamiska manifest
Du kan definiera filter för dina tillgångar med Media Services. Dessa filter är serversidan regler som hjälper dina kunder göra saker som att spela upp en viss del av en video eller ange en delmängd av ljud och video återgivningar som din kunds enheten kan hantera (i stället för alla återgivningar som är associerade med tillgången). Den här filtreringen uppnås via *dynamiska manifest* som skapas när kunden begär för direktuppspelning av video baserat på en eller flera specifika filter.

Mer information finns i [filter och dynamiska manifest](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Positionerare
Om du vill förse din användare med en URL som kan användas för att strömma eller hämta ditt innehåll, måste du först publicera tillgången genom att skapa en lokaliserare. En positionerare ger en startpunkt för att komma åt filer i en tillgång. Media Services stöder två typer av lokaliserare:

* OnDemandOrigin-positionerare. Dessa används för att strömma media (till exempel MPEG-DASH, HLS eller Smooth Streaming) eller hämta progressivt filer.
* Delad åtkomst (signatur) URL: en positionerare. De används för att hämta mediefiler till din lokala dator.

En *princip* används för att definiera behörigheter (till exempel läsa, skriva och lista) och varaktighet som en klient har åtkomst för en viss resurs. Observera att listan behörighet (AccessPermissions.List) inte ska användas när man skapar en OnDemandOrigin-positionerare.

Lokaliserare har förfallodatum. Azure-portalen anger ett utgångsdatum 100 år i framtiden för lokaliserare.

> [!NOTE]
> Om du använde Azure-portalen för att skapa lokaliserare före mars 2015, dessa lokaliserare har ställts in upphör att gälla efter två år.
> 
> 

Du uppdaterar ett utgångsdatum för en lokaliserare med [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator)- eller [.NET](https://go.microsoft.com/fwlink/?LinkID=533259)-API:er. Observera att URL:en ändras när du uppdaterar en SAS-lokaliserare.

Positionerare är inte utformade för att hantera åtkomstkontroll per användare. Du kan ge olika åtkomsträttigheter till enskilda användare med hjälp av lösningar för Digital Rights Management (DRM). Mer information finns i [skydda Media](https://msdn.microsoft.com/library/azure/dn282272.aspx).

När du skapar en positionerare kan finnas det en fördröjning på 30 sekunder på grund av lagring som krävs och spridning processer i Azure Storage.

## <a name="adaptive-streaming"></a>Adaptiv direktuppspelning
Med anpassningsbar bithastighet tekniker kan videospelarprogram att fastställa nätverksförhållanden och välja från flera olika bithastigheter. När nätverkskommunikation försämras kan klienten välja en lägre bithastighet så uppspelning kan fortsätta med lägre videokvalitet. Eftersom nätverksförhållanden förbättra, kan klienten växla till en högre bithastighet med bättre videokvalitet. Azure Media Services stöder följande tekniker med anpassningsbar bithastighet: HTTP Live Streaming (HLS), Smooth Streaming och MPEG-DASH.

För att ge användare strömmande URL: er måste skapa du först en OnDemandOrigin-positionerare. Skapa lokaliseraren får du grundläggande sökvägen till den tillgång som innehåller det innehåll som du vill spela. Men om du vill kunna strömma innehållet behöver du ändra den här sökvägen ytterligare. Om du vill skapa en fullständig URL till strömmande manifestfilen måste du sammanfoga den lokaliseraren sökvägsvärde och manifestet (filename.ism) filnamn. Lägger till **/Manifest** och ett lämpligt format (vid behov) till positionerare sökväg.

> [!NOTE]
> Du kan också strömma ditt innehåll över en SSL-anslutning. Om du vill göra detta måste du kontrollera att din strömmande URL: er börjar med HTTPS. Observera att för närvarande AMS inte stöder SSL med anpassade domäner.  
> 

Du kan endast strömmas via SSL om slutpunkten för direktuppspelning som du kan leverera ditt innehåll har skapats efter den 10 September 2014. Om din strömmande URL: er baseras på de slutpunkter för direktuppspelning som skapats efter den 10 September 2014, innehåller URL: en ”streaming.mediaservices.windows.net”. Strömmande URL: er som innehåller ”origin.mediaservices.windows.net” (det äldre formatet) stöder inte SSL. Om din URL: en används det äldre formatet och du vill kunna strömma via SSL, kan du skapa en ny slutpunkt för direktuppspelning. Använd webbadresserna baserat på den nya strömmande slutpunkten för att strömma ditt innehåll via SSL.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Strömmande URL-format

### <a name="mpeg-dash-format"></a>MPEG-DASH-format
{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4-format
{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3-format
{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming (HLS)-format med enbart ljud filter
Som standard enbart ljud spårar som ingår i HLS manifest. Detta krävs för Apple Store-certifiering för mobila nätverk. I så fall om en klient har inte tillräckligt mycket bandbredd eller är ansluten via en anslutning 2G, växlar uppspelning till enbart ljud. Detta hjälper till att hålla strömning av innehåll utan buffert, men bild ingen. I vissa fall kanske player buffring prioriterade över enbart ljud. Om du vill ta bort enbart ljud-spår, lägger du till **ljuddata = false** i URL: en.

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3enbart ljud = false)

Mer information finns i [stöd för dynamiska Manifest sammansättning och HLS utdata ytterligare funktioner](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Smooth Streaming-format
{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

Exempel:

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Smooth Streaming 2.0 manifest (äldre manifest)
Som standard innehåller Smooth Streaming-manifest format taggen upprepningar (r-tagg). Vissa spelare stöder dock inte r-taggen. Klienter med dessa spelare kan använda ett format som inaktiverar r-taggen:

{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Progressiv nedladdning
Du kan börja spela upp media innan hela filen har hämtats med progressiv nedladdning. Du kan inte progressivt hämta .ism * (ismv, isma, ismt eller ismc) filer.

För att progressivt hämta innehåll, använder du den OnDemandOrigin typen av lokaliserare. I följande exempel visas den URL som baseras på OnDemandOrigin typ av positionerare:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Du måste dekryptera alla storage-krypterade objekt som du vill spela från tjänsten ursprung för progressiv nedladdning.

## <a name="download"></a>Ladda ned
Du måste skapa en SAS-lokaliserare för att ladda ned innehållet till en klientenhet. SAS-lokaliserare ger dig tillgång till Azure Storage-behållare där filen finns. Du måste bädda in namnet på filen mellan värden och SAS-signaturen för att skapa nedladdnings-URL.

I följande exempel visas den URL som baseras på SAS-lokaliserare:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Följande gäller:

* Du måste dekryptera alla storage-krypterade objekt som du vill spela från tjänsten ursprung för progressiv nedladdning.
* En nedladdning som inte har slutförts inom 12 timmar misslyckas.

## <a name="streaming-endpoints"></a>Slutpunkter för direktuppspelning

En slutpunkt för direktuppspelning representerar en direktuppspelningstjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett nätverk för innehållsleverans (CDN) för vidare distribution. Den utgående dataströmmen från en strömmande slutpunkt-tjänst kan vara en direktsänd dataström eller en video på begäran-tillgång i Media Services-kontot. Det finns två typer av slutpunkter för direktuppspelning, **standard** och **premium**. Mer information finns i [Översikt över strömningsslutpunkter](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

## <a name="known-issues"></a>Kända problem
### <a name="changes-to-smooth-streaming-manifest-version"></a>Ändringar av Smooth Streaming manifest version
Innan den juli 2016 tjänsteuppdateringen--när tillgångar som produceras av Media Encoder Standard Media Encoder Premium Workflow eller tidigare Azure Media Encoder har strömmas med hjälp av dynamisk paketering--Smooth Streaming manifest returneras skulle överensstämmer med version 2.0. I version 2.0 Använd inte fragment varaktigheter så kallade Upprepa (r)-taggar. Exempel:

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

I juli 2016 service release, genererat Smooth Streaming-manifest överensstämmer med version 2.2, med fragment varaktigheter med hjälp av upprepningar taggar. Exempel:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Några av de äldre Smooth Streaming-klienterna stöder inte upprepa taggar och kommer inte att läsa in manifestet. För att lösa problemet, kan du använda formatparametern äldre manifest **(format = fmp4 v20)** eller uppdatera klienten till den senaste versionen, vilket stöder Upprepa taggar. Mer information finns i [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Uppdatera lokaliserare för Media Services efter återställning av lagringsnycklar](media-services-roll-storage-access-keys.md)

