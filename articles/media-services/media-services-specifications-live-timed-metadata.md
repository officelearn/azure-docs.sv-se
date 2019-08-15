---
title: Timed metadata för Azure Media Services signalering i direktsänd strömning | Microsoft Docs
description: Den här specifikationen beskriver metoder för att signalera Timed metadata vid inmatning och strömning till Azure Media Services. Detta omfattar stöd för allmänna timeed metadata-signaler (ID3), samt SCTE-35-signaler för annons infogning och splice-villkor signaler.
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
ms.date: 07/2/2019
ms.author: johndeu
ms.openlocfilehash: 444d5ca996c014bdbf2e62cacf2563c7b63372e4
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "69015724"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalerar Timed metadata i Live Streaming 

Senast uppdaterad: 2019-07-02

### <a name="conformance-notation"></a>Överensstämmelse notation

Nyckelorden "måste", "måste", "krävs", ",", ",", ",", "ska inte", "ska inte", "rekommenderas", "maj" och "valfritt" i det här dokumentet tolkas enligt beskrivningen i RFC 2119

## <a name="1-introduction"></a>1. Introduktion 

För att signalera infogningen av annonseringar eller anpassade metadata-händelser på en klient spelare använder broadcast-meddelanden ofta inbäddade metadata inbäddade i videon. För att aktivera de här scenarierna ger Media Services stöd för transport av tidshanterade metadata från inmatnings punkten för Live streaming-kanalen till klient programmet.
Den här specifikationen beskriver flera lägen som stöds av Media Services för Timed metadata i direkt uppspelnings signaler.

1. [SCTE-35] signalerar som uppfyller de standarder som beskrivs av [SCTE-35], [SCTE-214-1], [SCTE-214-3] och [RFC8216]

2. [SCTE-35] signalerar som uppfyller den äldre [Adobe-Primetime]-specifikationen för RTMP AD-signalering.
   
3. Ett allmänt timeed metadata Signaling-läge för meddelanden som **inte** är [SCTE-35] och som kan ha [ID3v2] eller andra anpassade scheman som definieras av programutvecklaren.

## <a name="11-terms-used"></a>användnings villkor för 1,1

| Term              | Definition |
|-------------------|------------|
| AD-avbrott          | En plats eller tidpunkt då en eller flera annonser kan schemaläggas för leverans; samma som DISP-och placerings möjlighet. |
| AD-besluts tjänst| extern tjänst som avgör vilka AD (er) och varaktigheter som ska visas för användaren. Tjänsterna tillhandahålls vanligt vis av en partner och är utanför omfånget för det här dokumentet.|
| Utlösare               | Indikering av tid och parametrar för den kommande AD-rasten. Observera att stacken kan indikera en väntande växel till en AD Break, väntar på att växla till nästa AD i en AD Break och väntande växel från en AD Break till huvud innehållet. |
| Packager          | Azure Media Services slut punkt för direkt uppspelning tillhandahåller funktioner för dynamisk paketering för streck-och HLS och kallas för "Paketeraren" i medie branschen. 
| Presentations tid | Den tid som en händelse presenteras för ett visnings program. Tiden visar tidpunkten för den medie tids linje som ett visnings program skulle se händelsen. Till exempel är presentations tiden för ett SCTE-35 splice_info ()-kommando meddelande splice_time (). |
| Införsel tid      | Den tidpunkt då ett händelse meddelande tas emot. Tiden är vanligt vis distinkt från händelsens presentations tid, eftersom händelse meddelanden skickas före händelsens presentations tid.                                     |
| Sparse-spår      | medie spår som inte är kontinuerlig och som är tids synkroniserat med ett överordnat eller kontroll spår.                                                                                                                                    |
| Ursprung            | Tjänsten Azure Media Streaming                                                                                                                                                                                                |
| Kanal mottagare      | Tjänsten Azure Media Live Streaming                                                                                                                                                                                           |
| HLS               | Apple HTTP Live Streaming-protokoll                                                                                                                                                                                               |
| DASH              | Dynamisk adaptiv strömning via HTTP                                                                                                                                                                                             |
| Jämn            | Smooth Streaming protokoll                                                                                                                                                                                                        |
| MPEG2 – TS          | MPEG 2-överförings strömmar                                                                                                                                                                                                         |
| RTMP              | Real tids multimedie protokoll                                                                                                                                                                                                    |
| uimsbf            | Osignerat heltal, mest signifikanta bit först.                                                                                                                                                                                    |

---

## <a name="12-normative-references"></a>1,2 normativa referenser

Följande dokument innehåller bestämmelser, som till följd av referens i denna text utgör bestämmelser i det här dokumentet. Alla dokument är föremål för revidering av standard organen, och läsarna uppmuntras att undersöka möjligheten att tillämpa de senaste versionerna av dokumenten som anges nedan. Läsarna får också en påminnelse om att nyare versioner av de refererade dokumenten kanske inte är kompatibla med den här versionen av den tidsbegränsade metadata-specifikationen för Azure Media Services.


