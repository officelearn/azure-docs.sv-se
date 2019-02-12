---
title: Filter och dynamiska manifest | Microsoft Docs
description: Det här avsnittet beskriver hur du skapar filter så att klienten kan använda dem till stream vissa delar av en dataström. Media Services skapar dynamiska manifest för att arkivera den här selektiva strömning.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/10/2019
ms.author: cenkd;juliako
ms.openlocfilehash: c982707b24f18e840e866b1dcc858fe2aceb686e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000018"
---
# <a name="filters-and-dynamic-manifests"></a>Filter och dynamiska manifest

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Version 2](media-services-dynamic-manifest-overview.md)
> * [Version 3](../latest/filters-dynamic-manifest-overview.md)

Från och med 2.17, kan Media Services du definiera filter för dina tillgångar. Dessa filter är serversidan regler som gör att kunderna kan välja att till exempel: spela bara en del av en video (i stället för att spela upp videon och hela) eller ange endast en delmängd av ljud och video återgivningar att din kunds enheten kan hantera () i stället för alla återgivningar som är associerade med tillgången). Den här filtrering av dina tillgångar uppnås via **dynamiska Manifest**s som skapas på din kunds begäran för direktuppspelning av video baserat på angivna filter.

Det här avsnittet beskriver vanliga scenarier som skulle vara fördelaktigt att dina kunder och länkar till ämnen som visar hur du kan skapa filter programmässigt med hjälp av filter.

## <a name="overview"></a>Översikt
När du levererar ditt innehåll till kunder (streaming direktsändningar eller video på begäran) är målet att leverera en video med hög kvalitet till olika enheter under olika nätverksförhållanden. Att uppnå detta mål att göra följande:

* koda strömmen till flera bithastigheter ([med anpassningsbar bithastighet](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) videoströmmen (Detta tar hand om kvalitet och nätverk) och 
* Använd Media Services [dynamisk paketering](media-services-dynamic-packaging-overview.md) dynamiskt Ompaketera din ström till olika protokoll (Detta tar hand om strömning på olika enheter). Media Services har stöd för leverans av de följande strömningstekniker med anpassningsbar bithastighet: HTTP Live Streaming (HLS), Smooth Streaming och MPEG DASH. 

### <a name="manifest-files"></a>Manifestfiler
När du koda en tillgång för strömning med anpassad bithastighet en **manifest** () spellistefil skapas (filen är text- eller XML-baserade). Den **manifest** -filen innehåller metadata för direktuppspelning till exempel: spåra typ (ljud, video eller text), spåra namn, start-och sluttid, med flera bithastigheter (Egenskaper), spåra språk, presentationsfönstret (Hoppande fönster med fast varaktighet), video-codec ( FourCC). Det instruerar också spelaren att hämta nästa fragment genom att tillhandahålla information om nästa kan spelas upp video fragmenten tillgängliga och deras plats. Fragment (eller segment) är de faktiska ”mängder” en videoinnehåll.

Här är ett exempel på en manifestfil: 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>Dynamiska manifest
Det finns [scenarier](media-services-dynamic-manifest-overview.md#scenarios) när klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Exempel:

* Enhet som är specifik: leverera endast den angivna återgivningar och/eller spårar för angivet språk som stöds av den enhet som används för att spela upp innehållet (”återgivningsfiltrering”). 
* Minska manifest för att visa ett underordnade klipp en direktsändning (”underklipp filtrering”).
* Trimma början av en video (”trimmar en video”).
* Justera Presentation fönstret (DVR) för att tillhandahålla en begränsad tidslängd DVR-perioden i spelaren (”justera presentationsfönster”).

För att uppnå den här flexibiliteten, Media Services erbjuder **dynamiska manifest** baserat på fördefinierade [filter](media-services-dynamic-manifest-overview.md#filters).  När du har definierat filtren kan klienterna använda dem för att strömma en specifik återgivning eller underordnade klipp av videon. Filter ska anges i strömnings-URL. Filter kan tillämpas på strömning med anpassad bithastighet protokoll som stöds av [dynamisk paketering](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH och Smooth Streaming. Exempel:

URL för MPEG-DASH med filter

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Smooth Streaming URL med filter

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Mer information om hur du leverera innehåll och skapa strömmande URL: er finns i [leverera innehåll – översikt](media-services-deliver-content-overview.md).

> [!NOTE]
> Observera att dynamiskt manifest inte ändrar tillgången och standard-manifest för tillgången. Klienten kan du begära en dataström med eller utan filter. 
> 
> 

### <a id="filters"></a>Filter
Det finns två typer av tillgången filter: 

* Globala filter (kan tillämpas på alla tillgångar i Media Services-konto, har en livslängd på kontot) och 
* Lokala filter (kan bara tillämpas på en tillgång som filtret associerades när den har skapats, har en livslängd på tillgången). 

Globala och lokala filtertyper har exakt samma egenskaper. Den största skillnaden mellan två är vilken typ av arkiverar är lämpligast för vilka scenarier. Globala filter är vanligtvis lämpliga för enhetsprofiler (återgivningsfiltrering) där lokala filter kan användas för att trimma en specifik tillgång.

## <a id="scenarios"></a>Vanliga scenarier
Som nämndes innan när du levererar ditt innehåll till kunder (streaming direktsändningar eller video på begäran) målet är att leverera en video med hög kvalitet till olika enheter under olika nätverksförhållanden. Dessutom kan du kan ha andra krav som rör filtrera dina tillgångar och om hur du använder **dynamiska Manifest**s. I följande avsnitt ger en kort översikt över olika scenarier som filtrerande.

* Ange endast en delmängd av ljud och video återgivningar som vissa enheter kan hantera (i stället för alla återgivningar som är associerade med tillgången). 
* Spela upp endast en del av en video (i stället för hela videon).
* Justera DVR-perioden för presentation.

## <a name="rendition-filtering"></a>Återgivningsfiltrering
Du kan välja att koda din tillgång till flera kodning profiler (H.264 baslinje, H.264 hög, AACL, AACH, Dolby Digital Plus) och flera olika bithastigheter kvalitet. Dock stöder inte alla klientenheter din tillgångs-profiler och bithastigheter för utdata. Äldre Android-enheter stöder till exempel bara H.264 baslinje + AACL. Skicka högre bithastighet till en enhet som det går inte att hämta fördelarna, slösar nätverksbandbredd och enheten beräkning. Sådana enheter måste avkoda alla angivna information endast för Nedskalning för visning.

Med dynamiska Manifest kan du skapa enhetsprofiler som mobila enheter, konsolen HD/SD osv och inkludera spårar och egenskaper som du vill ska vara en del av varje profil.

![Återgivning filtrering exempel][renditions2]

I följande exempel har en kodare använts för att koda en mezzanine tillgång till sju ISO MP4s video återgivningar (från 180p 1080p). Den kodade tillgången kan dynamiskt paketeras i någon av de följande protokollen med direktuppspelning: HLS, Smooth och MPEG DASH.  Längst ned i diagrammet visas HLS-manifest för tillgången utan filter (den innehåller alla sju återgivningar).  HLS-manifest som ett filter med namnet ”ott” användes visas i nedre vänstra hörnet. Filtret ”ott” anger för att ta bort alla bithastigheter för utdata nedan 1 Mbit/s, vilket resulterade i kvalitetsnivå längst ned två tas bort i svaret. I nederkant högra visas HLS manifestet som ett filter med namnet ”mobil” användes. Filtret ”mobil” anger för att ta bort återgivningar där den är större än 720p, vilket resulterade i två 1080p återgivningar tas bort.

![Återgivningsfiltrering][renditions1]

## <a name="removing-language-tracks"></a>Ta bort språkspår
Dina tillgångar kan innehålla flera ljud språk, till exempel engelska, spanska, franska, osv. Vanligtvis Player SDK-chefer standard ljudspår val och tillgängliga ljud spårar per användarens val. Är det svårt att utveckla sådana Player SDK: er, den kräver olika implementeringar över enhetsspecifika spelarramverk. Dessutom begränsas Player API: er på vissa plattformar och inkluderar inte ljudspår funktion där användare inte kan markera eller ändra standard ljudspår. Du kan styra beteendet genom att skapa filter som endast innehåller önskade ljud språk med tillgången filter.

![Språk spårar filtrering][language_filter]

## <a name="trimming-start-of-an-asset"></a>Ta bort början av en tillgång
I de flesta liveuppspelningshändelser kör operatörer vissa tester innan den faktiska händelsen. De kan till exempel innehålla en bakgrundsbild så här innan händelsen: ”Inleds tillfälligt”. Om programmet arkivering, test- och statiska data också arkiveras och ingår i presentationen. Den här informationen bör dock inte att visas för klienterna. Du kan skapa en starttidsfilter och ta bort oönskade data från manifestet med dynamiska Manifest.

![Ta bort start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Skapa underklipp (vyer) från en live-arkivet
Många direktsändningar är långvariga och live-arkivet kan innehålla flera händelser. När livehändelsen är slut, kanske sändningsföretag vill dela upp live-arkivet till logiska programmet startas och stoppa sekvenser. Därefter publicera separat dessa virtuella program utan efter bearbetning av live-arkivet och inte skapa separata resurser (som inte får fördelen med de befintliga cachelagrade fragment i de CDN-nät). Exempel på sådana virtuella program är kvartal football eller basket spel, innings i basket eller enskilda händelser för alla sport-program.

Med dynamiska Manifest kan du skapa filter med start-och sluttider och skapa virtuella vyer över dina live-arkivet. 

![Underklipp filter][subclip_filter]

Filtrerade tillgång:

![Skidåkning][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Justera presentationsfönstret (DVR)
Azure Media Services erbjuder för närvarande, cirkulär Arkiv där varaktigheten kan konfigureras mellan 5 minuter – 25: e timme. Manifest filtrering kan användas för att skapa en löpande DVR-perioden över upp arkivet, utan att ta bort media. Det finns många scenarier där sändningsföretag vill tillhandahålla en begränsad DVR-perioden för att flytta med live edge och samtidigt hålla ett större arkivering fönster. En broadcast kanske vill använda de data som ligger utanför fönstret DVR att markera klipp eller he\she kanske vill ge olika DVR-fönster för olika enheter. De flesta av de mobila enheterna hantera inte exempelvis stora DVR windows (du kan ha en 2-minut DVR-perioden för mobila enheter och en timme för stationära klienter).

![DVR-perioden][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Justera LiveBackoff (direktsända positionen)
Manifest filtrering kan användas för att ta bort några sekunder från live gränsen för en live. Filtrering kan sändningsföretag se presentationen på förhandsversion publikationen punkt och skapa annonsen infogas punkter innan användarna får den dataström (backas upp av med 30 sekunder). Programleverantörer kan sedan skicka dessa annonser till sina klientramverk i tid för dem att mottagna och bearbeta information innan annons-möjlighet.

Förutom annons-support användas inställningen LiveBackoff för att justera positionen för användarna så att när klienter avviker och når live edge kan de fortfarande få fragment från servern i stället för att få ett HTTP 404 eller 412 fel.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinera flera regler i ett enda filter
Du kan kombinera flera filtreringsregler i ett enda filter. Som exempel kan du definiera ”intervallet regeln” ta bort pekdatorer från en live-arkivet och även filtrerat bort tillgängliga bithastighet. När du använder flera filtreringsregler är slutresultatet skärningspunkten för alla regler.

![flera regler][multiple-rules]

## <a name="create-filters-programmatically"></a>Skapa filter programmässigt
I följande artikel beskriver Media Services-entiteter som är relaterade till filter. Artikeln beskriver också hur du programmässigt skapar filter.  

[Skapa filter med REST API: er](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinera flera filter (filter sammansättning)
Du kan också kombinera flera filter i en enskild URL. 

Följande scenario visar varför du kanske vill kombinera filter:

1. Du måste filtrera din video egenskaper för mobila enheter som Android- eller iPAD (för att begränsa video egenskaper). Om du vill ta bort oönskade egenskaper, skapar du ett globalt filter passar enhetsprofilerna. Som nämnts tidigare i den här artikeln, kan globala filter användas för alla dina tillgångar under samma media services-konto utan ytterligare association. 
2. Du bör också att trimma start- och tidpunkten för en tillgång. För att uppnå detta skulle du skapa ett lokalt filter och ange starttid/sluttid. 
3. Du vill kombinera båda dessa filter (utan kombination, du måste lägga till kvalitet filtrering för att ta bort filtret som gör det svårare filter användning).

Om du vill kombinera filter, måste du ange filternamn i manifestet/spelningslistan URL med semikolonavgränsad lista. Vi antar att du har ett filter med namnet *MyMobileDevice* som filtrerar egenskaper och det finns en annan med namnet *MyStartTime* att ställa in en viss starttid. Du kan kombinera dem så här:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Du kan kombinera upp till tre filter. 

Mer information finns i [detta](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogg.

## <a name="know-issues-and-limitations"></a>Vet problem och begränsningar
* Dynamiska manifest körs i GOP gränser (nyckel ramar) därför trimmar har GOP precision. 
* Du kan använda samma filternamnet för lokala och globala filter. Lokala filter har högre prioritet och åsidosätter globala filter.
* Om du uppdaterar ett filter, kan det ta upp till 2 minuter för slutpunkten för direktuppspelning att uppdatera reglerna. Om innehållet behandlades använda filter och cachelagras i proxyservrar och CDN cacheminnen, kan uppdaterar de här filtren resultera i player-fel. Det rekommenderas att rensa cacheminnet när du har uppdaterat filtret. Överväg att använda ett annat filter om det här alternativet inte är möjligt.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Leverera innehåll till kunder (översikt)](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
