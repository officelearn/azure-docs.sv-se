---
title: Azure Media Services - signalering tidsgränsen Metadata i direktsänd strömning | Microsoft Docs
description: Den här specifikationen beskrivs två lägen som stöds av Media Services för signaling tidsinställd metadata i direktsänd strömning. Detta inkluderar stöd för generiska tidsinställd metadata signaler, samt SCTE 35 signalering för ad splice infogning.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2018
ms.author: johndeu;
ms.openlocfilehash: 9c8472e74cab779e417e68316a6125d40410ef1c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalering tidsgränsen Metadata i direktsänd strömning


## <a name="1-introduction"></a>1 introduktion 
För att underlätta infogning av annonser eller anpassade händelser på en klient player programföretag ofta kontrollera användningen av tidsinställd metadata är inbäddad i videon. Om du vill aktivera dessa scenarier tillhandahåller Media Services support för transport av tidsinställd metadata tillsammans med media från klientprogrammet direktsänd strömning kanal infognings punkt.
Den här specifikationen beskrivs två lägen som stöds av Media Services för tidsinställd metadata i live strömmande signaler:

1. [SCTE 35] signalering som heeds rekommendationer som anges av [SCTE 67]

2. En allmän tidsinställd metadata signalering läge för meddelanden som inte [SCTE 35]

### <a name="12-conformance-notation"></a>1.2-överensstämmelse Notation
Nyckeln orden ”måste”, är ”får inte”, ”krävs”, ”skall”, ”skall inte”, ”SHOULD”, ”bör inte”, ”rekommenderade”, ”kan” och ”valfritt” i det här dokumentet tolkas som beskrivs i RFC 2119

### <a name="13-terms-used"></a>1.3 termer som används

| Period              | Definition                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Presentation tid | Den tid som en händelse visas som visningsprogram. Tiden representerar ögonblick på media tidslinjen ett visningsprogram ser händelsen. Presentation-tiden för meddelandet SCTE 35 splice_info() kommandot är till exempel splice_time(). |
| Ankomsttid      | Den tid som ett händelsemeddelande anländer. Tiden är vanligtvis separata från presentation tid för händelse, eftersom event-meddelanden skickas förväg presentation av händelsen.                                     |
| Sparse spåra      | Media spåra som inte är kontinuerlig och synkroniseras tid med över- eller -spår.                                                                                                                                    |
| Ursprung            | Tjänsten Azure Media strömning                                                                                                                                                                                                |
| Kanalmottagaren      | Azure Media Live Streaming Service                                                                                                                                                                                           |
| HLS               | Apple HTTP Live Streaming protocol                                                                                                                                                                                               |
| STRECK              | Dynamiska anpassningsbar strömning via HTTP                                                                                                                                                                                             |
| Smooth            | Smooth Streaming Protocol                                                                                                                                                                                                        |
| MPEG2 TS          | MPEG-2 transportströmmar                                                                                                                                                                                                         |
| RTMP              | Realtid Multimedia-protokollet                                                                                                                                                                                                    |
| uimsbf            | -Bitars heltal utan tecken, viktigaste först.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>Infognings-2 tidsinställd Metadata
## <a name="21-rtmp-ingest"></a>2.1 RTMP mata in
RTMP stöder tidsinställd metadata signaler som skickas som AMF stack-meddelanden som är inbäddad i RTMP-dataströmmen. Stack-meddelanden kan skickas en stund innan den faktiska händelsen eller ad splice infogning måste ske. För att stödja det här scenariot, skickas den faktiska tiden splice eller segment i härdning meddelandet. Mer information finns i [AMF0].

I följande tabell beskrivs AMF meddelande-nyttolast som Media Services kommer infognings-format.

Namnet på AMF meddelandet kan användas för att skilja flera händelseströmmar av samma typ.  För [SCTE 35] meddelanden, måste namnet på AMF meddelandet vara ”onAdCue” som rekommenderas i [SCTE 67].  Alla fält som inte anges nedan måste ignoreras så att innovation för den här designen inte är inaktiv i framtiden.

### <a name="signal-syntax"></a>Signal Syntax
För RTMP enkelt läge stöder Media Services en enda AMF stack-meddelande som kallas ”onAdCue” med följande format:

