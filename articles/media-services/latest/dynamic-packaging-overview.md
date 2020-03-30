---
title: Dynamisk paketering i Azure Media Services v3
titleSuffix: Azure Media Services
description: Den här artikeln innehåller en översikt över dynamiska paketeringar i Azure Media Services.
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
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: ae049d7486007696d8038eb4e6593cf996df659e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372594"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dynamisk förpackning i Media Services v3

Microsoft Azure Media Services kan användas för att koda många mediefilformat. Det levererar dem via olika streamingprotokoll, med eller utan innehållsskydd, för att nå alla större enheter (som iOS- och Android-enheter). Dessa klienter förstår olika protokoll. IOS kräver till exempel att strömmar levereras i HTTP Live Streaming (HLS) och Android-enheter stöder HLS samt MPEG DASH.

I Media Services representerar en slutpunkt för [direktuppspelning](streaming-endpoint-concept.md) en dynamisk (just-in-time)-förpackning och ursprungstjänst som kan leverera ditt live- och on-demand-innehåll direkt till en klientspelarapp. Den använder en av de vanliga protokoll för direktuppspelningsmedia som nämns i följande avsnitt. Dynamisk paketering är en funktion som finns som standard på alla slutpunkter för direktuppspelning (Standard eller Premium).

> [!NOTE]
> Du kan använda [Azure-portalen](https://portal.azure.com/) för att hantera v3 [Live Events,](live-events-outputs-concept.md)visa v3-tillgångar och få information om hur du använder API:er. [Assets](assets-concept.md) För alla andra hanteringsuppgifter (till exempel Transformeringar och jobb) använder [du REST API](https://docs.microsoft.com/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK:er som](media-services-apis-overview.md#sdks)stöds .

## <a name="to-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>Så här förbereder du källfilerna för leverans

För att dra nytta av Dynamisk Förpackning måste du [koda](encoding-concept.md) din mezzanine (källa) fil i en uppsättning av flera bitrate MP4 (ISO Base Media 14496-12) filer. Du måste ha en [tillgång](assets-concept.md) med de kodade MP4- och direktuppspelningskonfigurationsfilerna som behövs av Media Services Dynamic Packaging. Från den här uppsättningen MP4-filer kan du använda Dynamisk paketering för att leverera video via de protokoll för direktuppspelade media som beskrivs nedan.

> [!TIP]
> Ett sätt att få MP4 och strömmande konfigurationsfiler är att [koda din mezzanine fil med Media Services](#encode-to-adaptive-bitrate-mp4s). 

Om du vill göra videor i den kodade tillgången tillgängliga för klienter för uppspelning måste du skapa en [streaming locator](streaming-locators-concept.md) och skapa strömmande webbadresser. Baserat på det angivna formatet i hls-, MPEG DASH- eller Smooth Streaming-programmet får du sedan strömmen i det protokoll du har valt.

Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient.

Om du planerar att skydda ditt innehåll med hjälp av dynamisk kryptering av Media Services läser du [Direktuppspelningsprotokoll och krypteringstyper](content-protection-overview.md#streaming-protocols-and-encryption-types).

### <a name="hls-protocol"></a>HLS-protokoll

Din streamingklient kan ange följande HLS-format:

|Protokoll|Exempel|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

### <a name="mpeg-dash-protocol"></a>MPEG-DASH-protokoll

Din direktuppspelningsklient kan ange följande MPEG-DASH-format:

|Protokoll|Exempel|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Smidigt protokoll för direktuppspelning

Din streamingklient kan ange följande utjämnade direktuppspelningsformat:

|Protokoll|Anteckningar/exempel| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Smooth Streaming 2.0 (äldre manifest)|Som standard innehåller smooth streaming manifestformatet upprepa taggen (r-tag). Vissa spelare stöder dock `r-tag`inte . Klienter med dessa spelare kan använda ett format som inaktiverar r-taggen:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Smooth Streaming kräver att både ljud och video ska finnas i din ström.

## <a name="on-demand-streaming-workflow"></a>Arbetsflöde för direktuppspelning på begäran

Följande steg visar ett gemensamt arbetsflöde för direktuppspelning av Media Services där dynamisk paketering används tillsammans med standardkodaren i Azure Media Services.

1. Ladda upp en indatafil, till exempel en QuickTime/MOV- eller MXF-fil. Den här filen kallas även mezzanin- eller källfilen. En lista över format som stöds finns i [Format som stöds av standardkodaren](media-encoder-standard-formats.md).
1. [Koda](#encode-to-adaptive-bitrate-mp4s) din mezzanine fil i en H.264/AAC MP4 adaptiv bitrate set.
1. Publicera utdatatillgången som innehåller den adaptiva bithastigheten MP4-uppsättning. Du publicerar genom att skapa en streaming locator.
1. Skapa webbadresser som är inriktade på olika format (HLS, MPEG-DASH och Smooth Streaming). **Slutpunkten för direktuppspelning** skulle ta hand om att visa rätt manifest och begäranden för alla dessa olika format.

Följande diagram visar direktuppspelningen på begäran med arbetsflödet för dynamisk paketering.

![Diagram över ett arbetsflöde för direktuppspelning på begäran med dynamisk paketering](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Koda för adaptiva bithastighet MP4s

Följande artiklar visar exempel på [hur du kodar en video med Media Services:](encoding-concept.md)

* [Koda från en HTTPS-URL med hjälp av inbyggda förinställningar](job-input-from-http-how-to.md).
* [Koda en lokal fil med hjälp av inbyggda förinställningar](job-input-from-local-file-how-to.md).
* [Skapa en anpassad förinställning för att rikta in dig på ditt specifika scenario eller enhetskrav](customize-encoder-presets-how-to.md).

Se listan över [standardkodarformat och codec-enheter](media-encoder-standard-formats.md).

## <a name="live-streaming-workflow"></a>Arbetsflöde för direktuppspelning

En livehändelse kan ställas in på antingen en *genomströmning* (en lokal live-kodare skickar en multibitrate-ström) eller *livekodning* (en lokal live-kodare skickar en enda bitrate-ström). 

Här är ett vanligt arbetsflöde för livestreaming med Dynamisk förpackning:

1. Skapa en [livehändelse](live-events-outputs-concept.md).
1. Hämta den inmatnings-URL:en och konfigurera den lokala kodaren så att den använder URL:en för att skicka bidragsflödet.
1. Hämta förhandsgransknings-URL:en och använd den för att kontrollera att indata från kodaren tas emot.
1. Skapa en ny tillgång.
1. Skapa en live-utdata och använd det tillgångsnamn som du skapade.<br />Live-utdata arkiverar strömmen till tillgången.
1. Skapa en streaming locator med de inbyggda policytyperna för direktuppspelning.<br />Om du tänker kryptera ditt innehåll läser du [översikt över innehållsskydd](content-protection-overview.md).
1. Lista sökvägarna på den strömmande positioneraren för att få webbadresserna att använda.
1. Hämta värdnamnet för den slutpunkt för direktuppspelning som du vill strömma från.
1. Skapa webbadresser som är inriktade på olika format (HLS, MPEG-DASH och Smooth Streaming). Slutpunkten för direktuppspelning tar hand om att visa rätt manifest och begäranden för de olika formaten.

Det här diagrammet visar arbetsflödet för livestreaming med dynamisk paketering:

![Diagram över ett arbetsflöde för genomströmningskodning med dynamisk paketering](./media/live-streaming/pass-through.svg)

Information om livestreaming i Media Services v3 finns i [Översikt över direktuppspelning](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Video codec-enheter som stöds av Dynamic Packaging

Dynamic Packaging stöder MP4-filer som innehåller video som är kodad med [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC eller AVC1) eller [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 eller hvc1).

> [!NOTE]
> Upplösningar på upp till 4K och bildhastigheter på upp till 60 bildrutor/sekund har testats med dynamisk förpackning. [Premium-kodaren](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) stöder kodning till H.265 via de äldre v2-API:erna.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>Ljudcodec-enheter som stöds av Dynamic Packaging

Dynamic Packaging stöder ljud som är kodade med följande protokoll:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 eller HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Förbättrad AC-3 eller E-AC3)
* Dolby Atmos<br />
   Strömmande Dolby Atmos-innehåll stöds för standarder som MPEG-DASH-protokollet med antingen CSF (Common Streaming Format) eller CMAF (Common Media Application Format) och via HTTP Live Streaming (HLS) med CMAF.

* [Dts](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DTS-codec-codec-enheter som stöds av dash-csf-, dash-cmaf-, HLS-M2TS- och HLS-CMAF-förpackningsformat är:  

    * DTS Digital Surround (dtsc)
    * DTS-HD hög upplösning och DTS-HD Master Audio (dtsh)
    * DTS Express (avstängning)
    * DTS-HD Lossless (ingen kärna) (avsl)

Dynamic Packaging stöder flera ljudspår med DASH eller HLS (version 4 eller senare) för strömningstillgångar som har flera ljudspår med flera codec-enheter och språk.

### <a name="additional-notes"></a>Ytterligare information

Dynamic Packaging stöder inte filer som innehåller [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) ljud (det är en äldre codec).

> [!NOTE]
> [Premium-kodaren](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) stöder kodning till Dolby Digital Plus via de äldre v2-API:erna.

## <a name="manifests"></a>Manifesterar

I Dynamic Packaging för Media Services genereras direktuppströmningsklientmanifesten för HLS, MPEG-DASH och Smooth Streaming dynamiskt baserat på formatväljaren i URL:en.  

En manifestfil innehåller strömmande metadata som spårtyp (ljud, video eller text), spårnamn, start- och sluttid, bithastighet (kvaliteter), spårspråk, presentationsfönster (skjutfönster med fast varaktighet) och videocodec (FourCC). Det instruerar också spelaren att hämta nästa fragment genom att ge information om nästa spelbara videofragment som finns tillgängliga och deras plats. Fragment (eller segment) är de faktiska "bitar" av videoinnehåll.

### <a name="examples"></a>Exempel

#### <a name="hls"></a>HLS

Här är ett exempel på en HLS manifestfil, även kallad en HLS-huvudspellista: 

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

#### <a name="mpeg-dash"></a>MPEG-STRECK

Här är ett exempel på en MPEG-DASH manifestfil, även kallad mpeg-dash media presentationsbeskrivning (MPD):

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

Här är ett exempel på en smooth streaming manifestfil:

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

### <a name="naming-of-tracks-in-the-manifest"></a>Namnge spår i manifestet

Om ett ljudspårsnamn anges i ISM-filen lägger Media Services till ett `Label` element i en `AdaptationSet` för att ange textinformation för det specifika ljudspåret. Ett exempel på utdata DASH-manifestet:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Spelaren kan använda `Label` elementet för att visa på sitt användargränssnitt.

### <a name="signaling-audio-description-tracks"></a>Signalering av ljudbeskrivningsspår

Du kan lägga till ett berättarspår i videon så att synskadade klienter följer videoinspelningen genom att lyssna på berättarrösten. Du måste kommentera ett ljudspår som ljudbeskrivning i manifestet. Det gör du genom att lägga till parametrar för "tillgänglighet" och "roll" i .ism-filen. Det är ditt ansvar att ställa in dessa parametrar korrekt för att signalera ett ljudspår som ljudbeskrivning. Lägg till `<param name="accessibility" value="description" />` och `<param name="role" value="alternate"` till exempel till och i ISM-filen för ett visst ljudspår. 

Mer information finns i exemplet [med så här signalerar du ett beskrivande ljudspår.](signal-descriptive-audio-howto.md)

#### <a name="smooth-streaming-manifest"></a>Smooth Streaming manifest

Om du spelar upp en smooth streaming-ström, `Accessibility` `Role` skulle manifestet bära värden i och attribut för det ljudspåret. Till exempel `Role="alternate" Accessibility="description"` skulle läggas `StreamIndex` till i elementet för att ange att det är en ljudbeskrivning.

#### <a name="dash-manifest"></a>DASH-manifest

För DASH-manifestet skulle följande två element läggas till för att signalera ljudbeskrivningen:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS-spellista

För HLS v7 `(format=m3u8-cmaf)`och högre `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` skulle spellistan bära när ljudbeskrivningsspåret signaleras.

#### <a name="example"></a>Exempel

Mer information finns i [Så här signalerar du ljudbeskrivningsspår](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Dynamiskt manifest

Om du vill styra antalet spår, format, bithastigheter och presentationstidsfönster som skickas till spelare kan du använda dynamisk filtrering med den dynamiska paketeraren för Medietjänster. Mer information finns i [Förfiltreringsmanifest med den dynamiska paketeraren](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Dynamisk kryptering

Du kan använda *dynamisk kryptering* för att dynamiskt kryptera ditt live- eller on-demand-innehåll med AES-128 eller något av de tre stora DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser till auktoriserade klienter. Mer information finns i [dynamisk kryptering](content-protection-overview.md).

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="more-information"></a>Mer information

Kolla in [Azure Media Services-communityn](media-services-community.md) för att se olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna en supportbiljett genom att navigera till [Ny supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

[Ladda upp, koda och strömma videor](stream-files-tutorial-with-api.md)
