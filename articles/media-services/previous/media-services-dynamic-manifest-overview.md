---
title: Filter och dynamiska manifest | Microsoft-dokument
description: I det här avsnittet beskrivs hur du skapar filter så att klienten kan använda dem för att strömma specifika avsnitt i en ström. Media Services skapar dynamiska manifest för att arkivera den här selektiva direktuppspelningen.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 1234263fa800a17d0a5c235df54ca2751e3094bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69015853"
---
# <a name="filters-and-dynamic-manifests"></a>Filter och dynamiska manifest

> [!div class="op_single_selector" title1="Välj den version av Media Services som du använder:"]
> * [Version 2](media-services-dynamic-manifest-overview.md)
> * [Version 3](../latest/filters-dynamic-manifest-overview.md)

Från och med 2.17-versionen kan du med Media Services definiera filter för dina tillgångar. Dessa filter är regler på serversidan som gör det möjligt för kunderna att välja att göra saker som: spela upp endast en del av en video (i stället för att spela upp hela videon) eller bara ange en delmängd av ljud- och videoåtergivningar som kundens enhet kan hantera ( i stället för alla återgivningar som är associerade med tillgången). Den här filtrningen av dina tillgångar uppnås genom **dynamiska manifest**som skapas på kundens begäran om att strömma en video baserat på angivna filter.

I det här avsnittet beskrivs vanliga scenarier där det skulle vara fördelaktigt för kunderna att använda filter och länkar till ämnen som visar hur du skapar filter programmässigt.

## <a name="overview"></a>Översikt
När du levererar ditt innehåll till kunder (strömmande livehändelser eller video-on-demand) är ditt mål att leverera en video av hög kvalitet till olika enheter under olika nätverksförhållanden. För att uppnå detta mål gör du följande:

* koda din ström till multi-bitrate[(adaptiv bitrate)](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)videoström (detta tar hand om kvalitet och nätverksförhållanden) och 
* använd Dynamic [Packaging för](media-services-dynamic-packaging-overview.md) mediatjänster för att dynamiskt paketera om flödet till olika protokoll (detta tar hand om direktuppspelning på olika enheter). Media Services stöder leverans av följande adaptiva bithastighetsstreamingtekniker: HTTP Live Streaming (HLS), Smooth Streaming och MPEG DASH. 