### <a name="simple-mode"></a>Enkelt läge

| Fältnamn | Typ av fält | Krävs? | Beskrivningar                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| stack        | Sträng     | Krävs | Händelsemeddelandet.  Vara bör ”SpliceOut” för att ange ett enkelt läge splice.                                              |
| id         | Sträng     | Krävs | En unik identifierare som beskriver skarv eller segment. Identifierar den här instansen av meddelandet                            |
| Varaktighet   | Tal     | Krävs | Varaktighet för splice. Enheterna är bråkdelssekunder.                                                                |
| elapsed    | Tal     | Valfri | När signalen upprepas för att stödja finjustera skall fältet vara presentation tidsperiod som har förflutit sedan splice påbörjades. Enheterna är bråkdelssekunder. När du använder enkelt läge, får det här värdet inte överskrida splice ursprungliga varaktighet.                                                  |
| time       | Tal     | Krävs | Vara tiden för splice presentation tidpunkt. Enheterna är bråkdelssekunder.                                     |

---------------------------

### <a name="scte-35-mode"></a>SCTE 35 läge

| Fältnamn | Typ av fält | Krävs? | Beskrivningar                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| stack        | Sträng     | Krävs | Händelsemeddelandet.  För [SCTE 35] meddelanden måste vara base64 (IETF RFC 4648) binary kodade splice_info_section() för att meddelanden ska skickas till Dash, HLS och Smooth klienter i enlighet med [SCTE 67].                                              |
| typ       | Sträng     | Krävs | En URN eller en URL som identifierar meddelandet planen. till exempel ”urn: exempel: signaler: 1.0”.  För [SCTE 35] meddelanden måste vara ”urn: scte:scte35:2013a:bin” för att meddelanden ska skickas till Dash, HLS och Smooth klienter i enlighet med [SCTE 67].  |
| id         | Sträng     | Krävs | En unik identifierare som beskriver skarv eller segment. Identifierar den här instansen av meddelandet.  Meddelanden med motsvarande semantik bör ha samma värde.|
| Varaktighet   | Tal     | Krävs | Varaktighet för händelse eller ad splice-segment, om den är känd. Värdet bör vara 0 om det är okänt.                                                                 |
| elapsed    | Tal     | Valfri | När ad-signal [SCTE 35] upprepas för att finjustera och vara det här fältet presentation tidsperiod som har förflutit sedan splice påbörjades. Enheterna är bråkdelssekunder. Det här värdet kan överskrida den ursprungliga angivna varaktigheten för skarv eller segment i [SCTE 35]-läge.                                                  |
| time       | Tal     | Krävs | Presentation tid för händelse eller ad splice.  Presentation starttid och varaktighet ska justeras med dataströmmen åtkomst punkter (SAP) av typen 1 eller 2, som definieras i [ISO-14496-12] bilaga. Tid och varaktighet ska justeras med segment gränser för HLS utgång. Presentation starttid och varaktighet för olika händelsemeddelanden inom samma får händelseströmmen inte överlappa. Enheterna är bråkdelssekunder.

---------------------------

#### <a name="211-cancelation-and-updates"></a>2.1.1 annullering och uppdateringar

Meddelanden kan avbrytas eller uppdateras genom att skicka flera meddelanden med samma presentation tids- och -ID. Presentation tid och ID identifiera unikt händelsen och det sista meddelandet som togs emot för en specifik presentation tid som uppfyller före sammanslagning begränsningar är meddelandet som är efterlevts. Uppdaterade händelse ersätter alla tidigare mottagna meddelanden. Begränsningen före sammanslagning är fyra sekunder. Meddelanden som tagits emot minst fyra sekunder före tidpunkten presentation ska åtgärdas.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 fragmenterad MP4 infognings (Smooth Streaming)
Avse [LIVE-FMP4] infognings-krav på en direktsänd dataström. Följande avsnitt innehåller information om infogande av tidsinställd presentation metadata.  Tidsinställd presentation metadata inhämtas som ett null-optimerade spår som definieras i både Direktmigrering Manifest rutan Server (se MS-SSTR) och rutan film (moov).  Varje sparse fragment består av en film Fragment (moof) och Media Data ('mdat'), där rutan 'mdat' är det binära meddelandet.