|Standard  |Definition  |
|---------|---------|
|[Adobe-Primetime] | [Primetime Digital program infogning, signal specifikation 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf) |
|[Adobe-Flash-AS] | [Språk referens för FLASH ActionScript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf) |
| [AMF0]            | ["Åtgärds meddelande format AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) |
| [BINDESTRECK-IF-IOP]     | Utlinjes sällskap interop-vägledning v 4,2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html) |
| [HLS-TMD]         | Tidsbaserade metadata för HTTP Live Streaming-[https://developer.apple.com/streaming](https://developer.apple.com/streaming) |
| [ID3v2]           | ID3-tagg version 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure) |
| [ISO-14496-12]    | ISO/IEC 14496-12: Del 12 ISO Base-filformat för media, FourthEdition 2012-07-15  |
| [MPEGDASH]        | Informations teknik – dynamisk anpassningsbar strömning via HTTP (streck) – del 1: Beskrivning av medie presentation och segment format. Maj 2014. Delade. ADRESSER https://www.iso.org/standard/65274.html |
| [MPEGCMAF]        | Informations teknik – multimedie program format (MPEG-A)--del 19: Gemensamt medie program format (CMAF) för segmenterade medier. 2018 januari. Delade. ADRESSER https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Information Technology – MPEG Systems Technologies – del 7: Vanlig kryptering i filer i ISO-huvudfilformat. Februari 2016. Delade. ADRESSER https://www.iso.org/standard/68042.html |
| [MS-SSTR]         | ["Microsoft Smooth Streaming-protokollet", 15 maj 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) |
| [MS-SSTR-intag]  | [Azure Media Services-specifikation för fragmenterad MP4 Live](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview) |
| [RFC8216]         | R. Pantos, ED. W. maj. HTTP Live Streaming. 2017 augusti. Informations. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216) |
| [RFC4648]         |Data kodningarna Base16, Base32 och base64 –[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648) |
| RTMP            |["Adobes real tids meddelande protokoll", den 21 december 2012](https://www.adobe.com/devnet/rtmp.html)  |
| [SCTE-35-2019]    | SCTE 35: 2019-Digital program infogning cueing meddelande för kabel – https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf  |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG-streck för IP-baserade kabel tjänster, del 1: MPD-begränsningar och tillägg |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG-streck för IP-baserade kabel tjänster, del 3: Tank streck/FF-profil |
| [SCTE-224]        | SCTE 224 2018r1 – händelse schema och meddelande gränssnitt |
| [SCTE-250]        | API för händelse-och signal hantering (ESAM) |

---


## <a name="2-timed-metadata-ingest"></a>2. Inmatning av Timed metadata

## <a name="21-rtmp-ingest"></a>2,1 RTMP-inmatning

[RTMP] tillåter att Timed metadata signaler skickas som [AMF0] Cue-meddelanden inbäddade i [RTMP]-strömmen. Stack-meddelanden kan skickas någon gång innan den faktiska händelsen eller [SCTE35] AD splice-signalen måste utföras. För att stödja det här scenariot skickas den faktiska tiden för händelsen i stack-meddelandet. Mer information finns i [AMF0].

I följande tabeller beskrivs formatet på den AMF meddelande nytto last som Media Services kommer att mata in för både "enkla" och [SCTE35]-meddelande lägen.

Namnet på [AMF0]-meddelandet kan användas för att särskilja flera händelse strömmar av samma typ.  För både [SCTE-35]-meddelanden och läget "Simple" måste namnet på AMF-meddelandet vara "onAdCue", vilket krävs i [Adobe-Primetime]-specifikationen.  Fält som inte anges nedan ignoreras av Azure Media Services vid inmatning.

## <a name="211-rtmp-signal-syntax"></a>2.1.1 RTMP-signal-syntax

Azure Media Services kan lyssna efter och svara på flera [AMF0]-meddelande typer som kan användas för att signalera olika synkroniserade metadata i real tid i Live Stream.  [Adobe-Primetime]-specifikationen definierar två Cue-typer som kallas "enkelt" och "SCTE-35"-läge. För "enkelt" läge stöder Media Services ett enda AMF-Cue-meddelande med namnet "onAdCue" med en nytto last som matchar tabellen nedan definierad för signalen "enkel läge".  

I följande avsnitt visas en "enkel" läge "-nytto last som kan användas för att signalera en grundläggande" spliceOut "AD-signal som överförs till klient manifestet för HLS, tank streck och Microsoft Smooth Streaming. Detta är användbart för scenarier där kunden inte har ett komplext SCTE-35-baserat AD-signalerande distributions-eller infognings system och använder en grundläggande lokal kodare för att skicka i Cue-meddelandet via ett API. Den lokala kodaren har vanligt vis stöd för ett REST-baserat API för att utlösa den här signalen, som även kommer att "splice-Condition" video strömmen genom att infoga en IDR-ram i videon och starta en ny GOP.

## <a name="212--simple-mode-ad-signaling-with-rtmp"></a>2.1.2 enkel läge AD Signaling med RTMP

| Fältnamn | Fälttyp | Obligatorisk? | Beskrivningar                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| utlösare        | Sträng     | Obligatorisk | Händelse meddelandet.  Måste vara "SpliceOut" för att ange en enkel läges splice.                                              |
| id         | Sträng     | Obligatorisk | En unik identifierare som beskriver splice eller-segmentet. Identifierar den här instansen av meddelandet                            |
| duration   | Number     | Obligatorisk | Varaktigheten för splice. Enheter är bråkiska sekunder.                                                                |
| elapsed    | Number     | Valfritt | När signalen upprepas för att ge stöd för att ställa in, är det här fältet den mängd av presentationen som har förflutit sedan splice började. Enheter är bråkiska sekunder. När du använder enkelt läge får det här värdet inte överskrida den ursprungliga varaktigheten för splice.                                                  |
| time       | Number     | Obligatorisk | Är tiden för splice i presentations tid. Enheter är bråkiska sekunder.                                     |

---
 
## <a name="213-scte-35-mode-ad-signaling-with-rtmp"></a>2.1.3 SCTE – 35-läge AD Signaling med RTMP

När du arbetar med ett mer avancerat arbets flöde för sändnings produktion som kräver ett fullständigt SCTE-35-nyttolast-meddelande som ska överföras till HLS-eller streck manifestet, är det bäst att använda "SCTE-35 mode" i [Adobe-Primetime]-specifikationen.  Det här läget stöder in-band-SCTE-35-signaler som skickas direkt till en lokal Live-kodare, som sedan kodar signaler ut till RTMP-dataströmmen med "SCTE-35-läge" som anges i [Adobe-Primetime]-specifikationen. 

Vanligt vis kan SCTE-35-meddelanden endast visas i TS-indata (MPEG-2 transport stream) på en lokal kodare. Kontrol lera med din Encoder-tillverkare om du vill ha mer information om hur du konfigurerar en transport stream-inmatning som innehåller SCTE-35 och gör det möjligt för genom strömning till RTMP i Adobe SCTE-35-läge.

I det här scenariot måste följande nytto Last skickas från den lokala kodaren med meddelande typen **"onAdCue"** [AMF0].

| Fältnamn | Fälttyp | Obligatorisk? | Beskrivningar                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| utlösare        | Sträng     | Obligatorisk | Händelse meddelandet.  För [SCTE-35]-meddelanden måste detta vara den base64-kodade [RFC4648] binära splice_info_section () för att meddelanden ska kunna skickas till HLS-, utjämna-och streck-klienter.                                              |
| type       | Sträng     | Obligatorisk | En URN eller URL som identifierar meddelande schemat. För [SCTE-35]-meddelanden **ska** detta vara **"scte35"** för att meddelanden ska kunna skickas till HLS-, utjämna-och streck-klienter, i enlighet med [Adobe-Primetime]. Alternativt kan URN "urn: scte: scte35:2013: bin" också användas för att signalera ett [SCTE-35]-meddelande. |
| id         | Sträng     | Obligatorisk | En unik identifierare som beskriver splice eller-segmentet. Identifierar den här instansen av meddelandet.  Meddelanden med motsvarande semantik måste ha samma värde.|
| duration   | Number     | Obligatorisk | Händelsens eller AD-splice-segmentets varaktighet, om det är känt. Om detta är okänt **ska** värdet vara 0.                                                                 |
| elapsed    | Number     | Valfritt | När AD-signalen [SCTE-35] upprepas för att kunna ställa in, är det här fältet den mängd av presentationen som har förflutit sedan splice började. Enheter är bråkiska sekunder. I läget [SCTE-35] kan det här värdet överskrida den ursprungliga angivna varaktigheten för en splice eller ett segment.                                                  |
| time       | Number     | Obligatorisk | Presentations tiden för händelsen eller AD-splice.  Presentationens tid och varaktighet **bör** justeras mot Stream-åtkomst punkter (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I. För HLS utgående, tid och varaktighet **bör** justeras mot segment gränser. Presentations tiden och varaktigheten för olika händelse meddelanden inom samma händelse ström får inte överlappa varandra. Enheter är bråkiska sekunder.

---
## <a name="214-elemental-live-oncuepoint-ad-markers-with-rtmp"></a>2.1.4S grundämne Live "onCuePoint" AD-markörer med RTMP

Den fördefinierade Live-kodarens lokala kodare stöder AD-markörer i RTMP-signalen. Azure Media Services stöder för närvarande bara AD-markör typen "onCuePoint" för RTMP.  Detta kan aktive ras i Adobe RTMP Group-inställningarna i inställningarna för Media Live Encoder eller API genom att ange "**ad_markers**" till "onCuePoint".  Mer information finns i Live-dokumentationen för grundämne. Om du aktiverar den här funktionen i RTMP-gruppen skickas SCTE-35-signaler till de Adobe RTMP-utdata som ska bearbetas av Azure Media Services.

Meddelande typen "onCuePoint" definieras i [Adobe-Flash-AS] och har följande nytto Last struktur när den skickas från den fördefinierade Live RTMP-utdata.


| Egenskap  |Beskrivning  |
|---------|---------|
|  name     | Namnet måste vara '**scte35**' av typen grundämne Live. |
|time     |  Tiden i sekunder då referens punkten inträffade i video filen under tids linjen |
| type     | Typen av referens punkt ska anges till "**Event**". |
| parameters | En associativ array med namn/värde-par strängar som innehåller information från SCTE-35-meddelandet, inklusive ID och varaktighet. Dessa värden analyseras av Azure Media Services och ingår i dekorations tag gen för manifestet.  |


När det här läget för AD-markör används liknar HLS-Manifestets utdata samma som i Adobe "Simple"-läge. 

### <a name="215-cancellation-and-updates"></a>2.1.5 för annullering och uppdateringar

Meddelanden kan avbrytas eller uppdateras genom att skicka flera meddelanden med samma presentations tid och ID. Presentations tiden och ID: t identifierar händelsen unikt och det senaste meddelandet togs emot för en speciell presentations tid som uppfyller för insamlade begränsningar är det meddelande som har åtgärd ATS. Den uppdaterade händelsen ersätter alla tidigare mottagna meddelanden. För registrerings begränsningen är fyra sekunder. Meddelanden som mottagits minst fyra sekunder innan presentations tiden kommer att behandlas.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2,2-fragmenterad MP4-insugning (Smooth Streaming)

I [MS-SSTR-inmatning] finns krav på Live Stream-inmatning. I följande avsnitt finns information om inläsning av metadata för tids presentationer.  Timed presentations-metadata matas in som ett sparse-spår, som definieras i båda rutan Live Server-manifest (se MS-SSTR) och film rutan (' Moov ').  

Varje sparse-fragment består av en ruta för film fragment (' moof ') och media Data Box-enhet (' mdat '), där rutan ' mdat ' är det binära meddelandet.

För att det ska gå att lägga till en ram-korrekt infogning av annonser måste kodaren dela upp fragmentet vid den tidpunkt då stacken måste infogas.  Ett nytt fragment måste skapas som börjar med en nyligen skapad IDR-ram eller Stream-åtkomst punkter (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I. Detta gör det möjligt för Azure Media-Paketeraren att generera ett HLS-manifest och ett-paket med flera perioder där den nya perioden börjar med den bildruta-korrekta splice-baserade presentations tiden.

### <a name="221-live-server-manifest-box"></a>avsnitt med 2.2.1 Live Server-manifest

Det glesa spåret **måste** deklareras i rutan för Live Server-manifestet med en **\<textstream\>** -post och **måste** ha följande attribut inställda:

| **Attributnamn** | **Fälttyp** | **Kunna?** | **Beskrivning**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | Obligatorisk      | **Måste** vara "0", vilket indikerar ett spår med okänd variabel bit hastighet.                                                                                                                                                                                                 |
| parentTrackName    | Sträng         | Obligatorisk      | **Måste** vara namnet på det överordnade spåret, där tids koderna för den glesa spårnings tids skalan är justerade. Det överordnade spåret får inte vara ett sparse-spår.                                                                                                                    |
| manifestOutput     | Boolesk        | Obligatorisk      | **Måste** vara "true" för att indikera att det glesa spåret ska bäddas in i det smidiga klient manifestet.                                                                                                                                                               |
| Undertyp            | Sträng         | Obligatorisk      | **Måste** vara den fyra tecken koden "data".                                                                                                                                                                                                                        |
| Schema             | Sträng         | Obligatorisk      | **Måste** vara en URN eller URL som identifierar meddelande schemat. För [SCTE-35]-meddelanden **måste** detta vara "urn: SCTE: scte35:2013: bin" för att meddelanden ska kunna skickas till HLS-, utjämna-och streck-klienter i enlighet med [SCTE-35]. |
| trackName          | Sträng         | Obligatorisk      | **Måste** vara namnet på det sparse-spåret. TrackName kan användas för att särskilja flera händelse strömmar med samma schema. Varje unik händelse ström **måste** ha ett unikt spårnings namn.                                                                           |
| timescale          | Number         | Valfritt      | **Måste** vara tids skalan för det överordnade spåret.                                                                                                                                                                                                                      |

---

### <a name="222-movie-box"></a>2.2.2-film Box

Film rutan (' Moov ') följer direkt Server manifest rutan som en del av data Ströms huvudet för ett sparse-spår.

Rutan ' Moov ' **ska** innehålla en **TrackHeaderBox (' tkhd ')** som definieras i [ISO-14496-12] med följande begränsningar:

| **Fält namn** | **Fälttyp**          | **Kunna?** | **Beskrivning**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | 64-bitars osignerat heltal | Obligatorisk      | **Ska** vara 0, eftersom spår rutan innehåller noll exempel och den totala längden för exemplen i spår rutan är 0. |

---

Rutan Moov **ska** innehålla en **-hanterare (hdlr)** enligt definitionen i [ISO-14496-12] med följande begränsningar:

| **Fält namn** | **Fälttyp**          | **Kunna?** | **Beskrivning**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32-bitars osignerat heltal | Obligatorisk      | **Ska** vara meta. |

---

Rutan stsd **ska** innehålla en MetaDataSampleEntry-ruta med ett kodnings namn som definieras i [ISO-14496-12].  För SCTE-35-meddelanden **ska** kodnings namnet till exempel vara ' SCTE '.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3-film fragment Box och media Data Box-enhet

Uppdelade spår fragment består av en ruta för film fragment (' moof ') och en Media Data Box-enhet (' mdat ').

> [!NOTE]
> För att det ska gå att lägga till en ram-korrekt infogning av annonser måste kodaren dela upp fragmentet vid den tidpunkt då stacken måste infogas.  Ett nytt fragment måste skapas som börjar med en nyligen skapad IDR-ram eller Stream-åtkomst punkter (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I
> 

MovieFragmentBox (' moof ') **måste** innehålla en **TrackFragmentExtendedHeaderBox (' UUID ')** -ruta som definieras i [MS-SSTR] med följande fält:

| **Fält namn**         | **Fälttyp**          | **Kunna?** | **Beskrivning**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64-bitars osignerat heltal | Obligatorisk      | **Måste** vara händelsens ankomst tid. Det här värdet justerar meddelandet med det överordnade spåret.   |
| fragment_duration      | 64-bitars osignerat heltal | Obligatorisk      | **Måste** vara händelsens varaktighet. Varaktigheten kan vara noll för att indikera att varaktigheten är okänd. |

---


Rutan MediaDataBox (' mdat ') **måste** ha följande format:

| **Fält namn**          | **Fälttyp**                   | **Kunna?** | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32-bitars osignerat heltal (uimsbf) | Obligatorisk      | Anger formatet för innehållet i rutan "mdat". Okända versioner kommer att ignoreras. För närvarande är den enda version som stöds 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bitars osignerat heltal (uimsbf) | Obligatorisk      | Identifierar den här instansen av meddelandet. Meddelanden med motsvarande semantik måste ha samma värde. Det räcker med att bearbeta en händelse meddelande ruta med samma ID.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bitars osignerat heltal (uimsbf) | Obligatorisk      | Summan av fragment_absolute_time, som anges i TrackFragmentExtendedHeaderBox och presentation_time_delta **måste** vara presentations tiden för händelsen. Presentationens tid och varaktighet **bör** justeras mot Stream-åtkomst punkter (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I. För HLS utgående, tid och varaktighet **bör** justeras mot segment gränser. Presentations tiden och varaktigheten för olika händelse meddelanden inom samma händelse ström **får** inte överlappa varandra. |
| message                 | bytematris                       | Obligatorisk      | Händelse meddelandet. För [SCTE-35] meddelanden är meddelandet binärfilen splice_info_section (). För [SCTE-35]-meddelanden **måste** detta vara splice_info_section () för att meddelanden ska kunna skickas till HLS-, utjämna-och streck-klienter i enlighet med [SCTE-35]. För [SCTE-35]-meddelanden är binärfilen splice_info_section () nytto lasten för rutan "mdat" och den är **inte** Base64-kodad.                                                            |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 för annullering och uppdateringar

Meddelanden kan avbrytas eller uppdateras genom att skicka flera meddelanden med samma presentations tid och ID.  Presentationens tid och ID identifierar händelsen unikt. Det sista meddelandet som togs emot för en speciell presentations tid, som uppfyller för hands versioner, är det meddelande som har åtgärd ATS. Det uppdaterade meddelandet ersätter alla tidigare mottagna meddelanden.  För registrerings begränsningen är fyra sekunder. Meddelanden som mottagits minst fyra sekunder innan presentations tiden kommer att behandlas. 


## <a name="3-timed-metadata-delivery"></a>3 Timed metadata-leverans

Händelse data strömmen är ogenomskinlig för Media Services. Media Services skickar bara tre delar av information mellan inmatnings slut punkten och klient slut punkten. Följande egenskaper levereras till klienten, i enlighet med [SCTE-35] och/eller klientens strömnings protokoll:

1.  Schema – en URN eller URL som identifierar meddelandets schema.
2.  Presentations tid – händelsens presentations tid på medie tids linjen.
3.  Duration – händelsens varaktighet.
4.  ID – en valfri unik identifierare för händelsen.
5.  Meddelande – händelse data.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 Microsoft Smooth Streaming-manifest  

Mer information om hur du formaterar ett reoptimerat meddelande spår finns i hantering av glesa spår [MS-SSTR]. För [SCTE35]-meddelanden kommer Smooth Streaming att mata ut base64-kodade splice_info_section () till ett sparse-fragment.
StreamIndex **måste** ha en undertyp till "data" och CustomAttributes **måste** innehålla ett attribut med Name = "schema" och värde = "urn: scte: scte35:2013: bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Exempel på ett smidigt klient manifest som visar base64-kodade [SCTE35] splice_info_section ()
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
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3,2 Apple HLS-manifest

Azure Media Services stöder följande HLS manifest-taggar för att signalera AD via-information under en Live-eller on-demand-händelse. 

- EXT-X-DATERANGE som definieras i Apple HLS [RFC8216]
- EXT-X-CUE enligt definitionen i [Adobe-Primetime] – det här läget anses vara "Bakåtkompatibelt". Kunderna bör anta en EXT-X-DATERANGE-tagg när det är möjligt.

Datautdata till varje tagg varierar beroende på vilket inmatnings signal läge som används. Exempelvis innehåller RTMP-inmatningen med Adobe Simple mode inte den fullständiga SCTE-35 base64-kodade nytto lasten.

## <a name="321-apple-hls-with-adobe-primetime-ext-x-daterange-recommended"></a>3.2.1 äpple HLS med Adobe Primetime EXT-X-DATERANGE (rekommenderas)

I specifikationen Apple HTTP Live Streaming [RFC8216] kan du signalera [SCTE-35]-meddelanden. Meddelandena infogas i listan över segment i segment listan i en EXT-X-DATERANGE-tagg per [RFC8216]-avsnitt med rubriken "Mappa SCTE-35 till EXT-X-DATERANGE".  Klient program skiktet kan parsa M3U-spelnings listan och bearbeta M3U-taggarna, eller ta emot händelserna via Apple Player-ramverket.  

Den **rekommenderade** metoden i Azure Media Services (version 3 API) är att följa [RFC8216] och använda ext-X_DATERANGE-taggen för [SCTE35] AD-DISP i manifestet.

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple-HLS med Adobe Primetime EXT-X-CUE (bakåtkompatibelt)

Det finns också en "äldre" implementering som tillhandahålls i Azure Media Services (version 2 och 3 API) som använder taggen EXT-X-CUE enligt definitionen i [Adobe-Primetime] "SCTE-35-läge". I det här läget kommer Azure Media Services att bädda in Base64-kodat [SCTE-35] splice_info_section () i taggen EXT-X-CUE.  

Taggen "Legacy" EXT-X-CUE definieras enligt nedan och kan också vara normativ som refereras till i [Adobe-Primetime]-specifikationen. Detta bör endast användas för äldre SCTE35-signaler vid behov, annars definieras den rekommenderade taggen i [RFC8216] som EXT-X-DATERANGE. 

| **Attributnamn** | **Typ**                      | **Kunna?**                             | **Beskrivning**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| UTLÖSARE                | Citerad sträng                 | Obligatorisk                                  | Meddelandet kodas som en Base64-kodad sträng enligt beskrivningen i [RFC4648]. För [SCTE-35]-meddelanden är detta base64-kodade splice_info_section ().                                                                                                |
| TYP               | Citerad sträng                 | Obligatorisk                                  | En URN eller URL som identifierar meddelande schemat. För [SCTE-35]-meddelanden använder typen det särskilda värdet "scte35".                                                                                                                                |
| id                 | Citerad sträng                 | Obligatorisk                                  | En unik identifierare för händelsen. Om ID: t inte anges när meddelandet matas in kommer Azure Media Services att generera ett unikt ID.                                                                                                                                          |
| GILTIGHET           | decimalt flytt ALS nummer | Obligatorisk                                  | Händelsens varaktighet. Om detta är okänt **ska** värdet vara 0. Enheter är factional sekunder.                                                                                                                                                                                           |
| FÖRFLUTEN            | decimalt flytt ALS nummer | Valfritt, men krävs för glidande fönster | När signalen upprepas för att stödja ett glidande presentations fönster **måste** det här fältet vara den mängd av presentationen som har förflutit sedan händelsen började. Enheter är bråkiska sekunder. Det här värdet kan överskrida den ursprungliga angivna varaktigheten för en splice eller ett segment. |
| TID               | decimalt flytt ALS nummer | Obligatorisk                                  | Händelsens presentations tid. Enheter är bråkiska sekunder.                                                                                                                                                                                                                    |


HLS Player-Programskiktet använder typen för att identifiera meddelandets format, avkoda meddelandet, tillämpa nödvändiga tids konverteringar och bearbeta händelsen.  Händelserna är tids synkroniserade i segment listan över överordnade spår enligt händelsens tidsstämpel.  De infogas före närmaste segment (#EXTINF tagg).

### <a name="323-hls-segment-playlist-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS segment Playlist exempel med "Legacy" Adobe Primetime EXT-X-CUE

I följande exempel visas HLS-manifestet med hjälp av taggen Adobe Primetime EXT-X-CUE.  Parametern "CUE" innehåller den fullständiga base64-kodade SCTE-35-splice_info-nytto lasten som anger att den här signalen kom i användning av RTMP i Adobe SCTE-35 signal läge, eller att den kom in via Smooth Streaming SCTE-35 signal läge. 

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 HLS-meddelande hantering för "Legacy" Adobe Primetime EXT-X-CUE

Händelser signaleras i listan över segment för varje video-och ljud spår. Positionen för EXT-X-CUE-taggen **måste** alltid vara omedelbart före det första HLS-segmentet (för splice-ut-eller segment start) eller omedelbart efter det sista HLS-segmentet (för splice in-eller segment slut) som dess Time-och duration-attribut refererar till, som krävs av [Adobe-Primetime].

När ett glidande presentations fönster är aktiverat **måste** ext-X-Cue-taggen upprepas så ofta att splice eller segmentet alltid är fullständigt beskrivet i segment listan och det förflutna attributet **måste** användas för att ange hur lång tid som splice eller segment har Aktiver ATS, vilket krävs av [Adobe-Primetime].

När ett glidande presentations fönster är aktiverat tas EXT-X-CUE-taggarna bort från listan över segment när medie tiden som de hänvisar till har lyfts ut från fönstret med den glidande presentationen.

## <a name="33-dash-manifest-decoration-mpd"></a>3,3 STRECKs manifest dekoration (MPD)

[MPEGDASH] tillhandahåller tre sätt att signalera händelser:

1.  Händelser som signaleras i MPD-EventStream
2.  Händelser signalerade på-band med hjälp av händelse meddelande rutan ("EMSG")
3.  En kombination av både 1 och 2

Händelser som har signaler ATS i MPD-EventStream är användbara för VOD-direktuppspelning eftersom klienterna har åtkomst till alla händelser omedelbart när MPD laddas ned. Det är också användbart för SSAI-signaler, där den underordnade SSAI-leverantören behöver parsa signalerna från ett MPD-manifest med flera perioder och infoga AD-innehåll dynamiskt.  Lösningen in-band (' EMSG ') är användbar för Live-direktuppspelning där klienterna inte behöver hämta MPD igen, eller så finns det ingen SSAI för manifest manipulering mellan klienten och ursprunget. 

Azure Media Services standard beteendet för bindestreck är att signalera både i MPD-EventStream och i-band med hjälp av händelse meddelande rutan ("EMSG").

Stack meddelanden som matats in över [RTMP] eller [MS-SSTR-inmatning] mappas till streck händelser, med hjälp av EMSG-rutorna och/eller i MPD-EventStreams. 

In-band-SCTE – 35 signalering för streck följer definitionen och kraven som definierats i [SCTE-214-3] och även i [streck-IF-IOP] avsnitt 13.12.2 ("SCTE35 Events"). 

För in-band-[SCTE-35]-transport använder händelse meddelande rutan ("EMSG") schemeId = "urn: SCTE: scte35:2013: bin". För dekoration av MPD-manifest EventStream-schemeId använder "urn: scte: scte35:2014: XML + bin".  Det här formatet är en XML-representation av händelsen som innehåller en binär Base64-kodad utdata från det fullständiga SCTE-35-meddelandet som kom in vid inmatningen. 

Normativ referens definitioner för transport av [SCTE-35] Cue-meddelanden i bindestreck är tillgängliga i [SCTE-214-1] SEK 6.7.4 (MPD) och [SCTE-214-3] SEC 7.3.2 (transport av SCTE 35 Cue-meddelanden).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG-streck (MPD) EventStream-signalering

Ett manifest (MPD) av händelser kommer att signaleras i det här MPD-objektet med hjälp av EventStream-elementet, som visas i period-elementet. SchemeId som används är "urn: scte: scte35:2014: XML + bin".

> [!NOTE]
> I det kortfattat-syfte [SCTE-35] kan använda det Base64-kodade avsnittet i signal. Binary-element (i stället för signal-SpliceInfoSection-elementet) som ett alternativ till att transportera ett fullständigt parsat Cue-meddelande.
> Azure Media Services använder den här metoden "XML + bin" för att signalera i MPD-manifestet.
> Detta är även den rekommenderade metoden som används i [bindestreck-IF-IOP] – Se avsnittet ["AD-infogning händelse strömmar" i bindestrecket om IOP-rikt linjen](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

EventStream-elementet har följande attribut:

| **Attributnamn** | **Typ**                | **Kunna?** | **Beskrivning**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | sträng                  | Obligatorisk      | Identifierar meddelandets schema. Schemat anges till värdet för attributet Scheme i rutan Live Server manifest. Värdet **måste** vara en URN eller URL som identifierar meddelande schemat. De utdata-schemeId som stöds ska vara "urn: scte: scte35:2014: XML + bin" per [SCTE-214-1] SEK 6.7.4 (MPD), eftersom tjänsten endast stöder "XML + bin" för närvarande för det kortfattat i MPD.  |
| value              | sträng                  | Valfritt      | Ett ytterligare sträng värde som används av schemats ägare för att anpassa semantiken för meddelandet. För att kunna särskilja flera händelse strömmar med samma schema **måste** värdet anges till namnet på händelse strömmen (trackName for [MS-SSTR-inmatning] eller AMF-för [RTMP] intag). |
| Timescale          | 32-bitars osignerat heltal | Obligatorisk      | Tids skalan i Tick per sekund.                                                                                                                                                                                                       |


### <a name="332-example-mpeg-dash-manifest-mpd-signaling-of-scte-35-using-eventstream"></a>3.3.2 exempel på MPEG streck manifest (MPD) som signalerar SCTE-35 med EventStream

~~~ xml
<!-- Example MPD using xml+bin style signaling per [SCTE-214-1] -->
  <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">
            <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">
           <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
    </EventStream>
~~~

> [!IMPORTANT]
> Observera att presentationTime är presentations tiden för händelsen [SCTE-35] översatt till att vara relativ till periodens start tid, inte för meddelandets införsel tid.
> [MPEGDASH] definierar Event@presentationTime som "anger presentations tiden för händelsen i förhållande till början av perioden.
> Värdet för presentations tiden i sekunder är indelningen av värdet för det här attributet och EventStream@timescale attributets värde.
> Om detta inte anges är värdet för presentations tiden 0.


### <a name="333-mpeg-dash-in-band-event-message-box-signaling"></a>Varning för 3.3.3 MPEG-streck i band

En inbands händelse ström kräver att MPD har ett InbandEventStream-element på anpassnings uppsättnings nivån.  Det här elementet har ett obligatoriskt schemeIdUri-attribut och valfritt Tidsskale-attribut som också visas i händelse meddelande rutan (' EMSG ').  Händelse meddelande rutor med schema identifierare som inte har definierats i MPD **ska** inte finnas.

För in-band-[SCTE-35]-transport **måste** signaler använda schemeId = "urn: SCTE: scte35:2013: bin".
Normativa definitioner för transport av [SCTE-35] in-band-meddelanden definieras i [SCTE-214-3] s 7.3.2 (transport av SCTE 35 Cue-meddelanden).

Följande information beskriver de angivna värdena som klienten ska förvänta sig i EMSG i enlighet med [SCTE-214-3]:

| **Fält namn**          | **Fälttyp**          | **Kunna?** | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | sträng                  | Obligatorisk      | Identifierar meddelandets schema. Schemat anges till värdet för attributet Scheme i rutan Live Server manifest. Värdet **måste** vara en URN som identifierar meddelande schemat. För [SCTE-35]-meddelanden **måste** detta vara "urn: SCTE: scte35:2013: bin" i enlighet med [SCTE-214-3] |
| Value                   | sträng                  | Obligatorisk      | Ett ytterligare sträng värde som används av schemats ägare för att anpassa semantiken för meddelandet. För att skilja flera händelse strömmar med samma schema, anges värdet för händelse strömmen (trackName för utjämna inmatning eller AMF meddelande namn för RTMP-inmatning).                                                                  |
| Timescale               | 32-bitars osignerat heltal | Obligatorisk      | Tids skalan, i Tick per sekund, för fälten tid och varaktighet i rutan "EMSG".                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32-bitars osignerat heltal | Obligatorisk      | Medie presentationens tids förändring av händelsens presentations tid och den tidigaste presentations tiden i det här segmentet. Presentationens tid och varaktighet **bör** justeras mot Stream-åtkomst punkter (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I.                                                                                            |
| event_duration          | 32-bitars osignerat heltal | Obligatorisk      | Händelsens varaktighet eller 0xFFFFFFFF för att ange en okänd varaktighet.                                                                                                                                                                                                                                                                                          |
| ID                      | 32-bitars osignerat heltal | Obligatorisk      | Identifierar den här instansen av meddelandet. Meddelanden med motsvarande semantik måste ha samma värde. Om ID: t inte anges när meddelandet matas in kommer Azure Media Services att generera ett unikt ID.                                                                                                                                                    |
| Message_data            | bytematris              | Obligatorisk      | Händelse meddelandet. För [SCTE-35] meddelanden är meddelande data den binära splice_info_section () i enlighet med [SCTE-214-3] |

### <a name="334-dash-message-handling"></a>Meddelande hantering för 3.3.4-datastreck

Händelser signaleras i band i rutan "EMSG" för både video-och ljud spår.  Signaleringen sker för alla segment förfrågningar som presentation_time_delta är 15 sekunder eller mindre. 

När ett glidande presentations fönster är aktiverat tas händelse meddelanden bort från MPD när summan av tid och varaktighet för händelse meddelandet är mindre än tiden för medie data i manifestet.  Med andra ord tas händelse meddelandena bort från manifestet när medie tiden som de hänvisar till har lyfts ut från fönstret med den glidande presentationen.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE – 35 inmatnings vägledning för kodare-leverantörer

Följande rikt linjer är vanliga problem som kan påverka en kodares leverantörs implementering av den här specifikationen.  Rikt linjerna nedan har samlats in baserat på verklig feedback från vår partner för att göra det enklare att implementera den här specifikationen för andra. 

[SCTE-35] meddelanden matas in i binärformat med schemat **"urn: SCTE: scte35:2013: bin"** för [MS-SSTR-inmatning] och typen **"scte35"** för [RTMP] intag. För att under lätta konverteringen av [SCTE-35]-timing, som baseras på tidsstämplar för MPEG-2 transport data strömmar (punkter), en mappning mellan punkter (pts_time + pts_adjustment för splice_time ()) och medie tids linjen tillhandahålls av händelse presentations tiden ( fältet fragment_absolute_time för utjämna inmatning och tids fältet för RTMP-inmatningar. Mappningen är nödvändig eftersom värdet 33-bit punkter rullar över ungefär var 26,5: e timme.

Smooth Streaming-inmatningen [MS-SSTR-inmatning] kräver att medie Data Box-enhet (' mdat ') **måste** innehålla **splice_info_section ()** som definierats i [SCTE-35]. 

För RTMP-inläsning anges attributet Cue för AMF-meddelandet till Base64-kodad **splice_info_section ()** som definierats i [SCTE-35].  

När meddelandet har det format som beskrivs ovan skickas de till HLS-, utjämna-och streck-klienter enligt definitionen ovan.  

När du testar din implementering med Azure Media Services plattform börjar du testa med en "direkt" LiveEvent innan du går vidare till testning på en kodnings LiveEvent.

---

## <a name="next-steps"></a>Nästa steg
Visa Media Services utbildnings vägar.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
