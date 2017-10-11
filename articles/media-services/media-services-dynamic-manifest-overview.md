---
title: Filter och dynamiska manifesten | Microsoft Docs
description: "Det här avsnittet beskriver hur du skapar filter så att klienten kan använda dem till dataströmmen vissa delar av en dataström. Media Services skapar dynamiska manifest för att arkivera den här selektiv strömning."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 4034fd0aa64627c107a43208dcca766f7f44d5d4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="filters-and-dynamic-manifests"></a>Filter och dynamiska manifest
Från och med 2.11 kan Media Services du definiera filter för dina tillgångar. Dessa filter är server sida regler som gör att kunderna kan välja att till exempel: uppspelning endast en del av en video (i stället för hela video), eller ange bara en del av ljud och video återgivningar som kundens enhet kan hantera (i stället alla återgivningar som är kopplade till tillgången). Den här filtreringen dina tillgångar arkiveras via **dynamiska Manifest**s som skapas på kundens begäran för direktuppspelning av video baserat på angivna filter.

Detta avsnitt beskriver vanliga scenarier som skulle vara mycket bra att kunder och länkar till avsnitt som visar hur du skapar filter programmässigt med hjälp av filter (för närvarande kan du skapa filter med REST API: er endast).

## <a name="overview"></a>Översikt
Målet är att tillhandahålla en video av hög kvalitet till olika enheter under olika nätverksförhållanden när du levererar ditt innehåll till kunder som (streaming direktsända händelser eller video-on-demand). Att uppnå det här målet genom att göra följande:

* koda strömmen till flera bithastigheter ([anpassningsbar bithastighet](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) video-ström (Detta tar hand om kvalitet och nätverk) och 
* Använd Media Services [dynamisk paketering](media-services-dynamic-packaging-overview.md) till dynamiskt Ompaketera din ström till olika protokoll (Detta tar hand om strömning på olika enheter). Media Services stöder leverans av följande anpassningsbar bithastighet tekniker för strömning: HTTP Live Streaming (HLS), Smooth Streaming och MPEG DASH. 

### <a name="manifest-files"></a>Manifest-filer
När du koda en tillgång för anpassningsbar bithastighet strömning en **manifest** skapas (spelningslista)-fil (filen är text- eller XML-baserade). Den **manifest** filen innehåller strömning metadata som: spåra typ (ljud, video eller text), spåra namn, start-och sluttid, bithastighet (Egenskaper), spåra språk, presentationsfönstret (skjutfönster fast varaktighet), video-codec ( FourCC). Det gör även player att hämta nästa avsnitt ger information om de nästa spelas upp video fragment tillgängliga och deras plats. Fragment (eller segment) är de faktiska ”mängder” en videoinnehåll.

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

### <a name="dynamic-manifests"></a>Dynamisk manifest
Det finns [scenarier](media-services-dynamic-manifest-overview.md#scenarios) när klienten behov bättre flexibilitet än vad som beskrivs i standard tillgången manifestfilen. Exempel:

* Enhetsspecifik: leverera endast den angivna återgivningar och/eller angetts språkspår som stöds av den enhet som är används för att spela upp innehållet (”återgivning filtrering”). 
* Minska manifestet för att visa ett underordnade klipp för en direktsänd händelse (”underordnade clip filtrering”).
* Beskär början av en video (”trimning en video”).
* Justera Presentation fönstret (DVR) för att ge en begränsad längd på fönstret DVR i player (”justera presentationsfönster”).

För att uppnå den här flexibiliteten Media Services erbjuder **dynamiska visar** baserat på fördefinierade [filter](media-services-dynamic-manifest-overview.md#filters).  När du har definierat filtren kan klienterna använda dem för att strömma en specifik återgivning eller underordnade klipp av videon. De vill ange filter i strömnings-URL. Filter kan tillämpas på protokoll som stöds av strömning med anpassningsbar bithastighet [dynamisk paketering](media-services-dynamic-packaging-overview.md): HLS MPEG-DASH och Smooth Streaming. Exempel:

URL för MPEG DASH med filter

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Smooth Streaming URL med filtret

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Mer information om hur du kan leverera ditt innehåll och bygga URL: er för strömning finns [leverera Innehållsöversikt](media-services-deliver-content-overview.md).

> [!NOTE]
> Observera att dynamisk visar inte ändrar tillgången och standardmanifestet för tillgången. Klienten kan du begära en dataström med eller utan filter. 
> 
> 

### <a id="filters"></a>Filter
Det finns två typer av tillgångsinformation filter: 

* Globala filter (kan tillämpas på alla tillgångar i Azure Media Services-konto, har en livslängd på kontot) och 
* Lokala filter (kan bara tillämpas på en tillgång som kopplades filtret har skapats, har en livslängd på tillgången). 

Globala och lokala filtyperna ha exakt samma egenskaper. Den största skillnaden mellan två är vilken typ av arkiverar är lämpligast för vilka scenarier. Globala filter är normalt lämpliga för profiler för enheter (återgivning filtrering) där lokala filter kan användas för att ta bort en specifik resurs.

## <a id="scenarios"></a>Vanliga scenarier
Som nämnts innan när du levererar ditt innehåll till kunder som (streaming direktsända händelser eller video-on-demand) målet är att tillhandahålla en video av hög kvalitet till olika enheter under olika nätverksförhållanden. Dessutom din kan ha andra krav som inbegriper filtrering dina tillgångar och om hur du använder **dynamiska Manifest**s. Följande avsnitt ger en kort översikt över olika scenarier för filtrering.

* Ange endast en delmängd av ljud och video återgivningar som vissa enheter kan hantera (i stället för alla återgivningar som är associerade med tillgången). 
* Spela upp endast en del av en video (i stället för hela video).
* Justera DVR presentationsfönster.

## <a name="rendition-filtering"></a>Återgivning filtrering
Du kan välja att koda din tillgång till flera kodning profiler (H.264 baslinje, H.264 hög, AACL, AACH, Dolby Digital Plus) och flera olika bithastigheter kvalitet. Dock stöder inte alla klientenheter din tillgångs profiler och bithastighet. Äldre Android-enheter stöder till exempel bara H.264 baslinjen + AACL. Skicka högre bithastighet till en enhet som inte kan hämta fördelarna slösar bandbredd och enheten beräkning. Dessa enheter måste avkoda alla angivna information bara att skala den för visning.

Med dynamisk Manifest kan du skapa profiler för enhet, till exempel mobile, konsolen HD/SD, etc. och inkludera spårar och egenskaper som du vill ska vara en del av varje profil.

![Filtrera återgivning-exempel][renditions2]

I följande exempel har en kodare för att koda en mezzanine tillgång till sju ISO MP4s video återgivningar (från 180p-1080p). Den kodade tillgången dynamiskt kan paketeras i något av följande protokoll för dataströmmar: MPEG DASH, HLS och Smooth.  Längst upp i diagrammet HLS manifest för tillgångsinformation utan filter visas (den innehåller alla sju återgivningar).  Manifestet HLS som ett filter med namnet ”ott” användes visas längst ned till vänster. Filtret ”ott” anger att ta bort alla bithastighet nedan 1 Mbit/s, vilket resulterade i de nedre två olika tas bort i svaret.  I nederkant högra visas HLS manifestet som ett filter med namnet ”mobil” användes. ”Mobil” filter anger för att ta bort återgivningar där upplösningen är större än 720p, vilket resulterade i två 1080p återgivningar tas bort.

![Återgivning filtrering][renditions1]

## <a name="removing-language-tracks"></a>Spårar för att ta bort språk
Dina tillgångar kan innehålla flera ljud språk engelska, spanska, franska, t.ex. Vanligtvis Player SDK chefer standard ljud Spåra markering och tillgängliga ljud spårar per användarens val. Det är svårt att utveckla sådana Player-SDK: er, kräver olika implementeringar över enhetsspecifika spelarramverk. Dessutom begränsas Player-API: er på vissa plattformar och inkluderar inte ljud markeringen funktion där användare inte kan välja eller ändra standard ljud spåra. Du kan styra beteendet genom att skapa filter som bara innehåller önskade ljud språk med tillgångsinformation filter.

![Språk spårar filtrering][language_filter]

## <a name="trimming-start-of-an-asset"></a>Trimning start för en tillgång
I de flesta händelser via strömning live kör ansvariga för några test innan den faktiska händelsen. De kan till exempel inkludera en mallen så här före händelsen: ”programmet börjar tillfälligt”. Om programmet arkivering test- och en data också arkiveras och kommer att ingå i presentationen. Den här informationen ska inte visas för klienterna. Du kan skapa ett tidsfilter för start och ta bort oönskade data från manifestet med dynamiska Manifest.

![Trimning start][trim_filter]

## <a name="creating-sub-clips-views-from-a-live-archive"></a>Skapa underordnade klipp (vyer) från en live-Arkiv
Många direktsända händelser är tidskrävande och live Arkiv kan innehålla flera händelser. När direktsänd händelse kanske ends programföretag vill dela upp live arkivet till logiska programmet startas och stoppa sekvenser. Sedan kan publicera separat dessa virtuella program utan efter bearbetning live arkivet och inte skapa separata resurser (som inte kommer kunna utnyttja befintliga cachelagrade fragment i de andra CDN-lösningar). Exempel på sådana virtuella program (underordnade klipp) är kvartal i en football eller basketmatch, innings i baseball eller enskilda händelser i en här olympiad program.

Du kan skapa filter med start-och sluttider och skapa virtuella vyer över dina live Arkiv med dynamiska Manifest. 

![Underklipp filter][subclip_filter]

Filtrerade tillgångsinformation:

![Skidåkning][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Justera presentationsfönstret (DVR)
För närvarande erbjuder Azure Media Services cirkulär Arkiv där varaktigheten kan konfigureras mellan 5 minuter – 25 timmar. Manifestet filtrering kan användas för att skapa en rullande fönster för DVR över arkivet, utan att ta bort mediet. Det finns många scenarier där programföretag vill ge ett begränsat DVR fönster som flyttar med levande kant och samtidigt behålla en större arkivering fönster. En broadcast kanske vill använda de data som ligger utanför fönstret DVR om du vill markera klipp eller he\she kanske vill ge olika DVR fönster för olika enheter. De flesta av de mobila enheterna hanteras exempelvis inte stort DVR windows (du kan ha ett två minuter DVR fönster för mobila enheter och 1 timme för skrivbordsklienter).

![DVR fönster][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Justera LiveBackoff (direktsända positionen)
Manifestet filtrering kan användas för att ta bort några sekunder från en direktsänd program live kant. Detta gör att programföretag titta på presentationen preview publikationen pekar på och skapa annonsen infogas punkter innan visningsprogram får den dataström (vanligtvis backas upp av med 30 sekunder). -Kan sedan push dessa annonser till deras klient-ramverk i tid för dem att mottagna och processinformation innan annons affärsmöjligheten.

LiveBackoff kan användas för att justera klienten live download position så att när klienter driva och träffar live kanten kan de fortfarande få fragment från servern i stället för att hämta HTTP-fel 404 eller 412 förutom annons-stöd.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinera flera regler i ett enda filter
Du kan kombinera flera filtreringsregler i ett enda filter. Som exempel kan du definiera en regel för intervallet för att ta bort mallen från en live-Arkiv och även filtrera tillgänglig bithastighet. Slutresultatet är sammansättning (skärningspunkten) av reglerna för flera filtreringsregler.

![flera regler][multiple-rules]

## <a name="create-filters-programmatically"></a>Skapa filter programmässigt
I följande avsnitt beskrivs Media Services-entiteter som är relaterade till filter. Avsnittet visar även hur du programmässigt skapa filter.  

[Skapa filter med REST API: er](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinera flera filter (filter sammansättning)
Du kan också kombinera flera filter i en enskild URL. 

Följande scenario visar varför du kanske vill kombinera filter:

1. Du vill filtrera din video egenskaper för mobila enheter, till exempel Android eller iPAD (för att begränsa video egenskaper). Om du vill ta bort oönskade egenskaper, skulle du skapa ett globalt filter som är lämplig för profiler för enheter. Som nämnts ovan är kan globala filter användas för alla tillgångar under samma media services-konto utan ytterligare association. 
2. Du bör också trim start- och tiden för en tillgång. För att uppnå detta skulle du skapa ett lokalt filter och ange starttid/sluttid. 
3. Du vill kombinera båda dessa filter (utan kombination måste du lägga till kvalitet filtrering trimning-filtret som gör filter användning svårt).

Om du vill kombinera filter, måste du ange filternamn till manifest/spelningslista URL: en med semikolonavgränsad. Antar vi att du har ett filter med namnet *MyMobileDevice* som filtrerar egenskaper och du har en annan med namnet *MyStartTime* för att ange en specifik starttid. Du kan kombinera dem så här:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Du kan kombinera upp till 3 filter. 

Mer information finns i [detta](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogg.

## <a name="know-issues-and-limitations"></a>Information om problem och begränsningar
* Dynamisk manifestet fungerar i GOP gränser (nyckeln ramar) därför trimning har GOP noggrannhet. 
* Du kan använda samma filnamn för lokala och globala filter. Observera den lokala filtret har högre prioritet och åsidosätter globala filter.
* Om du uppdaterar ett filter kan ta det upp till 2 minuter för strömmande slutpunkten att uppdatera reglerna. Om innehållet behandlades använda filter (och cachelagras i proxyservrar och CDN cacheminnen) kan uppdaterar dessa filter resultera i player-fel. Det rekommenderas att rensa cachen när du har uppdaterat filtret. Om det här alternativet inte är möjligt bör du använda ett annat filter.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Leverera innehåll till kunder översikt](media-services-deliver-content-overview.md)

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
