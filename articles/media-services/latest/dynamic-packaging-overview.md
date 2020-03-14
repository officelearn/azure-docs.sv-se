---
title: Dynamisk paketering i Azure Media Services v3
titleSuffix: Azure Media Services
description: Den här artikeln ger en översikt över dynamisk paketering i Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: d408a862c18038f64b816bb54fc235d1b9d84179
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240382"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dynamisk paketering i Media Services v3

Microsoft Azure Media Services kan användas för att koda många fil format för medie källan. Den skickar dem via olika strömnings protokoll, med eller utan innehålls skydd, för att komma åt alla större enheter (t. ex. iOS-och Android-enheter). Dessa klienter förstår olika protokoll. Till exempel kräver iOS att strömmar levereras i HTTP Live Streaming-format (HLS) och Android-enheter stöder HLS samt MPEG-streck.

I Media Services representerar en [strömmande slut punkt](streaming-endpoint-concept.md) en dynamisk (just-in-Time)-paketering och ursprungs tjänst som kan leverera Live och innehåll på begäran direkt till en app i klient spelaren. Det använder ett av de vanliga protokollen för strömmande media som anges i följande avsnitt. Dynamisk paketering är en funktion som finns som standard på alla slutpunkter för direktuppspelning (Standard eller Premium).

> [!NOTE]
> Du kan använda [Azure Portal](https://portal.azure.com/) för att hantera v3 [Live-händelser](live-events-outputs-concept.md), Visa v3- [till gångar](assets-concept.md), hämta information om åtkomst till API: er. För alla andra hanterings uppgifter (t. ex. transformationer och jobb) använder du [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK](media-services-apis-overview.md#sdks): er som stöds.

## <a name="a-iddelivery-protocolsto-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>att förbereda dina källfiler för leverans

Om du vill dra nytta av dynamisk paketering måste du [koda](encoding-concept.md) din mezzaninfil (källa) till en uppsättning MP4-filer med flera bit hastigheter (ISO Base 14496-12). Du måste ha en [till gång](assets-concept.md) med de KODAde MP4-och streaming-konfigurationsfiler som krävs för Media Services dynamisk paketering. Från den här uppsättningen MP4-filer kan du använda dynamisk paketering för att leverera video via protokollen för strömnings medier som beskrivs nedan.

> [!TIP]
> Ett sätt att hämta filerna för MP4 och strömmande konfiguration är att [koda din mezzaninfil-fil med Media Services](#encode-to-adaptive-bitrate-mp4s). 

Om du vill göra videor i kodad till gång tillgängliga för klienter för uppspelning måste du skapa en [strömmande lokaliserare](streaming-locators-concept.md) och bygga direkt uppspelnings-URL: er. Sedan, baserat på det angivna formatet i klient manifestet för strömning (HLS, MPEG-datastreck eller Smooth Streaming), får du data strömmen i det protokoll som du har valt.

Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient.

Om du planerar att skydda ditt innehåll med hjälp av Media Services dynamisk kryptering, se [strömmande protokoll och krypterings typer](content-protection-overview.md#streaming-protocols-and-encryption-types).

### <a name="hls-protocol"></a>HLS-protokoll

Den strömmande klienten kan ange följande HLS-format:

|Protokoll|Exempel|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

### <a name="mpeg-dash-protocol"></a>MPEG-streck-protokoll

Den strömmande klienten kan ange följande MPEG-streck-format:

|Protokoll|Exempel|
|---|---|
|MPEG-STRECK – CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Smooth Streaming protokoll

Den strömmande klienten kan ange följande Smooth Streaming Format:

|Protokoll|Anteckningar/exempel| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Smooth Streaming 2,0 (bakåtkompatibelt manifest)|Som standard innehåller Smooth Streaming manifest formatet REPEAT-taggen (r-tag). Vissa spelare har dock inte stöd för `r-tag`. Klienter med dessa spelare kan använda ett format som inaktiverar r-taggen:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

## <a name="on-demand-streaming-workflow"></a>Arbets flöde för strömning på begäran

Följande steg visar ett vanligt Media Services strömnings arbets flöde där dynamisk paketering används tillsammans med standard kodare i Azure Media Services.

1. Ladda upp en indatafil, till exempel en QuickTime-/MOV-eller MXF-fil. Den här filen kallas även för mezzaninfil eller käll filen. En lista över format som stöds finns i [format som stöds av Media Encoder Standard](media-encoder-standard-formats.md).
1. [Koda](#encode-to-adaptive-bitrate-mp4s) din mezzaninfil-fil till en inställd H. 264/AAC MP4 anpassad bit hastighet.
1. Publicera den utgående till gången som innehåller MP4-uppsättningen med anpassad bit hastighet. Du publicerar genom att skapa en strömmande Locator.
1. Bygg webb adresser som riktar sig mot olika format (HLS, MPEG-streck och Smooth Streaming). **Slut punkten för direkt uppspelningen** tar hand om att betjäna rätt manifest och begär Anden för alla dessa olika format.

I följande diagram visas strömning på begäran med det dynamiska arbets flödet för paketering.

![Diagram över ett arbets flöde för strömning på begäran med dynamisk paketering](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Koda till hastigheter för anpassad bit hastighet

Följande artiklar innehåller exempel på [hur du kodar en video med Media Services](encoding-concept.md):

* [Koda från en HTTPS-URL med hjälp av inbyggda för inställningar](job-input-from-http-how-to.md).
* [Koda en lokal fil med hjälp av inbyggda för hands inställningar](job-input-from-local-file-how-to.md).
* [Bygg en anpassad för inställning för att rikta in dig på specifika scenarier eller enhets krav](customize-encoder-presets-how-to.md).

Se listan med Media Encoder Standard [format och codec](media-encoder-standard-formats.md).

## <a name="live-streaming-workflow"></a>Live streaming-arbetsflöde

En Live-händelse kan ställas in till antingen en *direkt* uppspelning (en lokal Live-kodare som skickar en data ström med flera bit hastigheter) eller *direktsänd kodning* (en lokal Live-kodare skickar en data ström med en bit hastighet). 

Här är ett vanligt arbets flöde för direktsänd strömning med dynamisk paketering:

1. Skapa en [Live-händelse](live-events-outputs-concept.md).
1. Hämta hämtnings-URL: en och konfigurera din lokala kodare för att använda URL: en för att skicka bidrags flödet.
1. Hämta förhands gransknings-URL och Använd den för att kontrol lera att inmatarna tas emot från kodaren.
1. Skapa en ny till gång.
1. Skapa en Live-utdata och Använd namnet på den till gång som du skapade.<br />Live-utdata arkiverar data strömmen till till gången.
1. Skapa en strömmande lokaliserare med de inbyggda typer av strömmande principer.<br />Om du vill kryptera ditt innehåll granskar du [innehålls skydds översikten](content-protection-overview.md).
1. Visa en lista över Sök vägarna för den strömmande lokaliseraren för att hämta URL: er som ska användas.
1. Hämta värd namnet för den strömnings slut punkt som du vill strömma från.
1. Bygg webb adresser som riktar sig mot olika format (HLS, MPEG-streck och Smooth Streaming). Slut punkten för direkt uppspelningen tar hand om att betjäna rätt manifest och begär Anden för de olika formaten.

Det här diagrammet visar arbets flödet för direktsänd strömning med dynamisk paketering:

![Diagram över ett arbets flöde för direkt kodning med dynamisk paketering](./media/live-streaming/pass-through.svg)

Information om Direktsänd strömning i Media Services v3 finns i [Översikt över direktsänd strömning](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Video-codec som stöds av dynamisk paketering

Dynamisk paketering stöder MP4-filer som innehåller video som är kodad med [H. 264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC eller avc1) eller [H. 265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (hevc, hev1 eller hvc1).

> [!NOTE]
> Lösningar på upp till 4K och bild Rute hastigheter på upp till 60 bild rutor per sekund har testats med dynamisk paketering. [Premium-kodaren](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) stöder kodning till H. 265 via äldre v2-API: er.

## <a name="a-idaudio-codecsaudio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>ljud-codec som stöds av dynamisk paketering

Dynamisk paketering stöder ljud som är kodat med följande protokoll:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 eller HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (utökad AC-3 eller E-AC3)
* Dolby Atmos<br />
   Strömmande Dolby Atmos-innehåll stöds för standarder som MPEG-streck-protokollet med antingen common streaming format (CSF) eller common Media Application format (CMAF), fragmenterade MP4 och via HTTP Live Streaming (HLS) med CMAF.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DTS-codecenheter som stöds av streck-CSF, streck-CMAF, HLS-M2TS och HLS-CMAF paket format är:  

    * DTS, Digital Surround (dtsc)
    * DTS – HD hög upplösning och DTS-HD – huvud ljud (dtsh)
    * DTS Express (dtse)
    * DTS-HD-förstörande (ingen kärna) (DTSL)

Dynamisk paketering stöder flera ljud spår med bindestreck eller HLS (version 4 eller senare) för strömnings till gångar som har flera ljud spår med flera codecenheter och språk.

### <a name="additional-notes"></a>Ytterligare information

Dynamisk paketering stöder inte filer som innehåller [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) -ljud (AC3) (det är en äldre codec).

> [!NOTE]
> [Premium-kodaren](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) stöder kodning till Dolby Digital Plus via äldre v2-API: er.

## <a name="manifests"></a>Manifest

I Media Services dynamisk paketering genereras direkt uppspelnings klientens manifest för HLS, MPEG-streck och Smooth Streaming dynamiskt baserat på format väljaren i URL: en.  

En manifest fil innehåller strömmande metadata som spår typ (ljud, video eller text), spår namn, start-och slut tid, bit hastighet (kvalitet), spåra språk, presentations fönster (glidande fönster med fast varaktighet) och video-codec (FourCC). Det instruerar också spelaren att hämta nästa fragment genom att ange information om nästa uppspelnings bara videofragment som är tillgängliga och var de befinner sig. Fragment (eller segment) är de faktiska "segmenten" av video innehåll.

### <a name="examples"></a>Exempel

#### <a name="hls"></a>HLS

Här är ett exempel på en HLS manifest fil, även kallat en HLS Master-lista: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

Här är ett exempel på en manifest fil för MPEG-streck, även kallat en beskrivning av en MPEG-STRECKs medie presentation (MPD):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Smooth Streaming

Här är ett exempel på en Smooth Streaming manifest fil:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>Namn på spår i manifestet

Om ett ljud spårs namn anges i. ISM-filen, Media Services lägger till ett `Label`-element i en `AdaptationSet` för att ange textural-information för det specifika ljud spåret. Ett exempel på utmatnings streck manifestet:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Spelaren kan använda `Label`-elementet för att visa i sitt användar gränssnitt.

### <a name="signaling-audio-description-tracks"></a>Signalerar ljud beskrivnings spår

Du kan lägga till en berättarröst i videon för att hjälpa visuellt avbildade klienter att följa videoinspelningen genom att lyssna på berättarrösten. Du måste kommentera ett ljud spår som ljud beskrivning i manifestet. Det gör du genom att lägga till parametrarna "hjälpmedel" och "roll" i. ISM-filen. Det är ditt ansvar att ange dessa parametrar korrekt för att signalera ljud spår som ljud beskrivning. Lägg till exempel till `<param name="accessibility" value="description" />` och `<param name="role" value="alternate"` till. ISM-filen för ett särskilt ljud spår. 

Mer information finns i exempel på [hur du signalerar ett beskrivande ljud spår](signal-descriptive-audio-howto.md) .

#### <a name="smooth-streaming-manifest"></a>Smooth Streaming manifest

Om du spelar upp en Smooth Streaming data ström skulle manifestet ha värden i `Accessibility` och `Role` attribut för det ljud spåret. `Role="alternate" Accessibility="description"` skulle till exempel läggas till i `StreamIndex`-elementet för att ange att det är en ljud beskrivning.

#### <a name="dash-manifest"></a>STRECK manifest

För streck manifestet skulle följande två element läggas till för att signalera ljud beskrivningen:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS-spelnings lista

För HLS v7 och högre `(format=m3u8-cmaf)`skulle spelnings listan medföra `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` när ljud beskrivnings spåret signaleras.

#### <a name="example"></a>Exempel

Mer information finns i [så här signalerar du ljud beskrivnings spår](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Dynamiskt manifest

Om du vill kontrol lera antalet spår, format, bit hastigheter och presentations tids fönster som skickas till spelare kan du använda dynamisk filtrering med Media Services dynamiska Paketeraren. Mer information finns i [för hands filtrerings manifest med den dynamiska Paketeraren](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Dynamisk kryptering

Du kan använda *dynamisk kryptering* för att dynamiskt kryptera din direktsända eller på begäran-innehåll med AES-128 eller någon av de tre större Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine och Apple Fairplay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser till auktoriserade klienter. Mer information finns i [dynamisk kryptering](content-protection-overview.md).

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="more-information"></a>Mer information

Kolla [Azure Media Services community](media-services-community.md) för att se olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna ett support ärende genom att gå till [nytt support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

[Ladda upp, koda och strömma videor](stream-files-tutorial-with-api.md)
