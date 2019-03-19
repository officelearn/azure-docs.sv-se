---
title: Azure Media Services dynamisk paketering översikt | Microsoft Docs
description: Avsnittet ger en översikt över dynamisk paketering i Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: juliako
ms.openlocfilehash: d9c59bdb2e8a7b115761554f70ebedeecaf5d04e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901753"
---
# <a name="dynamic-packaging"></a>Dynamisk paketering

Microsoft Azure medietjänster kan användas för att leverera många källa mediefilformat, format, för direktuppspelning och innehållsskydd formaterar till en mängd olika tekniker för klient (till exempel iOS och XBOX). Dessa klienter förstå olika protokoll, till exempel iOS kräver en HTTP Live Streaming (HLS)-format och Xbox kräver Smooth Streaming. Om du har en uppsättning med anpassningsbar bithastighet (multibithastighet) MP4 (ISO Base 14496-12) mediefiler eller en uppsättning med anpassningsbar bithastighet Smooth Streaming-filer som du vill ska fungera för klienter som förstå HLS, MPEG DASH och Smooth Streaming, kan du dra nytta av dynamisk Paketering. Förpackningen är oberoende av video lösning, SD/HD/UHD - 4K stöds.

[Slutpunkter för direktuppspelning](streaming-endpoint-concept.md) är tjänsten dynamisk paketering i Media Services som används för att leverera medieinnehåll till klienten spelare. Dynamisk paketering är en funktion som levereras som standard på alla **Strömningsslutpunkter** (Standard eller Premium). Det tillkommer ingen extra kostnad som hör till den här funktionen i Media Services v3. 

Att dra nytta av **dynamisk paketering**, måste du ha en **tillgången** med en uppsättning MP4-filer och strömmande konfigurationsfiler som krävs av Media Services dynamisk paketering. Ett sätt att hämta filerna är att koda din mezzaninfil (källa) med Media Services. Om du vill göra videor i den kodade tillgången tillgängliga för klienter för uppspelning, måste du skapa en **Strömningspositionerare** och skapa strömmande URL: er. Sedan, baserat på formatet som anges i klienten strömmande manifestet (HLS, DASH eller Smooth), du får dataströmmen i protokollet som du har valt.

Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient. 

I Media Services används dynamisk paketering om du strömning live eller på begäran. Följande diagram visar strömning på begäran med dynamisk paketering arbetsflöde.

![Dynamisk paketering](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Vanligt arbetsflöde för video på begäran

Följande är en gemensam Media Services streaming arbetsflöde där dynamisk paketering används.

1. Ladda upp en indatafil (kallas en mezzaninfil). Till exempel H.264 MP4 eller WMV (lista över de format som stöds finns i [format som stöds av Media Encoder Standard](media-encoder-standard-formats.md).
2. Koda din mezzaninfil för H.264 MP4 med anpassningsbar bithastighet uppsättningar.
3. Publicera tillgången med anpassningsbar bithastighet MP4-uppsättningen. Du publicerar genom att skapa en **Strömningspositionerare**.
4. Skapa URL: er som är riktade till olika format (HLS, Dash och Smooth Streaming). Den **Strömningsslutpunkt** skulle ta hand om betjänar rätt manifestet och begäranden för dessa olika format.

## <a name="encode-to-adaptive-bitrate-mp4s"></a>Koda till med anpassningsbar bithastighet MP4s

Information om [att koda ett videoklipp med Media Services](encoding-concept.md), se följande exempel:

* [Koda från en HTTPS-URL med hjälp av inbyggda förinställningar](job-input-from-http-how-to.md)
* [Koda en lokal fil med hjälp av inbyggda förinställningar](job-input-from-local-file-how-to.md)
* [Skapa en anpassad förinställning om du vill rikta in dina specifika krav för scenario eller enhet](customize-encoder-presets-how-to.md)

En lista över Media Encoder Standard format och -codec finns i [format och -codec](media-encoder-standard-formats.md)

## <a name="delivery-protocols"></a>Leveransprotokoll

|Protokoll|Exempel|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codec för indatavideo stöds av dynamisk paketering

Dynamisk paketering stöder MP4-filer som innehåller video kodad med [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC eller AVC1) [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (– HEVC, hev1 eller hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Ljud-codec som stöds av dynamisk paketering

Dynamisk paketering stöder MP4-filer som innehåller ljud kodad med [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (förbättrad AC-3 eller E-AC3) eller [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) DTS ( Express, DTS LBR, DTS HD, DTS HD förlustfri).

> [!NOTE]
> Dynamisk paketering har inte stöd för filer som innehåller [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) ljud (det är en äldre codec).

## <a name="manifests"></a>Manifest 
 
Media Services stöder HLS, MPEG DASH, Smooth Streaming-protokoll. Som en del av **dynamisk paketering**, strömmande klientmanifesten (HLS Master Playlist, DASH Media Presentation beskrivning (MPD) och Smooth Streaming) genereras dynamiskt baserat på väljaren format i URL: en. Se leveransprotokoll i [i det här avsnittet](#delivery-protocols). 

En manifestfil som innehåller metadata för direktuppspelning till exempel: spåra typ (ljud, video eller text), spåra namn, start-och sluttid, med flera bithastigheter (Egenskaper), spåra språk, presentationsfönster (Hoppande fönster med fast varaktighet), video-codec (FourCC). Det instruerar också spelaren att hämta nästa fragment genom att tillhandahålla information om nästa kan spelas upp video fragmenten tillgängliga och deras plats. Fragment (eller segment) är de faktiska ”mängder” en videoinnehåll.

### <a name="hls-master-playlist"></a>HLS Master spellista

Här är ett exempel på en HLS-manifestfilen: 

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

### <a name="dash-media-presentation-description-mpd"></a>DASH Media presentationsbeskrivning (MPD)

Här är ett exempel på ett DASH-manifest:

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
### <a name="smooth-streaming"></a>Smooth Streaming

Här är ett exempel på ett Smooth Streaming-manifest:

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
## <a name="next-steps"></a>Nästa steg

[Överför, koda, strömma videor](stream-files-tutorial-with-api.md)

