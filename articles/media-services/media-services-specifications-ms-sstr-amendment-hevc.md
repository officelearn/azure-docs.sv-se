---
title: Ändring av Smooth Streaming Protocol (MS-SSTR) för HEVC - Azure
description: Den här specifikationen beskriver protokollet och formatet för fragmenterad MP4-baserad livestreaming med HEVC i Azure Media Services. Endast de ändringar som krävs för att leverera HEVC anges i den här artikeln, förutom att "(Ingen ändring)" anger att texten endast kopieras för förtydligande.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: be4009d418f2f8f3dff755e2e990efee593f070b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514229"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Ändring av Smooth Streaming Protocol (MS-SSTR) för HEVC 

## <a name="1-introduction"></a>1 Inledning 

Den här artikeln innehåller detaljerade ändringar som ska tillämpas på Smooth Streaming Protocol-specifikationen [MS-SSTR] för att möjliggöra jämn strömning av HEVC-kodad video. I den här specifikationen beskriver vi endast de ändringar som krävs för att leverera HEVC-videocodec. Artikeln följer samma numreringsschema som [MS-SSTR]-specifikationen. De tomma rubriker som presenteras i hela artikeln tillhandahålls för att orientera läsaren till sin position i [MS-SSTR] specifikationen.  "(Ingen ändring)" anger att texten endast kopieras för förtydligande.

Artikeln innehåller tekniska implementeringskrav för signalering av HEVC video codec (med antingen "hev1" eller "hvc1" format spår) i en Smooth Streaming manifest och normativa referenser uppdateras för att referera till de nuvarande MPEG-standarder som inkluderar HEVC, gemensam kryptering av HEVC och rutnamn för ISO Base Media File Format har uppdaterats för att vara förenligt med de senaste specifikationerna. 

