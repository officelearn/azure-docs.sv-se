---
title: Azure Media Services - Signaling tidsinnade metadata i livestreaming
description: Den här specifikationen beskriver metoder för signalering av tidsbetonade metadata vid intag och direktuppspelning till Azure Media Services. Detta inkluderar stöd för generiska tidsinmatade metadatasignaler (ID3), samt SCTE-35-signalering för ad insertion och skarvtillståndssignalering.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: 551fb0cb9f3745a62d5d84f2c4878bbbbe5ad9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137330"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Tidsinställningar för signalering i livestreaming 

Senast uppdaterad: 2019-08-22

### <a name="conformance-notation"></a>Notering av konforman

Nyckelorden "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" och "OPTIONAL" i detta dokument ska tolkas enligt beskrivningen i RFC 2119

## <a name="1-introduction"></a>1. Inledning 

För att signalera införandet av annonser eller anpassade metadatahändelser på en klientspelare använder programföretagen ofta tidsinställd metadata som är inbäddade i videon. För att aktivera dessa scenarier ger Media Services stöd för transport av tidsinniga metadata från den intövande punkten för direktuppspelningskanalen till klientprogrammet.
Den här specifikationen beskriver flera lägen som stöds av Media Services för tidsbända metadata i direktuppspelningssignaler.

1. [SCTE-35] signalering som uppfyller de standarder som beskrivs av [SCTE-35], [SCTE-214-1], [SCTE-214-3] och [RFC8216]

2. [SCTE-35] signalering som överensstämmer med den äldre [Adobe-Primetime] specifikationen för RTMP-annonssignalering.
   
3. Ett allmänt tidsinmatat metadatasignaleringsläge för meddelanden som **INTE** är [SCTE-35] och som kan bära [ID3v2] eller andra anpassade scheman som definierats av programutvecklaren.

## <a name="11-terms-used"></a>1.1 Använda termer

| Period                | Definition                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Avbrott i annonser            | En plats eller tidpunkt där en eller flera annonser kan schemaläggas för leverans. samma som nytta och placering möjlighet.                                                                                                                     |
| Tjänst för annonsbeslut | externa tjänst som bestämmer vilka annonser och varaktigheter som ska visas för användaren. Tjänsterna tillhandahålls vanligtvis av en partner och är utanför omfånget för det här dokumentet.                                                                    |
| Cue                 | Uppgift om tid och parametrar för den kommande annonspausen. Observera att köer kan indikera en väntande övergång till en annonspaus, väntande övergång till nästa annons i en annonspaus och väntande övergång från en annonspaus till huvudinnehållet.           |
| Packager            | Azure Media Services "Streaming Endpoint" ger dynamiska förpackningsfunktioner för DASH och HLS och kallas "Packager" i mediebranschen.                                                                              |
| Presentationstid   | Den tid som en händelse presenteras för en tittare. Tiden representerar det ögonblick på media tidslinjen som en tittare skulle se händelsen. Presentationstiden för ett STE-35-splice_info()-kommandomeddelande är till exempel splice_time(). |
| Ankomsttid        | Den tid som ett händelsemeddelande anländer. Tiden skiljer sig vanligtvis från presentationen tid för händelsen, eftersom händelsemeddelanden skickas före presentationen tid för händelsen.                                                    |
| Gles spår        | mediespår som inte är kontinuerligt och som tid synkroniseras med ett överordnat spår eller kontrollspår.                                                                                                                                                  |
| Ursprung              | Streamingtjänsten för Azure Media                                                                                                                                                                                                             |
| Kanal Diskbänk        | The Azure Media Live Streaming Service                                                                                                                                                                                                        |
| HLS                 | Apple HTTP Live Streaming-protokoll                                                                                                                                                                                                            |
| Dash                | Dynamisk adaptiv direktuppspelning över HTTP                                                                                                                                                                                                          |
| Smidig              | Smidigt protokoll för direktuppspelning                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG 2 Transportströmmar                                                                                                                                                                                                                      |
| RTMP                | Multimediaprotokoll i realtid                                                                                                                                                                                                                 |
| uimsbf (på)              | Osignerat heltal, mest signifikanta bit först.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 Normativa referenser

Följande dokument innehåller bestämmelser som genom hänvisning i denna text utgör bestämmelser i detta dokument. Alla dokument är föremål för översyn av standardiseringsorganen, och läsarna uppmanas att undersöka möjligheten att tillämpa de senaste utgåvorna av de dokument som anges nedan. Läsare påminns också om att nyare versioner av de refererade dokumenten kanske inte är kompatibla med den här versionen av den tidsinbästa metadataspecifikationen för Azure Media Services.