### <a name="manifest-files"></a>Manifestfiler
När du kodar en tillgång för adaptiv bitrate streaming skapas en **manifestfil** (spellista) (filen är textbaserad eller XML-baserad). **Manifestfilen** innehåller strömmande metadata som: spårtyp (ljud, video eller text), spårnamn, start- och sluttid, bithastighet (kvaliteter), spårspråk, presentationsfönster (skjutfönster med fast varaktighet), videocodec (FourCC). Det instruerar också spelaren att hämta nästa fragment genom att ge information om nästa spelbara videofragment tillgängliga och deras plats. Fragment (eller segment) är de faktiska "segmenten" i ett videoinnehåll.

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
Det finns [scenarier](media-services-dynamic-manifest-overview.md#scenarios) när klienten behöver mer flexibilitet än vad som beskrivs i standardtillgångens manifestfil. Ett exempel:

* Enhetsspecifika: leverera endast angivna återgivningar och/eller angivna språkspår som stöds av enheten som används för att spela upp innehållet ("återgivningsfiltrering"). 
* Minska manifestet för att visa ett underklipp av en livehändelse ("sub-clip-filtrering").
* Trimma början på en video ("trimma en video").
* Justera presentationsfönstret (DVR) för att ge en begränsad längd på DVR-fönstret i spelaren ("justera presentationsfönstret").

För att uppnå denna flexibilitet erbjuder Media Services **dynamiska manifest** baserade på fördefinierade [filter](media-services-dynamic-manifest-overview.md#filters).  När du har definierat filtren kan klienterna använda dem för att strömma en viss återgivning eller underklipp av videon. De anger filter i url:en för direktuppspelning. Filter kan användas för adaptiv bithastighetsstreamingprotokoll som stöds av [Dynamisk förpackning:](media-services-dynamic-packaging-overview.md)HLS, MPEG-DASH och Smooth Streaming. Ett exempel:

MPEG DASH-URL med filter

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Url för jämn direktuppspelning med filter

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Mer information om hur du levererar innehåll och skapar webbadresser för direktuppspelning finns i [Leverera innehållsöversikt](media-services-deliver-content-overview.md).

> [!NOTE]
> Observera att dynamiska manifest inte ändrar tillgången och standardmanifestet för den tillgången. Din klient kan välja att begära en ström med eller utan filter. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Filter
Det finns två typer av tillgångsfilter: 

* Globala filter (kan tillämpas på alla tillgångar i Azure Media Services-kontot, har en livstid för kontot) och 
* Lokala filter (kan endast tillämpas på en tillgång som filtret var associerat när det skapades, har en livstid för tillgången). 

Globala och lokala filtertyper har exakt samma egenskaper. Den största skillnaden mellan de två är för vilka scenarier vilken typ av en filer är mer lämplig. Globala filter är i allmänhet lämpliga för enhetsprofiler (återgivningsfiltrering) där lokala filter kan användas för att trimma en viss tillgång.

## <a name="common-scenarios"></a><a id="scenarios"></a>Vanliga scenarier
Som tidigare nämnts, när du levererar ditt innehåll till kunder (strömma live-händelser eller video-on-demand) är ditt mål att leverera en högkvalitativ video till olika enheter under olika nätverksförhållanden. Dessutom kan du ha andra krav som innebär att filtrera dina tillgångar och använda **dynamiska manifest.** I följande avsnitt ges en kort översikt över olika filtreringsscenarier.

* Ange bara en delmängd av ljud- och videoåtergivningar som vissa enheter kan hantera (i stället för alla återgivningar som är associerade med tillgången). 
* Spelar upp endast en del av en video (i stället för att spela upp hela videon).
* Justera DVR-presentationsfönstret.

## <a name="rendition-filtering"></a>Filtrering av återgivning
Du kan välja att koda din tillgång till flera kodningsprofiler (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) och flera kvalitetsbithastigheter. Alla klientenheter stöder dock inte alla dina tillgångs profiler och bithastigheter. Äldre Android-enheter stöder till exempel bara H.264 Baseline+AACL. Skicka högre bithastigheter till en enhet som inte kan få fördelar, avfall bandbredd och enhet beräkning. En sådan anordning måste avkoda all viss information, bara för att skala ner den för visning.

Med Dynamic Manifest kan du skapa enhetsprofiler som mobil, konsol, HD/SD, etc. och inkludera de spår och kvaliteter som du vill vara en del av varje profil.

![Exempel på återgivningsfiltrering][renditions2]

I följande exempel användes en kodare för att koda en mezzanintillgång i sju ISO MP4-videoåtergivningar (från 180p till 1080p). Den kodade tillgången kan paketeras dynamiskt i något av följande direktuppspelningsprotokoll: HLS, Smooth och MPEG DASH.  Högst upp i diagrammet visas HLS-manifestet för tillgången utan filter (det innehåller alla sju återgivningar).  Längst ned till vänster visas HLS-manifestet som ett filter med namnet "ott" tillämpades på. Filtret "ott" anger att alla bithastigheter under 1 Mbps ska tas bort, vilket resulterade i att de två lägsta kvalitetsnivåerna togs bort i svaret. Längst ned till höger visas HLS-manifestet som ett filter med namnet "mobil" har tillämpats på. Filtret "mobil" anger att återgivningar ska tas bort där upplösningen är större än 720p, vilket resulterade i att de två 1080p-återgivningarna togs bort.

![Filtrering av återgivning][renditions1]

## <a name="removing-language-tracks"></a>Ta bort språkspår
Dina tillgångar kan innehålla flera ljudspråk som engelska, spanska, franska, etc. Vanligtvis är det standardval av ljudspår för Player SDK och tillgängliga ljudspår per användarval. Det är en utmaning att utveckla sådana Player SDK:er, det kräver olika implementeringar över enhetsspecifika spelarramverk. På vissa plattformar är spelar-API:er begränsade och inkluderar inte ljudvalsfunktionen där användarna inte kan välja eller ändra standardljudspåret. Med tillgångsfilter kan du styra beteendet genom att skapa filter som bara innehåller önskade ljudspråk.

![Filtrering av språkspår][language_filter]

## <a name="trimming-start-of-an-asset"></a>Trimma början av en tillgång
I de flesta direktuppspelningshändelser kör operatörerna några tester före den faktiska händelsen. De kan till exempel innehålla en griffeltavla som denna innan evenemanget börjar: "Programmet börjar tillfälligt". Om programmet arkiveras arkiveras och inkluderas även test- och skifferdata i presentationen. Denna information bör dock inte visas för klienterna. Med Dynamiskt manifest kan du skapa ett starttidsfilter och ta bort oönskade data från manifestet.

![Trimma start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Skapa underknurer (vyer) från ett livearkiv
Många livehändelser är tidskrävande och live-arkiv kan innehålla flera händelser. När liveevenemanget har avslutats kanske programföretagen vill dela upp livearkivet i logiska programstart- och stoppsekvenser. Publicera sedan dessa virtuella program separat utan efterbearbetning av livearkivet och inte skapa separata tillgångar (som inte får nytta av de befintliga cachelagrade fragmenten i CDN:erna). Exempel på sådana virtuella program är kvartalen i en fotboll eller basketmatch, innings i baseball, eller enskilda händelser i något sportprogram.

Med Dynamiskt manifest kan du skapa filter med start-/sluttider och skapa virtuella vyer överst i ditt livearkiv. 

![Filtret Subclip][subclip_filter]

Filtrerad tillgång:

![Skidåkning][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Justera presentationsfönstret (DVR)
För närvarande erbjuder Azure Media Services cirkulära arkiv där varaktigheten kan konfigureras mellan 5 minuter och 25 timmar. Manifestfiltrering kan användas för att skapa ett rullande DVR-fönster överarkivets överkant, utan att ta bort media. Det finns många scenarier där programföretag vill tillhandahålla ett begränsat DVR-fönster för att flytta med live kanten och samtidigt hålla ett större arkiveringsfönster. Ett programföretag kanske vill använda data som finns utanför DVR-fönstret för att markera klipp, eller så kanske de vill tillhandahålla olika DVR-fönster för olika enheter. De flesta mobila enheter hanterar till exempel inte stora DVR-fönster (du kan ha ett 2-minuters DVR-fönster för mobila enheter och en timme för stationära klienter).

![DVR-fönster][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Justera LiveBackoff (liveposition)
Manifestfiltrering kan användas för att ta bort flera sekunder från live kanten av ett live-program. Filtrering gör det möjligt för programföretag att titta på presentationen på förhandsgranskningspubliceringspunkten och skapa insättningspunkter för annonser innan tittarna får strömmen (backas upp med 30 sekunder). Programföretag kan sedan driva dessa annonser till sina kundramverk i tid för dem att ta emot och bearbeta informationen innan reklamen tillfälle.

Förutom annonsstöd kan LiveBackoff-inställningen användas för att justera tittarnas position så att de fortfarande kan få fragment från servern i stället för att få ett HTTP 404- eller 412-fel när klienterna driver och träffar livekanten.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinera flera regler i ett enda filter
Du kan kombinera flera filtreringsregler i ett enda filter. Som ett exempel kan du definiera en "intervallregel" för att ta bort griffeltavlor från ett livearkiv och även filtrera bort tillgängliga bithastigheter. När du använder flera filtreringsregler är slutresultatet skärningspunkten mellan alla regler.

![flera regler][multiple-rules]

## <a name="create-filters-programmatically"></a>Skapa filter programmässigt
I följande artikel beskrivs Media Services-entiteter som är relaterade till filter. Artikeln visar också hur man programmässigt skapa filter.  

[Skapa filter med REST-API:er](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinera flera filter (filtersammansättning)
Du kan också kombinera flera filter i en enda WEBBADRESS. 

Följande scenario visar varför du kanske vill kombinera filter:

1. Du måste filtrera dina videokvaliteter för mobila enheter som Android eller iPAD (för att begränsa videokvaliteter). Om du vill ta bort de oönskade egenskaperna skapar du ett globalt filter som passar enhetsprofilerna. Som tidigare nämnts i den här artikeln kan globala filter användas för alla dina tillgångar under samma medietjänstkonto utan någon ytterligare association. 
2. Du vill också trimma start- och sluttiden för en tillgång. För att uppnå detta skapar du ett lokalt filter och ställer in start-/sluttid. 
3. Du vill kombinera båda dessa filter (utan kombination måste du lägga till kvalitetsfiltrering till trimningsfiltret vilket gör filteranvändning svårare).

Om du vill kombinera filter måste du ange filternamnen till manifest-/spelliste-URL:en med semikolonavgränsad. Anta att du har ett filter med namnet *MyMobileDevice* som filtrerar kvaliteter och du har en annan som heter *MyStartTime* för att ställa in en viss starttid. Du kan kombinera dem så här:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Du kan kombinera upp till tre filter. 

Mer information finns i [den här](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) bloggen.

## <a name="know-issues-and-limitations"></a>Känna till problem och begränsningar
* Dynamisk manifest fungerar i GOP gränser (Nyckelramar) därmed trimning har GOP noggrannhet. 
* Du kan använda samma filternamn för lokala och globala filter. Lokala filter har högre prioritet och åsidosätter globala filter.
* Om du uppdaterar ett filter kan det ta upp till 2 minuter innan slutpunkten för direktuppspelning uppdateras. Om innehållet serverades med vissa filter (och cachelagrades i proxyservrar och CDN-cacheminnen) kan uppdatering av dessa filter resultera i spelarfel. Vi rekommenderar att du rensar cacheminnet efter att filtret har uppdaterats. Om det här alternativet inte är möjligt kan du överväga att använda ett annat filter.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över att leverera innehåll till kunder](media-services-deliver-content-overview.md)

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