Den refererade Smooth Streaming Protocol-specifikationen [MS-SSTR] beskriver det trådformat som används för att leverera digitala medier som är aktiva och på begäran, till exempel ljud och video, på följande sätt: från en kodare till en webbserver, från en server till en annan server och från en till en HTTP-klient.
Användningen av en MPEG-4[([MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787)-baserad datastrukturleverans via HTTP möjliggör sömlös växling i nära realtid mellan olika kvalitetsnivåer för komprimerat medieinnehåll. Resultatet är en konstant uppspelningsupplevelse för HTTP-klientens slutanvändare, även om nätverks- och videoåtergivningsvillkoren ändras för klientdatorn eller enheten.

## <a name="11-glossary"></a>1.1 Ordlista 

Följande termer definieras i *[MS-GLOS]*:

>   **globalt unik identifierare (GUID) universellt unik identifierare (UUID)**

Följande termer är specifika för det här dokumentet:

>  **sammansättningstid:** Den tid då ett prov presenteras hos klienten, enligt definitionen i [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695).
> 
>   **CENC**: Gemensam kryptering, enligt definitionen i [ISO/IEC 23001-7] Andra utgåvan.
> 
>   **avkoda tid:** Den tid då ett prov måste avkodas på klienten, enligt definitionen i [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695).

**fragment:** En oberoende nedladdningsbar **medieenhet** som består av ett eller **flera exempel**.

>   **HEVC:** Högeffektiv videokodning enligt definitionen i [ISO/IEC 23008-2]
> 
>   **manifest:** Metadata om **presentationen** som gör att en klient kan göra förfrågningar om **media**. **media:** Komprimerade ljud-, video- och textdata som används av klienten för att spela upp en **presentation**. **medieformat:** Ett väldefinierat format för att representera ljud eller video som ett komprimerat **exempel**.
> 
>   **Presentation:** Uppsättningen av alla **strömmar** och relaterade metadata som behövs för att spela upp en enda film. **begäran:** Ett HTTP-meddelande som skickas från klienten till servern, enligt definitionen i [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **svar:** Ett HTTP-meddelande som skickas från servern till klienten, enligt definitionen i [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **prov:** Den minsta grundläggande enheten (t.ex. en ram) där **mediet** lagras och bearbetas.
> 
>   **MAY, BÖR, BÖR, BÖR INTE, FÅR INTE:** Dessa termer (i versaler) används enligt beskrivningen i [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) Alla satser för valfri beteendeanvändning antingen maj, bör eller inte.

## <a name="12-references"></a>1.2 Referenser

>   Referenser till dokumentationen för Microsoft Open Specifications innehåller inte ett publiceringsår eftersom länkarna är till den senaste versionen av dokumenten, som uppdateras ofta. Referenser till andra dokument inkluderar ett publiceringsår när ett år är tillgängligt.

### <a name="121-normative-references"></a>1.2.1 Normativa referenser 

>  -Jag har inte tid med det här. Smidig streaming protokoll *v20140502*[https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] International Organization for Standardization, "Informationsteknik – Kodning av audiovisuella objekt – Del 12: ISO Base Media File Format", ISO/IEC 14496-12:2014, Edition 4, Plus Rättelse 1, Tillägg 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] International Organization for Standardization, "Informationsteknik – Kodning av audiovisuella objekt – Del 15: Transport av NAL-enhet strukturerad video i ISO Base Media File Format", ISO 14496-15:2015, Utgåva 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Informationsteknik – Högeffektiv kodning och medieleverans i heterogena miljöer - Del 2: Högeffektiv videokodning: 2013 eller nyaste utgåvan<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Informationsteknik – MPEG-systemteknik – Del 7: Gemensam kryptering i ISO-basfilformatfiler, CENC Edition 2:2015<https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "The 'Codecs' och 'Profiles' Parametrar för "Bucket" Media Types"<https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] MP4:s registreringsmyndighet, "MP4REG",[http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "Nyckelord för användning i RFC för att ange kravnivåer", BCP 14, RFC 2119, mars 1997,[https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Informativa referenser 

>   -Jag har inte tid med det här. Microsoft Corporation, "*Windows Protokoll Master Ordlista*."
> 
>   [RFC3548] Josefsson, S., Ed., "The Base16, Base32 och Base64 Data Encodings", RFC 3548, juli 2003,[https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., och Overell, P., "Augmented BNF for Syntax Specifications: ABNF", STD 68, RFC 5234, januari 2008,[https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 Översikt 

>   Endast ändringar av smooth streaming-specifikationen som krävs för leverans av HEVC anges nedan. Oförändrade avsnittsrubriker visas för att behålla platsen i den refererade smooth streaming-specifikationen [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 Förhållande till andra protokoll 

## <a name="15-prerequisitespreconditions"></a>1.5 Förutsättningar/förutsättningar 

## <a name="16-applicability-statement"></a>1.6 Redogörelse för tillämplighet 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Versionshantering och kapacitetsförhandling 

## <a name="18-vendor-extensible-fields"></a>1.8 Utökningsbara fält för leverantör 

>   Följande metod ska användas för att identifiera strömmar med hjälp av HEVC-videoformatet:
> 
>   * **Anpassade beskrivande koder för medieformat:** Den här funktionen tillhandahålls av **fourcc-fältet,** enligt avsnitt *2.2.2.5*.
>   Implementerare kan se till att tillägg inte står i konflikt genom att registrera tilläggskoder med MPEG4-RA, enligt vad som anges i [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 Standardtilldelningar 

## <a name="2-messages"></a>2 Meddelanden 

## <a name="21-transport"></a>2.1 Transport

## <a name="22-message-syntax"></a>2.2 Meddelandesyntax 

### <a name="221-manifest-request"></a>2.2.1 Manifest begäran 

### <a name="222-manifest-response"></a>2.2.2 Manifestsvar 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (variabel):** Delversionen av meddelandet Manifestsvar. MÅSTE ställas in på 2. (Ingen ändring)
> 
>   **Tidsskala (variabel):** Tidsskalan för attributet Duration, som anges som antalet steg på en sekund. Standardvärdet är 
> 1. (Ingen ändring)
> 
>    Det rekommenderade värdet är 90000 för att representera den exakta varaktigheten för videobildrutor och fragment som innehåller fraktionerad framerate video (till exempel 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 Skyddselement 

ProtectionElement ska finnas när gemensam kryptering (CENC) har tillämpats på video- eller ljudströmmar. HEVC-krypterade strömmar ska överensstämma med Common Encryption 2nd Edition [ISO/IEC 23001-7]. Endast segmentdata i VCL NAL-enheter ska krypteras.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (variabel):** Tidsskalan för varaktighets- och tidsvärden i den här strömmen, som anges som antalet steg på en sekund. Ett värde på 90000 rekommenderas för HEVC-strömmar. Ett värde som matchar provfrekvensen för vågform (till exempel 48000 eller 44100) rekommenderas för ljudströmmar.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlMönster 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (variabel):** En kod med fyra tecken som identifierar vilket medieformat som används för varje exempel. Följande värdeintervall reserveras med följande semantiska betydelser:
> 
> * "hev1": Videoprover för detta spår använder HEVC-video med hjälp av "hev1"-exempelbeskrivningsformatet som anges i [ISO/IEC-14496-15].
>
> * "hvc1": Videoprover för detta spår använder HEVC-video med hjälp av det "hvc1"-exempelbeskrivningsformat som anges i [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (variabel):** Data som anger parametrar som är specifika för medieformatet och som är gemensamma för alla exempel i spåret, representerade som en sträng med hexkodade byte. Formatet och den semantiska innebörden av bytesekvensen varierar med värdet i **fourcc-fältet** enligt följande:
> 
>   * När en TrackElement beskriver HEVC-video ska **FourCC-fältet** vara lika **med "hev1"** eller **"hvc1"**
> 
>   **Fältet CodecPrivateData** SKA innehålla en hexkodad strängrepresentation av följande bytesekvens, angiven i ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (ingen ändring från MS-SSTR)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField innehåller sekvensparameteruppsättningen (SPS).
> 
>   * PPSField innehåller PPS (Slice Parameter Set).
> 
>   Videoparameteruppsättningen (VPS) finns inte i CodecPrivateData, men ska finnas i filhuvudet för lagrade filer i rutan "hvcc". System som använder Smooth Streaming Protocol måste signalera ytterligare avkodningsparametrar (till exempel HEVC-nivå) med hjälp av "Codec"-codec-enheter med anpassat attribut".

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   **Fältet SmoothStreamingMedias MajorVersion** MÅSTE anges till 2 och **fältet MinorVersion** måste anges till 2. (Ingen ändring)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 Begäran om fragment 

>   **Obs:** Standardmedieformatet som begärs för **MinorVersion** 2 och "hev1" eller "hvc1" är "iso8" varumärke ISO Base Media File Format som anges i [ISO/IEC 14496-12] ISO Base Media File Format Fourth Edition, och [ISO/IEC 23001-7] Common Second Encryption Second Edition.

### <a name="224-fragment-response"></a>2.2.4 Fragmentrespons 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** är föråldrad och dess funktion ersätts av time boxen för spårfragment ("tfdt") som anges i avsnitt 8.8.12 .
> 
>   **Obs:** En klient kan beräkna varaktigheten för ett fragment genom att summera provvaraktigheterna i rutan Spårkörning ("trunk") eller multiplicera antalet exempel gånger standardprovvaraktigheten. BaseMediaDecodeTime i 'tfdt' plus fragmentvaraktighet är lika med URL-tidsparametern för nästa fragment.
> 
>   En tidsruta för producentreferens ("prft") bör införas före en filmfragmentlåda ("moof") efter behov för att ange utc-tiden som motsvarar spårfragmentets avkodningstid för det första provet som refereras av filmfragmentrutan, enligt [ISO/IEC 14496-12] avsnitt 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** är föråldrad och dess funktion ersätts av time boxen för spårfragment ("tfdt") som anges i avsnitt 8.8.12 .
> 
>   **Obs:** En klient kan beräkna varaktigheten för ett fragment genom att summera provvaraktigheterna i rutan Spårkörning ("trunk") eller multiplicera antalet exempel gånger standardprovvaraktigheten. BaseMediaDecodeTime i 'tfdt' plus fragmentvaraktighet är lika med URL-tidsparametern för nästa fragment. Se framåt-adresser är inaktuella eftersom de försenar livestreaming.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** och relaterade fält kapslar in standardvärden för per exempelmetadata i fragmentet. Syntaxen för **TfhdBox-fältet** är en strikt delmängd av syntaxen i rubrikrutan Spåra fragment som definieras i avsnitt [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) avsnitt 8.8.7.
> 
>   **BaseDataOffset (8 byte):** Förskjutningen, i byte, från början av fältet **MdatBox** till exempelfältet i fältet **MdatBox.** För att signalera den här begränsningen måste flaggan standard-base-is-moof (0x020000) anges.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** och relaterade fält kapslar in per exempelmetadata för det begärda fragmentet. Syntaxen för **TrunBox** är en strikt delmängd av rutan Version 1 Track Fragment Run som definieras i [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* avsnitt 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 byte):** Tidsförskjutningen för provsammansättning för varje prov justeras så att presentationstiden för det första presenterade provet i fragmentet är lika med avkodningstiden för det första avkodade provet. Negativa förskjutningar av videoprovsammansättning ska användas.
> 
>   enligt definitionen i [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Detta undviker ett videosynkroniseringsfel som orsakas av video eftersläpande ljud som är lika med den största avkodade borttagningsfördröjningsfördröjningsfördröjningen för bildbufferten och bibehåller presentationstidtiden mellan alternativa fragment som kan ha olika borttagningsfördröjningar.
> 
>   Syntaxen för de fält som definieras i det här avsnittet, som anges i ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) förblir densamma, förutom följande:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Gemensamma fält för fragmentrespons 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Gles strömpekare 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Fragment ej ännu tillgängligt 

### <a name="227-live-ingest"></a>2.2.7 Levande Ingest 

#### <a name="2271-filetype"></a>2.2.7.1 Filtyp 

>   **FileType (variabel):** anger undertypen och den avsedda användningen av FILEN MPEG-4[([MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) och attribut på hög nivå.
> 
>   **MajorBrand (variabel):** Den stora märke mediefilen. MÅSTE ställas in på "isml".
> 
>   **MinorVersion (variabel):** Den mindre versionen av mediefilen. MÅSTE ställas in på 1.
> 
>   **CompatibleBrands (variabel):** Anger mpeg-4-varumärken som stöds.
>   MÅSTE innehålla "ccff" och "iso8".
> 
>   Syntaxen för de fält som definieras i det här avsnittet, som anges i ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) är följande:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Anmärkning:** Kompatibilitetsmärkena "ccff" och "iso8" anger att fragmenten överensstämmer med "Common Container File Format" och Common Encryption [ISO/IEC 23001-7] och ISO Base Media File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StrömmarMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LevandeMJÖLK 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Utökat sidhuvud för fragment 

### <a name="228-server-to-server-ingest"></a>2.2.8 Server-till-server-introllning 

## <a name="3-protocol-details"></a>3 Protokoll detaljer 


## <a name="31-client-details"></a>3.1 Kundinformation 

### <a name="311-abstract-data-model"></a>3.1.1 Abstrakt datamodell 

#### <a name="3111-presentation-description"></a>3.1.1.1 Presentationsbeskrivning 

>   Dataelementet Presentationsbeskrivning kapslar in alla metadata för presentationen.
> 
>   Presentationsmetadata: En uppsättning metadata som är gemensamma för alla strömmar i presentationen. Presentationsmetadata omfattar följande fält som anges i avsnitt *2.2.2.1:*
> 
> * **MajorVersion (Svenska)**
> * **MinorVersion (MindreVersion)**
> * **Tidsskalan**
> * **Varaktighet**
> * **IsLive (på andra sätt)**
> * **LookaheadCount (olikartade)**
> * **DVRWindowLength**
> 
>   Presentationer som innehåller HEVC-strömmar skall fastställas:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Obs: Rutor inaktuella)
> 
>   Presentationer bör också ställa in:

    TimeScale = 90000

>   Stream Collection: En samling dataelement för flödesbeskrivning, enligt avsnitt *3.1.1.1.2*.
> 
>   Skyddsbeskrivning: En samling dataelement för att beskriva metadatabeskrivning för skyddssystem enligt avsnitt *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Beskrivning av metadata för skyddssystemet 

>   Dataelementet För att skydda metadatabeskrivningen kapslar in metadata som är specifika för ett enda content protection system. (Ingen ändring)
> 
>   Beskrivning av skyddshuvud: Metadata för innehållsskydd som hör till ett enda content protection system. Beskrivning av skyddshuvudet omfattar följande fält som anges i avsnitt *2.2.2.2:*
> 
>   * **SystemID**
>   * **ProtectionHeaderInnehåll**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Strömbeskrivning 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Spårbeskrivning 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Anpassad attributbeskrivning 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Referensbeskrivning för fragment 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Referensbeskrivning för spårspecifikt fragment 

#### <a name="3112-fragment-description"></a>3.1.1.2 Fragmentbeskrivning 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Exempel beskrivning 

### <a name="312-timers"></a>3.1.2 Timers 

### <a name="313-initialization"></a>3.1.3 Initiering 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Utlösta händelser med högre lager 

#### <a name="3141-open-presentation"></a>3.1.4.1 Öppen presentation 

#### <a name="3142-get-fragment"></a>3.1.4.2 Få fragment 

#### <a name="3143-close-presentation"></a>3.1.4.3 Avsluta presentation 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 Regler för behandling av evenemang och sekvensering 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 Manifestbegäran och manifestsvar 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Fragmentbegäran och fragmentsvar

## <a name="32-server-details"></a>3.2 Serverinformation

## <a name="33-live-encoder-details"></a>3.3 Information om livegivare 

## <a name="4-protocol-examples"></a>4 Protokoll exempel 

## <a name="5-security"></a>5 Säkerhet 

## <a name="51-security-considerations-for-implementers"></a>5.1 Säkerhetshänsyn för genomförare

>   Om innehållet som transporteras med det här protokollet har ett högt kommersiellt värde bör ett content protection system användas för att förhindra obehörig användning av innehållet. **ProtectionElement** kan användas för att bära metadata relaterade till användningen av ett content protection system. Skyddat ljud- och videoinnehåll ska krypteras enligt MPEG Common Encryption Second Edition: 2015 [ISO/IEC 23001-7].
> 
>   **För**HEVC-video krypteras endast segmentdata i VCL NALs. Segmentrubriker och andra NALs är tillgängliga för presentationsprogram före dekryptering. i en säker videosökväg är krypterad information inte tillgänglig för presentationsprogram.

## <a name="52-index-of-security-parameters"></a>5.2 Index för säkerhetsparametrar 


| **Parameter för säkerhet**  | **Section**         |
|-------------------------|---------------------|
| Skyddselement       | *2.2.2.2*           |
| Vanliga krypteringsrutor | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 Vanliga krypteringsrutor

Följande rutor kan finnas i fragmentsvar när gemensam kryptering används och anges i [ISO/IEC 23001-7] eller [ISO/IEC 14496-12]:

1.  Specifik rubrikruta för skyddssystem ("pssh")

2.  Exempel på krypteringsruta (senc)

3.  Exempel på förskjutningslåda för extrainformation (saio)

4.  Exempel på hjälpinformationsstorlekslåda ("saiz")

5.  Beskrivningsruta för exempelgrupp (sgpd)

6.  Exempel till gruppruta (sbgp)

---

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