| Standard          | Definition                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime Digital Program Insättningssignal specifikation 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Språkreferens för FLASH ActionScript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| - Jag vet inte vad du säger.            | ["Åtgärdsmeddelandeformat AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | DASH Industri Forum Interop Vägledning v 4,2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Tidsinläst metadata för HTTP Live Streaming -[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Tidsinformad metadata i CMAF (Common Media Application Format)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 Tag version 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: Del 12 ISO-basmediafilformat, Fjärde Uppgången 2012-07-15                                                                                                                                 |
| -Jag har inte tid med det här.        | Informationsteknik – Dynamisk adaptiv direktuppspelning via HTTP (DASH) – Del 1: Beskrivning av mediepresentation och segmentformat. maj 2014. Publicerade. Url:https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Informationsteknik – MPEG-A (Multimedia application format) – Del 19: CMAF (Common Media Application Format) för segmenterade medier. januari 2018. Publicerade. Url:https://www.iso.org/standard/71975.html |
| -Jag har inte tid med det här.        | Informationsteknik – MPEG-systemteknik – Del 7: Gemensam kryptering i ISO-basfilformatfiler. februari 2016. Publicerade. Url:https://www.iso.org/standard/68042.html                   |
| -Jag har inte tid med det här.         | ["Microsoft Smooth Streaming Protocol", 15 maj 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-Ingest]  | [Fragmenterad MP4 Live-specifikation för Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. Pantos, Ed.; W. Maj. HTTP Live Streaming. augusti 2017. Informativt. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Datakodnings- och baskodningskodningen Base16, Base32 och Base64 -[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| -Jag har inte tid med det här.            | ["Adobes protokoll för meddelanden i realtid", 21 december 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 - Cueing-meddelande för infogning av digitala program för kabel -https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH för IP-baserade kabeltjänster Del 1: MPD-begränsningar och tillägg                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH för IP-baserade kabeltjänster Del 3: DASH/FF-profil                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – Gränssnitt för schemaläggning och anmälan till evenemang                                                                                                                                                  |
| [SCTE-250]        | API för händelse- och signalhantering (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Tidsintade metadata intas

Azure Media Services stöder realtidsmetadata för både [RTMP] och Smooth Streaming [MS-SSTR-Ingest] protokoll. Metadata i realtid kan användas för att definiera anpassade händelser, med dina egna unika anpassade scheman (JSON, Binary, XML) samt branschdefinierade format som ID3 eller SCTE-35 för annonssignalering i en broadcast-ström. 

Den här artikeln innehåller information om hur du skickar anpassade tidsinniga metadatasignaler med hjälp av de protokoll som stöds för Azure Media Services. Artikeln förklarar också hur manifesten för HLS, DASH och Smooth Streaming är dekorerade med tidsintade metadatasignaler, samt hur det bärs i bandet när innehållet levereras med CMAF-segment (MP4-fragment) eller Transport Stream (TS) för HLS. 

Vanliga användningsfallsscenarier för tidsbegripade metadata är:

 - SCTE-35-annonssignaler för att utlösa annonspauser i en livehändelse eller linjär sändning
 - Anpassade ID3-metadata som kan utlösa händelser i ett klientprogram (webbläsare, iOS eller Android)
 - Anpassade definierade JSON-, binära eller XML-metadata för att utlösa händelser i ett klientprogram
 - Telemetri från en live-kodare, IP-kamera eller drone
 - Händelser från en IP-kamera som rörelse, ansiktsigenkänning, etc.
 - Geografisk positionsinformation från en åtgärdskamera, drönare eller flyttenhet
 - Låttexter
 - Programgränser för en linjär live-feed
 - Bilder eller utökade metadata som ska visas i en live-feed
 - Sportresultat eller information om spelklockan
 - Interaktiva reklampaket som ska visas tillsammans med videon i webbläsaren
 - Frågesporter eller omröstningar
  
Azure Media Services Live Events och Packager kan ta emot dessa tidsinniga metadatasignaler och konvertera dem till en ström av metadata som kan nå klientprogram med hjälp av standardbaserade protokoll som HLS och DASH.


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP-tidsinnade metadata

[RTMP]-protokollet gör det möjligt att skicka tidsinnade metadatasignaler för olika scenarier, inklusive anpassade metadata och SCTE-35-annonssignaler. 

Reklamsignaler (cue-meddelanden) skickas som [AMF0] cue-meddelanden inbäddade i [RTMP]-strömmen. Kömeddelandena kan skickas någon gång innan den faktiska händelsen eller [SCTE35] ad splice signal måste inträffa. För att stödja det här scenariot skickas den faktiska presentationens tidsstämpel för händelsen i kömeddelandet. Mer information finns i [AMF0].

Följande [AMF0]-kommandon stöds av Azure Media Services för RTMP-intag:

- **onUserDataEvent** - används för anpassade metadata eller [ID3v2] tidsinlämda metadata
- **onAdCue** - används främst för att signalera en annons placering möjlighet i live stream. Två former av kö stöds, ett enkelt läge och ett "SCTE-35"-läge. 
- **onCuePoint** - stöds av vissa lokala maskinvarukodare, som Elemental Live-kodaren, för att signalera [SCTE35] meddelanden. 
  

I följande tabell beskrivs formatet för den AMF-meddelandenyttolat som Media Services får förtjänst för både meddelandelägena "enkel" och [SCTE35].

Namnet på [AMF0]-meddelandet kan användas för att skilja flera händelseströmmar av samma typ.  För både [SCTE-35] meddelanden och "enkelt" läge måste namnet på AMF-meddelandet vara "onAdCue" enligt kraven i [Adobe-Primetime]-specifikationen.  Alla fält som inte visas nedan ska ignoreras av Azure Media Services vid intag.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP med anpassade metadata med "onUserDataEvent"

Om du vill tillhandahålla anpassade metadataflöden från din uppströmskodare, IP-kamera, drönare eller enhet med RTMP-protokollet använder du kommandotypen "onUserDataEvent" [AMF0].

Datameddelandekommandot **"onUserDataEvent"** måste bära en meddelandenyttolast med följande definition som ska fångas av Media Services och paketeras i in-band-filformatet samt manifesten för HLS, DASH och Smooth Streaming.
Vi rekommenderar att du skickar tidsinnade metadatameddelanden inte oftare än en gång var 0,5 sekund (500 ms) eller stabilitetsproblem med livestreamen kan uppstå. Varje meddelande kan aggregera metadata från flera ramar om du behöver ange metadata på ramnivå. Om du skickar multibitrate-strömmar rekommenderar vi att du även tillhandahåller metadata på en enda bithastighet bara för att minska bandbredden och undvika störningar i video-/ljudbearbetning. 

Nyttolasten för **"onUserDataEvent"** ska vara ett [MPEGDASH] EventStream XML-formatmeddelande. Detta gör det enkelt att passera i anpassade definierade scheman som kan bäras i 'emsg' nyttolaster i bandet för CMAF [MPEGCMAF] innehåll som levereras via HLS- eller DASH-protokoll. Varje DASH Event Stream-meddelande innehåller ett schemaIdUri som fungerar som en URN-meddelandeschemaidentifierare och definierar nyttolasten för meddelandet. Vissa system somhttps://aomedia.org/emsg/ID3" " för [ID3v2] eller **urn:scte:scte35:2013:bin** för [SCTE-35] standardiseras av branschkonsortier för interoperabilitet. Alla programleverantörer kan definiera sitt eget anpassade schema med hjälp av en URL som de styr (ägd domän) och kan tillhandahålla en specifikation på den webbadressen om de vill. Om en spelare har en hanterare för det definierade schemat är det den enda komponenten som behöver förstå nyttolasten och protokollet.

Schemat för [MPEG-DASH] EventStream XML-nyttolasten definieras som (utdrag från DASH ISO-IEC-23009-1-3rd Edition). Observera att endast en "EventType" per "EventStream" stöds just nu. Endast det första **händelseelementet** bearbetas om flera händelser anges i **EventStream**.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Exempel på XML Event Stream med ID3-schema-ID och base64-kodad datanyttolast.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Exempel på händelseström med anpassade schema-ID och base64-kodade binära data  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Exempel på händelseström med anpassat schema-ID och anpassad JSON  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>Inbyggda schema-ID-urier som stöds
| Schema-ID-IURI                 | Beskrivning                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | Beskriver hur [ID3v2]-metadata kan överföras som tidsintade metadata i en CMAF-kompatibel [MPEGCMAF] fragmenterad MP4. Mer information finns [i tidsinformade metadata i CMAF (Common Media Application Format)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Händelsebearbetning och manifestsignalering

Vid mottagandet av en giltig **"onUserDataEvent"-händelse** söker Azure Media Services efter en giltig XML-nyttolast som matchar EventStreamType (definierad i [MPEGDASH] ), tolkar XML-nyttolasten och konverterar den till en [MPEGCMAF] MP4-fragmentversion 1-box för lagring i det direktsända arkivet och överföringen till Media Services Packager.   Den Packer kommer att upptäcka "emsg" rutan i live stream och:

- (a) "dynamiskt paketera" det till TS-segment för leverans till HLS-klienter i enlighet med HLS tidsinställda metadataspecifikation [HLS-TMD], eller
- b) försa den för leverans i CMAF-fragment via HLS eller DASH, eller 
- c) omvandla den till en gles spårsignal för leverans via Smooth Streaming [MS-SSTR].

Förutom cmaf- eller TS PES-paketen i bandets format för HLS innehåller manifesten för DASH (MPD) och Smooth Streaming en referens till in-band-händelseströmmarna (även kallat sparse stream-spår i Smooth Streaming). 

Enskilda händelser eller deras datanyttolaster matas INTE ut direkt i HLS-, DASH- eller Smooth-manifesten. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Ytterligare informationsbegränsningar och standardvärden för onUserDataEvent-händelser

- Om tidsskalan inte anges i EventStream-elementet används tidsskalan RTMP 1 kHz som standard
- Leverans av ett onUserDataEvent-meddelande är begränsad till en gång per 500ms max. Om du skickar händelser oftare kan det påverka bandbredden och stabiliteten i live-matningen

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 RTMP-annonssignal signalering med "onAdCue"

Azure Media Services kan lyssna och svara på flera [AMF0]-meddelandetyper som kan användas för att signalera olika realtidssynkronerade metadata i livestreamen.  [Adobe-Primetime]-specifikationen definierar två kötyper som kallas "enkel" och "SCTE-35"-läge. För "enkelt" läge stöder Media Services ett enda AMF-stackmeddelande som kallas "onAdCue" med en nyttolast som matchar tabellen nedan definierad för signalen "Enkelt läge".  

Följande avsnitt visar RTMP "enkelt" läge nyttolast, som kan användas för att signalera en grundläggande "spliceOut" annonssignal som kommer att överföras till klientmanifestet för HLS, DASH och Microsoft Smooth Streaming. Detta är mycket användbart för scenarier där kunden inte har ett komplext SCTE-35-baserat annonssignaldistributions- eller insättningssystem och använder en grundläggande lokal kodare för att skicka in stack-ikonen via ett API. Vanligtvis stöder den lokala kodaren ett REST-baserat API för att utlösa den här signalen, som också "skarvar-villkorar" videoströmmen genom att infoga en IDR-ram i videon och starta en ny GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 RTMP-annonssignal signalering med "onAdCue" - Enkelt läge

| Fältnamn | Fälttyp | Krävs? | Beskrivningar                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| typ       | String     | Krävs  | Händelsemeddelandet.  Ska vara "SpliceOut" för att beteckna en enkel läge skarv.                                                                                                                                                                                                         |
| id         | String     | Krävs  | En unik identifierare som beskriver skarv eller segment. Identifierar den här instansen av meddelandet                                                                                                                                                                                       |
| varaktighet   | Tal     | Krävs  | Varaktigheten av skarv. Enheter är fraktionerad sekund.                                                                                                                                                                                                                           |
| elapsed    | Tal     | Valfri  | När signalen upprepas för att stödja ingången ska detta fält vara den mängd presentationstid som har förflutit sedan skarvningen började. Enheter är fraktionerad sekund. När du använder enkelt läge bör detta värde inte överstiga den ursprungliga varaktigheten av skarv. |
| time       | Tal     | Krävs  | Skall vara tiden för skarv, i presentationen tid. Enheter är fraktionerad sekund.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exempel på MPEG DASH-manifestutdata när du använder enkelt Adobe RTMP-läge

Se exempel [3.3.2.1 MPEG DASH .mpd EventStream med enkelt Adobe-läge](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Se exempel [3.3.3.1 DASH-manifest med enkel period och Adobe enkelt läge](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exempel på HLS-manifestutdata när du använder enkelt Adobe RTMP-läge

Se exempel [3.2.2 HLS manifest med Adobe enkelt läge och EXT-X-CUE tagg](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 RTMP-annonssignal signalering med "onAdCue" - SCTE-35 Mode

När du arbetar med ett mer avancerat broadcast-produktionsarbetsflöde som kräver att det fullständiga SCTE-35-nyttolastmeddelandet ska överföras till HLS- eller DASH-manifestet är det bäst att använda "SCTE-35 Mode" i [Adobe-Primetime]-specifikationen.  Det här läget stöder att SCTE-35-signaler i frekvensbandet skickas direkt till en lokal livekodare, som sedan kodar ut signalerna i RTMP-strömmen med hjälp av "SCTE-35 Mode" som anges i [Adobe-Primetime]-specifikationen. 

Vanligtvis kan SCTE-35-meddelanden bara visas i TS-indata (MPEG-2 transportström) på en lokal kodare. Kontakta kodartillverkaren för mer information om hur du konfigurerar ett transportflödesföseri som innehåller SCTE-35 och aktivera den för vidareström till RTMP i Adobe SCTE-35-läge.

I det här fallet måste följande nyttolast skickas från den lokala kodaren med meddelandetypen **"onAdCue"** [AMF0].

| Fältnamn | Fälttyp | Krävs? | Beskrivningar                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cue        | String     | Krävs  | Händelsemeddelandet.  För [SCTE-35]-meddelanden måste detta vara base64-kodade [RFC4648] binära splice_info_section() för att meddelanden ska skickas till HLS-, Smooth- och Dash-klienter.                                                                                                                                                                                                                               |
| typ       | String     | Krävs  | En URN eller URL som identifierar meddelandeschemat. För [SCTE-35] meddelanden **bör** detta vara **"scte35"** för att meddelanden ska skickas till HLS-, Smooth- och Dash-klienter, i enlighet med [Adobe-Primetime]. Du kan också använda URN-meddelandet urn:scte:scte35:2013:bin för att signalera ett [SCTE-35]-meddelande.                                                                                                        |
| id         | String     | Krävs  | En unik identifierare som beskriver skarv eller segment. Identifierar den här instansen av meddelandet.  Meddelanden med motsvarande semantik ska ha samma värde.                                                                                                                                                                                                                                                       |
| varaktighet   | Tal     | Krävs  | Varaktigheten av händelsen eller annons skarv-segmentet, om det är känt. Om det är okänt **ska** värdet vara 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Tal     | Valfri  | När [SCTE-35]-annonssignalen upprepas för att ställa in, ska detta fält vara den mängd presentationstid som har förflutit sedan skarvningen började. Enheter är fraktionerad sekund. I [SCTE-35] -läge kan detta värde överstiga den ursprungliga angivna varaktigheten för skarv eller segment.                                                                                                                   |
| time       | Tal     | Krävs  | Presentationstiden för händelsen eller annonssker.  Presentationstiden och varaktigheten **BÖR** anpassas till Stream Access Points (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I. För HLS-utgående bör tid och **varaktighet** anpassa sig till segmentgränserna. Presentationstiden och varaktigheten för olika händelsemeddelanden i samma händelseström får inte överlappa varandra. Enheter är fraktionerad sekund. |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>Exempel på MPEG DASH .mpd-manifest med SCTE-35-läge
Se [avsnitt 3.3.3.2 exempel DASH manifest med SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Exempel på HLS-manifestet .m3u8 med SCTE-35-lägessignal
Se [avsnitt 3.2.1.1 exempel HLS manifest med SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP-annonssignalering med "onCuePoint" för Elemental Live

Elemental Live lokalt kodare stöder annonsmarkörer i RTMP-signalen. Azure Media Services stöder för närvarande endast typen "onCuePoint" ad marker för RTMP.  Detta kan aktiveras i Adobe RTMP-gruppinställningarna i inställningarna elemental media live eller API genom att ställa in "**ad_markers**" till "onCuePoint".  Mer information finns i Dokumentationen till Elemental Live. Om du aktiverar den här funktionen i RTMP-gruppen skickar SCTE-35-signaler till Adobe RTMP-utgångarna som ska bearbetas av Azure Media Services.

Meddelandetypen "onCuePoint" definieras i [Adobe-Flash-AS] och har följande nyttolaststruktur när den skickas från Elemental Live RTMP-utdata.


| Egenskap   | Beskrivning                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| namn       | Namnet BÖR vara '**scte35**' av Elemental Live.                                                                                                                                                                              |
| time       | Den tid i sekunder då referenspunkten inträffade i videofilen under tidslinjen                                                                                                                                           |
| typ       | Typen av referenspunkt bör ställas in på "**händelse**".                                                                                                                                                                             |
| parameters | En associativ matris med namn-/värdeparsträngar som innehåller informationen från MEDDELANDET SCTE-35, inklusive ID och varaktighet. Dessa värden tolkas av Azure Media Services och ingår i manifestdekorationstaggen. |


När det här annonsmarkörläget används liknar HLS-manifestutdata Adobes "enkla" läge.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Exempel PÅ MPEG DASH MPD, signaler för en period, Adobe Simple-läge

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Exempel på HLS-spellista, Adobe Simple-mode-signaler med EXT-X-CUE-tagg (trunkerad "..." för korthet)

I följande exempel visas utdata från den dynamiska paketeraren för Media Services för en RTMP-inmatningsström med Adobes "enkla" lägessignaler och den äldre [Adobe-Primetime] EXT-X-CUE-taggen.  

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 Avbokning och uppdateringar

Meddelanden kan avbrytas eller uppdateras genom att skicka flera meddelanden med samma presentationstid och ID. Presentationstiden och ID:n identifierar händelsen unikt och det senaste meddelandet som tas emot för en viss presentationstid som uppfyller pre-roll-begränsningar är det budskap som åtgärdas. Den uppdaterade händelsen ersätter alla tidigare mottagna meddelanden. Pre-roll-begränsningen är fyra sekunder. Meddelanden som tas emot minst fyra sekunder före presentationstiden kommer att åtgärdas.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Fragmenterad MP4-intiga (jämn strömning)

Se [MS-SSTR-Ingest] för krav på live stream-intag. I följande avsnitt finns information om intag av tidsinnade presentationsmetadata.  Tidsinförda presentationsmetadata intas som ett glest spår, vilket definieras i både Manifestboxen för Live Server (se MS-SSTR) och Movie Box ("moov").  

Varje glesfragment består av en filmfragmentruta ("moof") och Media Data Box ("mdat"), där rutan "mdat" är det binära meddelandet.

För att uppnå bildruterejlig insättning av annonser måste kodaren dela upp fragmentet vid den presentationstid där köeringen måste infogas.  Ett nytt fragment MÅSTE skapas som börjar med en nyskapad IDR-ram, eller Stream Access Points (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I. På så sätt kan Azure Media Packager korrekt generera ett HLS-manifest och ett DASH-flerperiodmanifest där den nya perioden börjar vid den bildruterelevade skarvkonditionerade presentationstiden.

### <a name="221-live-server-manifest-box"></a>2.2.1 Manifestbox för liveserver

Det glesa spåret **MÅSTE** deklareras i rutan Live Server Manifest med en ** \<textstream-post\> ** och **måste** ha följande attribut inställda:

| **Attributnamn** | **Fälttyp** | **Krävs?** | **Beskrivning**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Tal         | Krävs      | **MÅSTE** vara "0", vilket indikerar ett spår med okänd, variabel bithastighet.                                                                                                                                                          |
| parentTrackName    | String         | Krävs      | **MÅSTE** vara namnet på det överordnade spåret, som de glesa spårtidskoderna är tidsskala justerade. Det överordnade spåret kan inte vara ett glest spår.                                                                             |
| manifestUtfällbart     | Boolean        | Krävs      | **MÅSTE** vara "sant", för att ange att det glesa spåret bäddas in i smooth-klientmanifestet.                                                                                                                        |
| Subtyp            | String         | Krävs      | **MÅSTE** vara den fyra teckenkoden "DATA".                                                                                                                                                                                  |
| Schema             | String         | Krävs      | **MÅSTE** vara ett URN eller EN URL som identifierar meddelandeschemat. För [SCTE-35]-meddelanden **måste** detta vara "urn:scte:scte35:2013:bin" för att meddelanden ska kunna skickas till HLS-, Smooth- och Dash-klienter i enlighet med [SCTE-35]. |
| trackName (trackName)          | String         | Krävs      | **MÅSTE** vara namnet på det glesa spåret. TrackName kan användas för att skilja flera händelseströmmar med samma schema. Varje unik händelseström **måste** ha ett unikt spårnamn.                                |
| Tidsskalan          | Tal         | Valfri      | **MÅSTE** vara tidsskalan för det överordnade spåret.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Filmlåda

Movie Box ('moov') följer Live Server Manifest Box som en del av strömhuvudet för ett glest spår.

"moov"-rutan **SKA** innehålla en **TrackHeaderBox -ruta (nedan kallad tkhd)** enligt definitionen i [ISO-14496-12] med följande begränsningar:

| **Fältnamn** | **Fälttyp**          | **Krävs?** | **Beskrivning**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| varaktighet       | 64-bitars osignerat heltal | Krävs      | **BÖR** vara 0, eftersom spårrutan har nollprover och den totala varaktigheten av proverna i spårrutan är 0. |

---

"moov"-rutan **SKA** innehålla en **HandlerBox ("hdlr")** enligt definitionen i [ISO-14496-12] med följande begränsningar:

| **Fältnamn** | **Fälttyp**          | **Krävs?** | **Beskrivning**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32-bitars osignerat heltal | Krävs      | **Bör** vara "meta". |

---

Rutan "stsd" **SKA** innehålla en MetaDataSampleEntry-ruta med kodningsnamn enligt definitionen i [ISO-14496-12].  För SCTE-35-meddelanden **ska** kodningsnamnet till exempel vara "scte".

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Filmfragmentlåda och mediadataruta

Glesa spårfragment består av en filmfragmentruta ("moof") och en mediadataruta (mdat).

> [!NOTE]
> För att uppnå bildruterejlig insättning av annonser måste kodaren dela upp fragmentet vid den presentationstid där köeringen måste infogas.  Ett nytt fragment MÅSTE skapas som börjar med en nyskapad IDR-ram, eller Stream Access Points (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] Bilaga I
> 

Rutan MovieFragmentBox (nedan kallad moof) **MÅSTE** innehålla en **TrackFragmentExtendedHeaderBox ("uuid")** enligt definitionen i [MS-SSTR] med följande fält:

| **Fältnamn**         | **Fälttyp**          | **Krävs?** | **Beskrivning**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64-bitars osignerat heltal | Krävs      | **MÅSTE** vara ankomsttiden för evenemanget. Det här värdet justerar meddelandet med det överordnade spåret.           |
| fragment_duration      | 64-bitars osignerat heltal | Krävs      | **MÅSTE** vara varaktigheten av händelsen. Varaktigheten kan vara noll för att ange att varaktigheten är okänd. |

---


Rutan MediaDataBox (mdat) **måste** ha följande format:

| **Fältnamn**          | **Fälttyp**                   | **Krävs?** | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | 32-bitars osignerat heltal (uimsbf) | Krävs      | Bestämmer formatet på innehållet i rutan "mdat". Okända versioner ignoreras. För närvarande är den enda versionen som stöds 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bitars osignerat heltal (uimsbf) | Krävs      | Identifierar den här instansen av meddelandet. Meddelanden med likvärdig semantik skall ha samma värde. det vill än räcker att bearbeta en händelsemeddelanderuta med samma id.that is, processing any one event message box with the same id is sufficient.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bitars osignerat heltal (uimsbf) | Krävs      | Summan av fragment_absolute_time, som anges i TrackFragmentExtendedHeaderBox, och presentation_time_delta **MÅSTE** vara presentationen tid för händelsen. Presentationstiden och varaktigheten **BÖR** anpassas till Stream Access Points (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I. För HLS-utgående bör tid och **varaktighet** anpassa sig till segmentgränserna. Presentationstiden och varaktigheten för olika händelsemeddelanden i samma händelseström **får** inte överlappa varandra. |
| meddelande                 | bytematris                       | Krävs      | Händelsemeddelandet. För [SCTE-35] meddelanden är meddelandet den binära splice_info_section(). För [SCTE-35]-meddelanden **måste** detta vara splice_info_section() för att meddelanden ska kunna skickas till HLS-, Smooth- och Dash-klienter i enlighet med [SCTE-35]. För [SCTE-35] meddelanden är den binära splice_info_section() nyttolasten för rutan "mdat" och den är **INTE** base64 kodad.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Avbokning och uppdateringar

Meddelanden kan avbrytas eller uppdateras genom att skicka flera meddelanden med samma presentationstid och ID.  Presentationstiden och ID:et identifierar händelsen unikt. Det sista meddelandet som tas emot för en viss presentationstid, som uppfyller pre-roll-begränsningar, är det meddelande som åtgärdas. Det uppdaterade meddelandet ersätter alla tidigare mottagna meddelanden.  Pre-roll-begränsningen är fyra sekunder. Meddelanden som tas emot minst fyra sekunder före presentationstiden kommer att åtgärdas. 


## <a name="3-timed-metadata-delivery"></a>3 Tidsinställd metadataleverans

Händelseströmdata är ogenomskinlig för Media Services. Media Services skickar bara tre delar av informationen mellan den intaste slutpunkten och klientslutpunkten. Följande egenskaper levereras till klienten i enlighet med [SCTE-35] och/eller klientens streamingprotokoll:

1.  Schema – en URN eller URL som identifierar schemat för meddelandet.
2.  Presentationstid – presentationen tid för händelsen på media tidslinjen.
3.  Varaktighet – varaktigheten av händelsen.
4.  ID – en valfri unik identifierare för händelsen.
5.  Meddelande – händelsedata.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft Smooth Streaming Manifest  

Mer information om hur du formaterar ett glest meddelandespår finns i gles spårhantering [MS-SSTR] . För [SCTE35]-meddelanden matas Smooth Streaming ut den base64-kodade splice_info_section() till ett glest fragment.
StreamIndex **MÅSTE** ha en undertyp av "DATA" och CustomAttributes **MÅSTE** innehålla ett attribut med Name="Schema" och Value="urn:scte:scte35:2013:bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Exempel på utjämnat klientmanifest som visar base64-kodad [SCTE35] splice_info_section()
~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 ÄppelHLS Manifest dekoration

Azure Media Services stöder följande HLS-manifesttaggar för signalering av annonsavbegripande information under en live- eller on-demand-händelse. 

- EXT-X-DATERANGE enligt definitionen i Apple HLS [RFC8216]
- EXT-X-CUE enligt definitionen i [Adobe-Primetime] - det här läget anses vara "arv". Kunder bör anta EXT-X-DATERANGE-taggen när det är möjligt.

Datautdata till varje tagg varierar beroende på det intagssignalläge som används. RTMP-inmatning med Adobe Simple-läge innehåller till exempel inte den fullständiga SCTE-35-bas64-kodade nyttolasten.

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 Apple HLS med EXT-X-DATERANGE (rekommenderas)

Apple HTTP Live Streaming [RFC8216] specifikation möjliggör signalering av [SCTE-35] meddelanden. Meddelandena infogas i segmentspellistan i ett EXT-X-DATERANGE-tagg per [RFC8216] med titeln "Mapping SCTE-35 into EXT-X-DATERANGE".  Klientprogramlagret kan tolka M3U-spellistan och bearbeta M3U-taggar, eller ta emot händelserna via Apple Player-ramverket.  

**Den REKOMMENDERADE** metoden i Azure Media Services (version 3 API) är att följa [RFC8216] och använda EXT-X_DATERANGE-taggen för [SCTE35] ad avail dekoration i manifestet.

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 Exempel HLS manifest .m3u8 visar EXT-X-DATERANGE signalering av SCTE-35

Följande exempel HLS-manifestutdata från den dynamiska paketeraren för Media Services visar användningen av EXT-X-DATERANGE-taggen från [RFC8216] som signalerar SCTE-35-händelserna i dataströmmen. Dessutom innehåller den här strömmen den "äldre" EXT-X-CUE-taggen för [Adobe-Primetime].

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS med Adobe Primetime EXT-X-CUE (äldre)

Det finns också en "äldre" implementering som tillhandahålls i Azure Media Services (version 2 och 3 API) som använder EXT-X-CUE-taggen enligt definitionen i [Adobe-Primetime] "SCTE-35 Mode". I det här läget bäddar Azure Media Services in base64-kodade [SCTE-35] splice_info_section() i EXT-X-CUE-taggen.  

Den "äldre" EXT-X-CUE-taggen definieras som nedan och kan även vara normativ refererad i [Adobe-Primetime]-specifikationen. Detta bör endast användas för äldre SCTE35-signalering där det behövs, annars definieras den rekommenderade taggen i [RFC8216] som EXT-X-DATERANGE. 

| **Attributnamn** | **Typ**                      | **Krävs?**                             | **Beskrivning**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cue                | citerad sträng                 | Krävs                                  | Meddelandet kodas som en base64-kodad sträng enligt beskrivningen i [RFC4648]. För [SCTE-35] meddelanden är detta base64-kodade splice_info_section().                                                                                                                                      |
| TYPE               | citerad sträng                 | Krävs                                  | En URN eller URL som identifierar meddelandeschemat. För [SCTE-35] meddelanden tar typen det speciella värdet "scte35".                                                                                                                                                                          |
| ID                 | citerad sträng                 | Krävs                                  | En unik identifierare för händelsen. Om ID:n inte anges när meddelandet används genererar Azure Media Services ett unikt id.                                                                                                                                              |
| Varaktighet           | decimalt flyttalstal | Krävs                                  | Varaktigheten av händelsen. Om det är okänt **ska** värdet vara 0. Enheter är fraktionella sekunder.                                                                                                                                                                                           |
| Förfluten            | decimalt flyttalstal | Valfritt, men obligatoriskt för skjutfönster | När signalen upprepas för att stödja ett glidande presentationsfönster **måste** det här fältet vara den mängd presentationstid som har förflutit sedan händelsen började. Enheter är fraktionerad sekund. Det här värdet kan överstiga den ursprungliga angivna varaktigheten för skarv eller segment. |
| TIME               | decimalt flyttalstal | Krävs                                  | Presentationen tid för händelsen. Enheter är fraktionerad sekund.                                                                                                                                                                                                                        |


HLS-spelarens programlager använder TYP för att identifiera formatet för meddelandet, avkoda meddelandet, tillämpa nödvändiga tidskonverteringar och bearbeta händelsen.  Händelserna är tidssynkronerade i segmentspellistan för det överordnade spåret, enligt händelsetidsstämpeln.  De infogas före närmaste segment (#EXTINF tagg).

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS .m3u8 manifestexempel med "Legacy" Adobe Primetime EXT-X-CUE

I följande exempel visas HLS manifestdekoration med adobe Primetime EXT-X-CUE-taggen.  Parametern "CUE" innehåller endast egenskaperna TYP och Varaktighet, vilket innebär att detta var en RTMP-källa med Adobes "enkla" lägessignalering.  Om detta var en SCTE-35-lägessignal skulle taggen innehålla bas64-kodad binär SCTE-35-nyttolast som visas i [exemplet 3.2.1.1](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 HLS-meddelandehantering för "Legacy" Adobe Primetime EXT-X-CUE

Händelser signaleras i segmentspellistan för varje video- och ljudspår. Positionen för EXT-X-CUE-taggen **måste** alltid vara antingen omedelbart före det första HLS-segmentet (för skarv ut eller segmentstart) eller omedelbart efter det sista HLS-segmentet (för skarv i eller segmentets ände) som dess attribut TID och VARAKTIGHET avser, som krävs av [Adobe-Primetime].

När ett glidande presentationsfönster är aktiverat **måste** EXT-X-CUE-taggen upprepas tillräckligt ofta för att skarven eller segmentet alltid beskrivs fullständigt i segmentspellistan, och förflutet-attributet **MÅSTE** användas för att ange hur lång tid skarv eller segment har varit aktivt, i enlighet med [Adobe-Primetime].

När ett glidande presentationsfönster är aktiverat tas EXT-X-CUE-taggarna bort från segmentspellistan när medietiden som de refererar till har rullat ut ur det glidande presentationsfönstret.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 DASH Manifest dekoration (MPD)

[MPEGDASH] ger tre sätt att signalera händelser:

1.  Händelser som signaleras i MPD-eventstream
2.  Händelser som signaleras i bandet med hjälp av händelsemeddelanderutan ("emsg")
3.  En kombination av både 1 och 2

Händelser som signaleras i MPD EventStream är användbara för VOD-direktuppspelning eftersom klienter har åtkomst till alla händelser, omedelbart när MPD hämtas. Det är också användbart för SSAI-signalering, där SSAI-leverantören i efterföljande led behöver tolka signalerna från ett MPD-manifest med flera perioder och infoga annonsinnehåll dynamiskt.  In-band ('emsg')-lösningen är användbar för livestreaming där klienter inte behöver ladda ner MPD igen, eller om det inte finns någon SSAI-manifestmanipulering mellan klienten och ursprunget. 

Standardbeteendet för AZURE Media Services för DASH är att signalera både i MPD EventStream och i-bandet med hjälp av händelsemeddelanderutan ("emsg").

Cue-meddelanden som förtärs över [RTMP] eller [MS-SSTR-Ingest] mappas till DASH-händelser med hjälp av in-band 'emsg' rutor och/eller i-MPD EventStreams. 

In-band SCTE-35-signalering för DASH följer den definition och de krav som definieras i [SCTE-214-3] och även i [DASH-IF-IOP] avsnitt 13.12.2 (SCTE35 Events). 

För in-band [SCTE-35] vagn, Händelsemeddelande rutan ("emsg") använder schemeId = "urn:scte:scte35:2013:bin". För MPD manifest dekoration EventStream schemeId använder "urn:scte:scte35:2014:xml+bin".  Det här formatet är en XML-representation av händelsen som innehåller en binär base64-kodad utdata från det fullständiga SCTE-35-meddelandet som kom fram till inmatning. 

Normativa referensdefinitioner för transport av [SCTE-35] cue-meddelanden i DASH finns i [SCTE-214-1] sek 6.7.4 (MPD) och [SCTE-214-3] sek 7.3.2 (Transport av SCTE 35 cue-meddelanden).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) EventStream-signalering

Manifest -MPD-dekoration av händelser kommer att signaleras i MPD med elementet EventStream, som visas i elementet Period. Det schema Som används är "urn:scte:scte35:2014:xml+bin".

> [!NOTE]
> För korthetsändamål [SCTE-35] tillåter användning av base64-kodade avsnittet i Signal.Binary element (snarare än Signal.SpliceInfoSection element) som ett alternativ till transport av en helt tolkad cue meddelande.
> Azure Media Services använder den här xml+bin-metoden för att signalera i MPD-manifestet.
> Detta är också den rekommenderade metoden som används i [DASH-IF-IOP] - se avsnittet ["Ad insertion event streams" i DASH IF IOP-riktlinjen](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

Elementet EventStream har följande attribut:

| **Attributnamn** | **Typ**                | **Krävs?** | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | sträng                  | Krävs      | Identifierar schemat för meddelandet. Schemat är inställt på värdet för attributet Scheme i rutan Live Server Manifest. Värdet **SKA** vara ett URN eller EN URL som identifierar meddelandeschemat. Det utdata schemeId som stöds ska vara "urn:scte:scte35:2014:xml+bin" per [SCTE-214-1] sek 6.7.4 (MPD), eftersom tjänsten endast stöder "xml+bin" för tillfället för korthet i MPD. |
| värde              | sträng                  | Valfri      | Ett ytterligare strängvärde som används av ägarna till schemat för att anpassa meddelandets semantik. För att kunna skilja flera händelseströmmar med samma schema **måste** värdet anges till namnet på händelseströmmen (trackName för [MS-SSTR-Ingest] eller AMF-meddelandenamnet för [RTMP]-intårigeten).                                                                         |
| Tidsskala          | 32-bitars osignerat heltal | Krävs      | Tidsskalan, i fästingar per sekund.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 Exempel på händelseströmmar för MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Exempel MPEG DASH .mpd manifest signalering av RTMP streaming med Adobe enkelt läge

Följande exempel visar ett utdrag EventStream från Media Services dynamisk paketerare för en RTMP ström med Adobe "enkel" läge signalering.

~~~ xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Exempel MPEG DASH .mpd manifest signalering av en RTMP ström med Adobe SCTE-35 läge

I följande exempel visas ett utdrag EventStream från Media Services dynamiska paketerare för en RTMP-ström med Adobe SCTE-35-lägessignalering.

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
~~~

> [!IMPORTANT]
> Observera att presentationTime är presentationstiden för [SCTE-35] händelse översatt till att vara relativ till periodens starttid, inte ankomsttiden för meddelandet.
> [MPEGDASH] definierar Event@presentationTime som "Anger presentationens tid för händelsen i förhållande till början av perioden.
> Värdet för presentationstiden i sekunder är fördelningen av värdet för det EventStream@timescale här attributet och värdet för attributet.
> Om det inte finns är värdet för presentationstiden 0.

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Exempel PÅ MPEG DASH-manifest (MPD) med enperiod, EventStream, med hjälp av Adobes enkla lägessignaler

I följande exempel visas utdata från den dynamiska paketeraren för Media Services för en källa RTMP-ström med adsignalmetoden i Adobes "enkla" läge. Utdata är ett enda periodmanifest som visar ett EventStream med schemeId Uri inställd på "urn:com:adobe:dpi:simple:2015" och värdeegenskapen inställd på "simplesignal".
Varje enkel signal tillhandahålls i ett @presentationTime @durationhändelseelement @id med , och egenskaper som fylls baserat på inkommande enkla signaler.

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 Exempel MPEG DASH-manifest (MPD) med flera period, EventStream, med Adobe SCTE35-lägessignalering

I följande exempel visas utdata från den dynamiska paketeraren för Media Services för en källa RTMP-ström med signalen i Adobe SCTE35-läget.
I det här fallet är utdatamanifestet en DASH -mpd @schemeIdUri med flera olika perioder med ett EventStream-element och egenskapen "urn:scte:scte35:2014:xml+bin" och en @value egenskap inställd på "scte35". Varje händelseelement i EventStream innehåller den fullständiga base64-kodade binära SCTE35-signalen 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH In-band Event Message Box Signalering

En händelseström i bandet kräver att MPD har ett InbandEventStream-element på anpassningsuppsättningsnivå.  Det här elementet har ett obligatoriskt schemeIdUri-attribut och valfritt tidskaleattribut, som också visas i händelsemeddelanderutan ("emsg").  Händelsemeddelanderutor med schemaidentifierare som inte har definierats i MPD **bör** inte finnas.

För in-band [SCTE-35] vagn, signaler **måste** använda schemeId = "urn:scte:scte35:2013:bin".
Normativa definitioner av transport av [SCTE-35] in-band-meddelanden definieras i [SCTE-214-3] sek 7.3.2 (Transport av SCTE 35-stacklägen).

Följande detaljer beskriver de specifika värden som klienten bör förvänta sig i "emsg" i enlighet med [SCTE-214-3]:

| **Fältnamn**          | **Fälttyp**          | **Krävs?** | **Beskrivning**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | sträng                  | Krävs      | Identifierar schemat för meddelandet. Schemat är inställt på värdet för attributet Scheme i rutan Live Server Manifest. Värdet **MÅSTE** vara ett URN som identifierar meddelandeschemat. För [SCTE-35] meddelanden **måste** detta vara "urn:scte:scte35:2013:bin" i enlighet med [SCTE-214-3]          |
| Värde                   | sträng                  | Krävs      | Ett ytterligare strängvärde som används av ägarna till schemat för att anpassa meddelandets semantik. För att skilja flera händelseströmmar med samma schema ställs värdet in på namnet på händelseströmmen (trackName for Smooth ingest eller AMF-meddelandenamn för RTMP-intält). |
| Tidsskala               | 32-bitars osignerat heltal | Krävs      | Tidsskalan, i fästingar per sekund, av fälten tider och varaktighet i rutan "emsg".                                                                                                                                                                                                            |
| Presentation_time_delta | 32-bitars osignerat heltal | Krävs      | Tidsdeldelen för mediepresentation för presentationen av händelsen och den tidigaste presentationstiden i det här segmentet. Presentationstiden och varaktigheten **BÖR** anpassas till Stream Access Points (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I.                                  |
| event_duration          | 32-bitars osignerat heltal | Krävs      | Varaktigheten av händelsen, eller 0xFFFFFFFF att ange en okänd varaktighet.                                                                                                                                                                                                                              |
| Id                      | 32-bitars osignerat heltal | Krävs      | Identifierar den här instansen av meddelandet. Meddelanden med motsvarande semantik ska ha samma värde. Om ID:n inte anges när meddelandet används genererar Azure Media Services ett unikt id.                                                                                        |
| Message_data            | bytematris              | Krävs      | Händelsemeddelandet. För [SCTE-35]-meddelanden är meddelandedata den binära splice_info_section() i enlighet med [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Exempel på InBandEvenStream-entitet för Enkelt Adobe-läge
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 HANTERING AV DASH-meddelanden

Händelser signaleras i bandet, inom "emsg" rutan, för både video-och ljudspår.  Signalen sker för alla segmentbegäranden för vilka presentation_time_delta är 15 sekunder eller mindre. 

När ett glidande presentationsfönster är aktiverat tas händelsemeddelanden bort från MPD när summan av tid och varaktighet för händelsemeddelandet är mindre än tiden för mediedata i manifestet.  Med andra ord tas händelsemeddelandena bort från manifestet när medietiden som de refererar till har rullat ut från det glidande presentationsfönstret.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 Introllningsvägledning för kodare Leverantörer

Följande riktlinjer är vanliga problem som kan påverka en kodarleverantörs implementering av den här specifikationen.  Riktlinjerna nedan har samlats in baserat på verkliga partner feedback för att göra det lättare att genomföra denna specifikation för andra. 

[SCTE-35]-meddelanden förtärs i binärt format med formatet **"urn:scte:scte35:2013:bin"** för [MS-SSTR-Ingest] och typen **"scte35"** för [RTMP] intag. För att underlätta konverteringen av [SCTE-35] timing, som baseras på MPEG-2 transportström presentation tidsstämplar (PTS), en kartläggning mellan PTS (pts_time + pts_adjustment av splice_time()) och media tidslinjen tillhandahålls av händelsen presentation tid ( fältet fragment_absolute_time för Smooth ingest och tidsfältet för RTMP-intag). Mappningen är nödvändig eftersom PTS-värdet på 33 bitar rullar över ungefär var 26,5 timme.

Smooth Streaming ingest [MS-SSTR-Ingest] kräver att Media Data Box ("mdat") **måste** innehålla **splice_info_section()** som definieras i [SCTE-35]. 

För RTMP-introllning ställs köattributet för AMF-meddelandet till den base64-kodade **splice_info_section()** som definieras i [SCTE-35].  

När meddelandena har det format som beskrivs ovan skickas de till HLS-, Smooth- och DASH-klienter enligt definitionen ovan.  

När du testar implementeringen med Azure Media Services-plattformen kan du börja testa med en "direkt" LiveEvent först innan du går över till testning på en kodnings-LiveEvent.

---

## <a name="change-history"></a>Ändra historik

| Datum     | Ändringar                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Reviderad RTMP-intag för SCTE35-stöd, tillade RTMP "onCuePoint" för Elemental Live                                  |
| 08/22/19 | Uppdaterad för att lägga till OnUserDataEvent i RTMP för anpassade metadata                                                          |
| 1/08/20  | Fast fel på RTMP Enkel och RTMP SCTE35 läge. Ändrat från "onCuePoint" till "onAdCue". Uppdaterad Tabell med enkelt läge. |

## <a name="next-steps"></a>Nästa steg
Visa utbildningsvägar för Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