#### <a name="221-live-server-manifest-box"></a>2.2.1 live manifestet rutan för Server
Sparse spåra måste deklareras i rutan Live Manifest Server med en \<textstream\> transaktionen och det måste ha följande attribut anges:

| **Attributets namn** | **Typ av fält** | **Krävs?** | **Beskrivning**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Tal         | Krävs      | MÅSTE vara ”0”, som anger ett spår med okänd, variabel bithastighet.                                                                                                                                                                                                 |
| parentTrackName    | Sträng         | Krävs      | MÅSTE vara namnet på överordnade spåra, som sparse Spåra tid koderna är tidsrymd justerad. Överordnad spåra får inte vara ett null-optimerade spår.                                                                                                                    |
| manifestOutput     | Boolesk        | Krävs      | MÅSTE vara ”sant”, som anger att sparse spåra kommer att vara inbäddat i manifestet Smooth klienten.                                                                                                                                                               |
| Undertyp            | Sträng         | Krävs      | Vara måste fyra tecken code ”DATA”.                                                                                                                                                                                                                         |
| schemat             | Sträng         | Krävs      | MÅSTE vara en URN eller en URL identifierar meddelandet planen. till exempel ”urn: exempel: signaler: 1.0”. För [SCTE 35] meddelanden måste vara ”urn: scte:scte35:2013a:bin” för att meddelanden ska skickas till Dash, HLS och Smooth klienter i enlighet med [SCTE 67]. |
| TrackName          | Sträng         | Krävs      | MÅSTE vara namnet på sparse spåra. TrackName kan användas för att skilja flera händelseströmmar med samma schema. Varje unik händelseströmmen måste ha ett unikt namn.                                                                           |
| tidsrymd          | Tal         | Valfri      | MÅSTE vara en tidsrymd av överordnade-spår.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 film rutan

Rutan film (moov) följer Live Manifest rutan Server som en del av rubriken dataström för ett null-optimerade spår.

Rutan 'moov' ska innehålla en **TrackHeaderBox (tkhd)** rutan som definierats i [ISO-14496-12] med följande begränsningar:

| **Fältnamn** | **Typ av fält**          | **Krävs?** | **Beskrivning**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| Varaktighet       | 64-bitars heltal utan tecken | Krävs      | BÖR vara 0, eftersom rutan spåra har noll prover och den totala varaktigheten för exemplen i rutan spåra är 0. |
-------------------------------------

Rutan 'moov' ska innehålla en **HandlerBox (hdlr)** som definierats i [ISO-14496-12] med följande begränsningar:

| **Fältnamn** | **Typ av fält**          | **Krävs?** | **Beskrivning**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32-bitars heltal utan tecken | Krävs      | MÅSTE vara 'meta'. |
-------------------------------------

Rutan 'stsd' ska innehålla en MetaDataSampleEntry ruta med ett namn som kodning som definierats i [ISO-14496-12].  För SCTE 35 meddelanden ska kodning namnet 'scte'.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 film Fragment och Media Data

Sparse spåra fragment består av en film Fragment (moof) och en Media Data (mdat).

Rutan MovieFragmentBox (moof) måste innehålla en **TrackFragmentExtendedHeaderBox (uuid)** rutan som definierats i [MS-SSTR] med följande fält:

| **Fältnamn**         | **Typ av fält**          | **Krävs?** | **Beskrivning**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64-bitars heltal utan tecken | Krävs      | MÅSTE vara ankomsttid för händelsen. Det här värdet justerar meddelandet med överordnade spåra.   |
| fragment_duration      | 64-bitars heltal utan tecken | Krävs      | MÅSTE vara varaktigheten för händelsen. Varaktigheten kan vara noll för att indikera att varaktigheten är okänd. |

------------------------------------


Rutan MediaDataBox (mdat) måste ha följande format:

| **Fältnamn**          | **Typ av fält**                   | **Krävs?** | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32-bitars heltal utan tecken (uimsbf) | Krävs      | Anger formatet för innehållet i rutan 'mdat'. Okänd versioner kommer att ignoreras. För närvarande är den enda versionen som stöds 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bitars heltal utan tecken (uimsbf) | Krävs      | Identifierar den här instansen av meddelandet. Meddelanden med motsvarande semantik skall ha samma värde. bearbeta alla en händelse meddelanderuta från meddelandetjänsten med samma id är tillräckliga.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bitars heltal utan tecken (uimsbf) | Krävs      | Summan av fragment_absolute_time, anges i TrackFragmentExtendedHeaderBox och presentation_time_delta måste vara presentation tid för händelsen. Presentation starttid och varaktighet ska justeras med dataströmmen åtkomst punkter (SAP) av typen 1 eller 2, som definieras i [ISO-14496-12] bilaga. Tid och varaktighet ska justeras med segment gränser för HLS utgång. Presentation starttid och varaktighet för olika händelsemeddelanden inom samma får händelseströmmen inte överlappa. |
| meddelande                 | byte-matris                       | Krävs      | Händelsemeddelandet. För [SCTE 35] meddelanden är meddelandet binära splice_info_section(), även om [SCTE 67] rekommenderar något annat. För [SCTE 35] meddelanden måste vara splice_info_section() för att meddelanden ska skickas till Dash, HLS och Smooth klienter i enlighet med [SCTE 67]. För [SCTE 35] meddelanden binära splice_info_section() nyttolasten för rutan 'mdat' och är inte base64-kodad.                                                            |

------------------------------


### <a name="224-cancelation-and-updates"></a>2.2.4 annullering och uppdateringar
Meddelanden kan avbrytas eller uppdateras genom att skicka flera meddelanden med samma presentation tids- och -ID.  Presentation tid och ID identifiera unikt händelsen. Det sista meddelandet som togs emot för en specifik presentation tid, som uppfyller före sammanslagning begränsningar är det meddelande som är efterlevts. Uppdaterade meddelandet ersätter alla tidigare mottagna meddelanden.  Begränsningen före sammanslagning är fyra sekunder. Meddelanden som tagits emot minst fyra sekunder före tidpunkten presentation ska åtgärdas. 


## <a name="3-timed-metadata-delivery"></a>3 tidsgränsen Metadata leverans

Händelsedata i dataströmmen är täckande till Media Services. Media Services Överför bara tre typer av information mellan infognings-slutpunkten och klient-slutpunkt. Följande egenskaper levereras till klienten, i enlighet med [SCTE 67] och/eller klienten streaming protocol:

1.  Schema – en URN eller en URL som anger schemat för meddelandet.

2.  Presentation tid – presentation tid för händelse på tidslinjen för media.

3.  Varaktighet – varaktigheten för händelsen.

4.  ID – en valfri unika identifierare för händelsen.

5.  Meddelande – informationen om händelsen.


## <a name="31-smooth-streaming-delivery"></a>3.1 smooth strömmande leverans

Referera till sparse spåra hanterar information om [MS-SSTR].

#### <a name="smooth-client-manifest-example"></a>Manifestet Smooth Client-exempel
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Apple HLS leverans
Tidsinställda metadata för Apple HTTP Live Streaming (HLS) kan vara inbäddat i listan segment i en anpassad M3U-tagg.  Applikationsnivån kan parsa M3U spelningslista och bearbeta M3U taggar. Azure Media Services bäddas tidsinställd metadata i taggen EXT-X-stack definierad i [HLS].  EXT-X-stack-taggen är för närvarande används av DynaMux för meddelanden av typen ADI3.  Ange attributen för taggen EXT-X-stack för att stödja SCTE 35 och icke SCTE 35 meddelanden, enligt nedan:

| **Attributets namn** | **Typ**                      | **Krävs?**                             | **Beskrivning**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| STACK                | sträng inom citattecken                 | Krävs                                  | Meddelandet kodade som en base64-sträng, enligt beskrivningen i [IETF RFC 4648](http://tools.ietf.org/html/rfc4648). För [SCTE 35] meddelanden är base64-kodade splice_info_section().                                                                                                |
| TYP               | sträng inom citattecken                 | Krävs                                  | En URN eller en URL som identifierar meddelandet planen. till exempel ”urn: exempel: signaler: 1.0”. För [SCTE 35] meddelanden tar typen särskilda värdet ”scte35”.                                                                                                                                |
| ID                 | sträng inom citattecken                 | Krävs                                  | En unik identifierare för händelsen. Om ID: T inte är anges när meddelandet inhämtas, genereras ett unikt id i Azure Media Services.                                                                                                                                          |
| VARAKTIGHET           | decimal flyttal | Krävs                                  | Varaktighet för händelsen. Värdet bör vara 0 om det är okänt. Enheterna är factional sekunder.                                                                                                                                                                                           |
| FÖRFLUTEN TID            | decimal flyttal | Valfritt, men krävs för glidande fönster | När signalen upprepas som stöd för ett skjutfönster presentation och måste det här fältet vara presentation tidsperiod som har förflutit sedan händelsen påbörjades. Enheterna är bråkdelssekunder. Det här värdet kan överskrida den ursprungliga angivna varaktigheten för skarv eller segment. |
| TID               | decimal flyttal | Krävs                                  | Presentation-tid för händelsen. Enheterna är bråkdelssekunder.                                                                                                                                                                                                                    |


Applikationsnivån HLS player använder typen för att identifiera format för meddelandet, avkoda meddelandet, konvertera tid som krävs och bearbeta händelsen.  Händelser är tidsinställningen synkroniserad i listan segment av överordnade enligt tidsstämpel för händelse.  De infogas före det närmaste segmentet (#EXTINF tagg).

#### <a name="hls-segment-playlist-example"></a>HLS Segment spelningslista exempel
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:6
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:6.000000,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=60000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
#EXT-X-CUE: ID=”metadata-12.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”12.000000”, DURATION=”18.000000”,CUE=”HrwOi8vYmWVkaWEvhhaWFRlRDa=”
Fragments(video=120000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=180000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=240000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=300000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=360000000,format=m3u8-aapl)
#EXT-X-CUE: ID=”metadata-42.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”42.000000”, DURATION=”60.000000”,CUE=”PD94bWwgdm0iMS4wIiBlbmNvpD4=”
#EXTINF:6.000000,no-desc
Fragments(video=420000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=480000000,format=m3u8-aapl)
…
~~~

#### <a name="hls-message-handling"></a>HLS meddelandehantering

Händelser signaleras i segmentet spelningslista med varje video och ljud spår. Positionen för EXT-X-stack-taggen måste alltid vara antingen omedelbart före de första HLS segmentet (för skarv ut eller segment start) eller omedelbart efter de senaste HLS segmentet (för skarv i eller segment end) till dess tidpunkt och varaktighet attribut finns, vilket krävs av [ HLS].

När ett skjutfönster presentation aktiveras ska EXT-X-stack-taggen upprepas tillräckligt ofta att splice eller segment alltid är fullständigt beskrivs i listan segment och attributet GÅTT måste användas för att ange hur lång tid splice eller segment har varit aktivt som krävs av [HLS].

När ett skjutfönster presentation aktiveras tas EXT-X-stack-taggar bort från listan segment när media-tid som de refererar till har återställts utanför skjutfönster för presentation.

## <a name="33--dash-delivery"></a>3.3 DASH leverans
[DASH] tillhandahåller tre sätt att signal händelser:

1.  Händelse uppstår i MPD
2.  Händelser signaleras band återgivning (med hjälp av händelse meddelanderuta (emsg)
3.  En kombination av både 1 och 2

Händelse uppstår i MPD är användbara för VOD-direktuppspelning eftersom klienter har åtkomst till alla händelser, omedelbart när MPD hämtas. In-band-lösningen är användbart för direktsänd strömning eftersom klienterna inte behöver ladda ned MPD igen. För tidsbaserade segmentering anger klienten du URL-Adressen för nästa segment genom att lägga till varaktighet och tidsstämpeln för det aktuella segmentet. Om som begär misslyckas klienten förutsätter en avvikelse och hämtar MPD men annars fortsätter strömning utan att hämta MPD.

Azure Media Services gör både signalering i MPD och band med hjälp av händelse meddelanderutan-signalering.

#### <a name="mpd-signaling"></a>MPD-signalering

Händelser signaleras i MPD med EventStream element, som visas i elementet Period.

Elementet EventStream har följande attribut:

| **Attributets namn** | **Typ**                | **Krävs?** | **Beskrivning**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | sträng                  | Krävs      | Identifierar schemat för meddelandet. Schemat har angetts till värdet för attributet schemat i rutan Live Server Manifest. Värdet måste vara en URN eller en URL som identifierar meddelandet planen. till exempel ”urn: exempel: signaler: 1.0”.                                                                |
| värde              | sträng                  | Valfri      | En ytterligare sträng-värde som används av ägarna av schemat för att anpassa semantiken för meddelandet. Värdet måste anges till namnet på händelseströmmen (trackName för Smooth infognings- eller AMF meddelandenamn för RTMP infognings-) för att skilja flera händelseströmmar med samma schema. |
| tidsrymd          | 32-bitars heltal utan tecken | Krävs      | Tidsrymd i tick per sekund, i fälten gånger och varaktighet i rutan 'emsg'.                                                                                                                                                                                                       |


Noll eller flera element i händelsen finns i elementet EventStream, och de har följande attribut:

| **Attributets namn**  | **Typ**                | **Krävs?** | **Beskrivning**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 64-bitars heltal utan tecken | Valfri      | MÅSTE vara media presentation tid för händelse i förhållande till början av perioden. Presentation starttid och varaktighet ska justeras med dataströmmen åtkomst punkter (SAP) av typen 1 eller 2, som definieras i [ISO-14496-12] bilaga. |
| Varaktighet            | 32-bitars heltal utan tecken | Valfri      | Varaktighet för händelsen. Detta måste utelämnas om varaktighet är okänd.                                                                                                                                                 |
| id                  | 32-bitars heltal utan tecken | Valfri      | Identifierar den här instansen av meddelandet. Meddelanden med motsvarande semantik bör ha samma värde. Om ID: T inte är anges när meddelandet inhämtas, genereras ett unikt id i Azure Media Services.             |
| Händelsen elementvärde | sträng                  | Krävs      | Händelsemeddelandet som en base64-sträng som beskrivs i [IETF RFC 4648](http://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>XML-Syntax och exempel för DASH manifest signal (MPD)

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->

<EventStream schemeIdUri=”urn:example:signaling:1.0” timescale=”1000” value=”player-statistics”>
  <Event presentationTime=”0” duration=”10000” id=”0”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
  <Event presentationTime=”20000” duration=”10000” id=”1”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
</EventStream>
~~~

>[!NOTE]
>Observera att presentationTime presentation tid för händelse, inte ankomsttid för meddelandet.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 in-band-händelse visas rutan-signalering
En in-band-händelseström kräver MPD ha ett InbandEventStream element på nivån anpassning anges.  Det här elementet har en obligatorisk schemeIdUri attribut och valfria tidsrymd attribut, som också finns i meddelanderutan (emsg).  Händelsen meddelanderutor med schemat identifierare som inte har definierats i MPD bör inte finnas. Om en DASH-klienten identifierar en händelse meddelanderuta från meddelandetjänsten med ett schema som inte är definierad i MPD, förväntas klienten att den ska ignoreras.
Rutan händelsemeddelandet (emsg) ger signalering för allmänna händelser relaterade till media presentation tid. Om det finns att en 'emsg'-ruta placera innan en 'moof'-ruta.

### <a name="dash-event-message-box-emsg"></a>STRECK händelse meddelanderuta 'emsg'
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

Fälten för DASHEventMessageBox anges nedan.

| **Fältnamn**          | **Typ av fält**          | **Krävs?** | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | sträng                  | Krävs      | Identifierar schemat för meddelandet. Schemat har angetts till värdet för attributet schemat i rutan Live Server Manifest. Värdet måste vara en URN eller en URL som identifierar meddelandet planen. till exempel ”urn: exempel: signaler: 1.0”. För [SCTE 35] meddelanden kommer särskilda värdet ”urn: scte:scte35:2013a:bin”, även om [SCTE 67] rekommenderar något annat. |
| Värde                   | sträng                  | Krävs      | En ytterligare sträng-värde som används av ägarna av schemat för att anpassa semantiken för meddelandet. För att skilja flera händelseströmmar med samma schema kommer värdet anges till namnet på händelseströmmen (trackName för Smooth infognings- eller AMF meddelandenamn för RTMP mata in).                                                                  |
| tidsrymd               | 32-bitars heltal utan tecken | Krävs      | Tidsrymd i tick per sekund, i fälten gånger och varaktighet i rutan 'emsg'.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32-bitars heltal utan tecken | Krävs      | Den media presentation tidsspann presentation tid för händelsen och den tidigaste presentation tid i det här segmentet. Presentation starttid och varaktighet ska justeras med dataströmmen åtkomst punkter (SAP) av typen 1 eller 2, som definieras i [ISO-14496-12] bilaga.                                                                                            |
| event_duration          | 32-bitars heltal utan tecken | Krävs      | Varaktigheten för händelsen eller 0xFFFFFFFF anger en okänd varaktighet.                                                                                                                                                                                                                                                                                          |
| Id                      | 32-bitars heltal utan tecken | Krävs      | Identifierar den här instansen av meddelandet. Meddelanden med motsvarande semantik bör ha samma värde. Om ID: T inte är anges när meddelandet inhämtas, genereras ett unikt id i Azure Media Services.                                                                                                                                                    |
| Message_data            | byte-matris              | Krävs      | Händelsemeddelandet. För [SCTE 35] meddelanden är meddelandedata binära splice_info_section(), även om [SCTE 67] rekommenderar något annat.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 STRECK meddelandehantering

Händelser signaleras i band, i rutan 'emsg' för både ljud och video spår.  Signalering inträffar för alla segment begäranden som presentation_time_delta är 15 sekunder eller mindre. När ett skjutfönster presentation aktiveras tas händelsemeddelanden bort från MPD när summan av tid och varaktighet för meddelandet är mindre än tidpunkten för media data i manifestet.  Med andra ord tas meddelanden om händelser bort från manifestet när media tiden som de avser har återställts utanför skjutfönster för presentation.

## <a name="4-scte-35-ingest"></a>4 SCTE-35 mata in

[SCTE 35] meddelanden inhämtas i binärformat med **”urn: scte:scte35:2013a:bin”** Smooth infognings och typen **”scte35”** RTMP infognings. Att underlätta konvertering av [SCTE 35] tidpunkten, som baseras på MPEG-2 transport dataströmmen presentation tidsstämplar (punkter), en mappning mellan punkter (pts_time + pts_adjustment av splice_time()) och media tidslinjen tillhandahålls av händelse presentation tid ( fältet fragment_absolute_time för Smooth mata in och infognings-fältet för RTMP). Mappningen är nödvändigt eftersom 33-bitars pt värde samlar ungefär var 26.5 timmarna.

Mata in Smooth Streaming kräver att rutan Media (mdat) måste innehålla den **splice_info_section()** definieras i tabell 8-1 [SCTE 35]. RTMP infognings, attributet stack-postmeddelandets AMF är inställt på base64encoded **splice_info_section()**. När meddelanden har formatet som beskrivs ovan, skickas de till Dash, HLS och Smooth klienter i enlighet med [SCTE 67].


## <a name="5-references"></a>5 referenser

**[SCTE 35]**  ANSI/SCTE 35 2013a – Program för Digital infogning köa meddelande för kabel, 2013a

**[SCTE 67]**  ANSI/SCTE 67 2014 – rekommenderad säkerhetspraxis för SCTE 35: Program för Digital infogning köa meddelande för kabel

**[DASH]**  ISO/IEC 23009 1 2014 – informationsteknik – dynamiska anpassningsbar strömning via HTTP-(bindestreck) – del 1: Media Presentation beskrivning och segment format, 2 edition

**[HLS]**  [”HTTP Live Streaming”, draft-pantos-http-live-streaming-14, 14 oktober 2014](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]**  [”Microsoft Smooth Streaming protokoll”, 15 maj 2014](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]**  [”Åtgärden meddelandet Format AMF0”](http://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE FMP4]**  [Azure Media Services fragmenterad MP4 Live infognings-specifikationen](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]**  ISO/IEC 14496 12: del 12 ISO grundläggande mediafilen fjärde Edition 2012-07-15-format.

**[RTMP]**  [”Adobe realtid meddelanden protokoll”, 21 December 2012](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>Nästa steg
Visa Media Services utbildningsvägar.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
