---
title: Azure Media Services - Smooth Streaming Protocol (MS-SSTR) ändring för HEVC | Microsoft Docs
description: Den här specifikationen beskriver protokoll och format för fragmenterad MP4-baserade direktsänd strömning med HEVC i Azure Media Services. Detta är en ändring av protokollet Smooth Streaming documentaiton (MS-SSTR) med stöd för infognings-HEVC och strömning. Endast de ändringar som krävs för att leverera HEVC har angetts i den här artikeln, förutom har ”(ingen ändring)” anger texten kopieras för endast information.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: johndeu;
ms.openlocfilehash: 7b4e1d933040225445e83d6e507cdee29a8f2363
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Smooth Streaming Protocol (MS-SSTR) ändring för HEVC

## <a name="1-introduction"></a>1 introduktion 

Den här artikeln innehåller detaljerade ändringar tillämpas på specifikationen Smooth Streaming Protocol [MS-SSTR] att aktivera Smooth Streaming av HEVC kodad video. I den här specifikationen beskriver vi bara ändringarna som krävs för att leverera video-codec HEVC. Artikeln följer samma numrering schema som [MS-SSTR]-specifikation. Tom rubriker som visas i hela artikeln finns om du vill att läsaren till sin position i [MS-SSTR]-specifikationen.  ”(Ingen ändring)” anger texten kopieras endast för information.

Artikeln innehåller teknisk implementeringskrav för signalering HEVC video-codec i ett Smooth Streaming-manifest och normativa referenser uppdateras för att referera till de aktuella MPEG-standarder som inkluderar HEVC, vanliga kryptering HEVC och rutan namn för ISO Base Mediaformatet har uppdaterats för att stämma överens med de senaste specifikationerna. 

Refererade Smooth Streaming specifikationen av protokollet [MS-SSTR] beskriver det kabelformat som används för att leverera live och på begäran digitala media, till exempel ljud och video, på följande sätt: från en kodare till en webbserver från en server till en annan server och från en Server för att en HTTP-klient.
Användning av en MPEG-4 ([[MPEG4 RA])](http://go.microsoft.com/fwlink/?LinkId=327787)-baserade struktur dataöverföring via HTTP kan sömlös växlar i nära realtid mellan olika kvalitet på komprimerade medieinnehåll. Resultatet är en konstant uppspelningsupplevelse för slutanvändaren för http-klienten, även om nätverket och video återgivning villkor ändrar för klientdatorer eller enheter.

## <a name="11-glossary"></a>1.1-ordlista 

Följande villkor är definierade i *[MS-GLOS]*:

>   **globalt unik identifierare (GUID) universellt Unik identifierare (UUID)**

Följande villkor är specifika för det här dokumentet:

>  **tid för sammansättning:** tid ett prov visas på klienten, som definierats i [[ISO/IEC-14496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

>   **CENC**: Common Encryption som definierats i [ISO/IEC 23001 7], andra utgåvan.

>   **avkoda tid:** tid ett exempel krävs för att avkoda på klienten, som definierats i [[ISO/IEChttp://go.microsoft.com/fwlink/?LinkId=18369514496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

**fragment:** en separat nedladdningsbara enhet **media** som består av en eller flera **exempel**.

>   **HEVC:** hög effektivitet Video kodning, enligt definitionen i [ISO/IEC 23008-2]

>   **manifest:** Metadata om den **presentation** som gör det möjligt för en klient begär **media**. **Media:** komprimerade data för ljud, video och text som används av klienten för att spela upp en **presentation**. **Media format:** en väldefinierad format för att representera ljud- eller som en komprimerad **exempel**.

>   **presentation:** uppsättningen av alla **dataströmmar** och tillhörande metadata som krävs för att spela en enda film. **begäran:** en HTTP-meddelande som skickas från klienten till servern, enligt definitionen i [[RFC2616].](http://go.microsoft.com/fwlink/?LinkId=90372) **svaret:** en HTTP-meddelande som skickas från servern till klienten, som definierats i [[RFC2616].](http://go.microsoft.com/fwlink/?LinkId=90372)

>   **Exempel:** den minsta grundläggande enheten (till exempel en ram) som **media** lagras och bearbetas.

>   **FÅR SHOULD, måste, bör inte får inte:** används dessa termer (i versaler) enligt beskrivningen i [[RFC2119].](http://go.microsoft.com/fwlink/?LinkId=90317) Alla instruktioner för valfritt beteende användning kan antingen SHOULD eller bör inte.

## <a name="12-references"></a>1.2-referenser 
-----------

>   Referenser till öppna specifikationer för Microsoft-dokumentationen innehåller inte ett publishing år eftersom länkar går till den senaste versionen av dokument, som uppdateras ofta. Referenser till andra dokument innehåller ett publishing år när någon är tillgänglig.

 ### <a name="121-normative-references"></a>1.2.1 normativa referenser 

>  [MS-SSTR] Utjämna strömning protokollet *v20140502* [ http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/[MS-SSTR] PDF](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

>   [ISO/IEC 14496 12] International Organization for Standardization ”, IT - kodning av audiovisuella objekt--del 12: ISO grundläggande Mediaformatet”, ISO/IEC 14496-12:2014, version 4, Plus korrigering 1, ändringarna 1 och 2.
>   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>

>   [ISO/IEC 14496-15] International Organization for Standardization ”, IT - kodning av audiovisuella objekt--del 15: transport av NAL enhet strukturerad video i filformatet ISO grundläggande Media”, ISO 14496-15:2015, version 3.
>   <http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>

>   [ISO/IEC 23008-2] IT - högeffektiv kodning och media leverans i heterogena miljöer--del 2: högeffektiv video kodning: 2013 eller senaste version   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>

>   [ISO/IEC 23001 7] IT – MPEG system tekniker – del 7: Common encryption i ISO basera media file format-filer, CENC Edition 2:2015 <http://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>

>   [RFC 6381] IETF RFC 6381 ”'Codec' och 'Profiler' parametrar för” Bucket ”medietyper” <http://tools.ietf.org/html/rfc6381>

>   [MPEG4 RA] MP4-registreringsutfärdare ”MP4REG” [http://www.mp4ra.org   ](http://go.microsoft.com/fwlink/?LinkId=327787)

>   [RFC2119] Bradner S., ”nyckelord för användning i RFC: er för att ange krav nivåer”, BCP 14, RFC 2119, mars 1997   [http://www.rfc-editor.org/rfc/rfc2119.txt   ](http://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 informativa referenser 

>   [MS-GLOS] Microsoft Corporation ”*Windows standardprotokoll Master ordlista*”.

>   [RFC3548] Josefsson S., Ed. ”den Base16 Base32 och Base64-Data kodningar”, RFC 3548, juli 2003 [http://www.ietf.org/rfc/rfc3548.txt   ](http://go.microsoft.com/fwlink/?LinkId=90432)

>   [RFC5234] Crocker D., Ed., och Overell P. ”, förstärkta BNF för Syntax specifikationer: ABNF”, STD 68, RFC 5234, januari 2008   [http://www.rfc-editor.org/rfc/rfc5234.txt   ](http://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3-översikt 
---------

>   Endast ändringar Smooth Streaming-specifikationen som krävs för leverans av HEVC anges nedan. Oförändrat avsnittsrubrik visas för att underhålla plats i refererade Smooth Streaming-specifikationen [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 relation till andra protokoll 
--------------------------------

## <a name="15-prerequisitespreconditions"></a>1.5 krav/villkor 
----------------------------

## <a name="16-applicability-statement"></a>1.6 tillämplighet instruktionen 
------------------------

## <a name="17-versioning-and-capability-negotiation"></a>1.7 versionshantering och kapaciteten förhandling 
--------------------------------------

## <a name="18-vendor-extensible-fields"></a>1.8 Extensible leverantör fält 
-------------------------

>   Följande metod för att användas identifiera dataströmmar i formatet HEVC video:

>   * **Anpassade beskrivande koder för Media-format:** den här funktionen tillhandahålls av den **FourCC** fält som anges i avsnittet *2.2.2.5*.
>   Administratörer kan se till att tilläggen inte står i konflikt genom att registrera tillägget koder med MPEG4-RA, som anges i [[ISO/IEC-14496-12] ](http://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 standarder tilldelningar 
----------------------

# <a name="2-messages"></a>2 meddelanden 

## <a name="21-transport"></a>2.1 transport 
----------

## <a name="22-message-syntax"></a>2.2 meddelandet Syntax 
---------------

### <a name="221-manifest-request"></a>2.2.1 manifest begäran 

### <a name="222-manifest-response"></a>2.2.2 manifest svar 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (variabel):** den lägre versionen av Manifest svarsmeddelandet. MÅSTE anges till 2. (Ingen ändring)

>   **Tidsrymd (variabel):** skala för attributet varaktighet som angetts som antalet steg i en sekund. Standardvärdet är
>   10000000. (Ingen ändring)

>   Rekommenderat värde är 90000 för att representera exakt varaktighet bildrutor och fragment som innehåller bråkdelar ramhastighet video (till exempel 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

ProtectionElement skall finnas när vanliga kryptering (CENC) har tillämpats på video eller ljud dataströmmar. HEVC krypterade dataströmmar överensstämma med Common Encryption 2 Edition [ISO/IEC 23001 7]. Sektorn data i VCL NAL enheter ska krypteras.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (variabel):** skala varaktighet och tid värdena i den här dataströmmen som antalet steg i en sekund. Ett värde av 90000 rekommenderas för HEVC dataströmmar. Ett värde som matchar samplingsfrekvens Wave (till exempel 48000 eller 44100) rekommenderas för ljudströmmar.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (variabel):** en kod med fyra tecken som identifierar vilka medieformat används för varje prov. Följande värdeintervallet reserveras med följande semantiska innebörd:

>  * ”hev1”: Video prover för spåret använda HEVC video, i formatet 'hev1' exempel beskrivning anges i [ISO/IEC-14496-15].

>   **CodecPrivateData (variabel):** Data som anger parametrarna för media format och gemensamma för alla prover för spåret representeras som en sträng med hex-kodad byte. Formatera och semantiska betydelse byte-sekvens varierar beroende på värdet för den **FourCC** så här:

>   * När en TrackElement beskriver HEVC video den **FourCC** fältet skall vara lika med **”hev1”** och;

>   Den **CodecPrivateData** fältet ska innehålla en hex-kodad strängrepresentation av de följande byte-sekvens som anges i ABNF [[RFC5234]:](http://go.microsoft.com/fwlink/?LinkId=123096) (ingen ändring av MS-SSTR)

>   * %x 00 %x 00 %x 00 %x 01 SPSField %x 00 %x 00 %x 00 %x 01 PPSField

>   * SPSField innehåller sekvens parametern ange (Service Pack).

>   * PPSField innehåller segment parametern ange (PPS).

>   Obs: Den Video parametern ange (VPS) ingår inte i CodecPrivateData, men ska finnas i filhuvudet lagrade filernas i rutan 'hvcC'. System med Smooth Streaming-protokollet måste signalera ytterligare avkodning parametrar (t.ex, HEVC nivån) med det anpassade attributet ”codec”.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   Den **Smoothstreamingmedia's MajorVersion** fält måste anges till 2, och **MinorVersion** fält måste anges till 2. (Ingen ändring)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 fragment begäran 

>   **Obs**: media standardformat som begärdes för **MinorVersion** 2 och 'hev1' är 'iso8' varumärken ISO Base Mediaformatet anges i [ISO/IEC 14496 12] ISO Base Media filen Format fjärde Edition och [ISO/IEC 23001 7] Vanliga kryptering andra utgåvan.

### <a name="224-fragment-response"></a>2.2.4 fragment svar 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   Den **TfxdBox** är föråldrad och dess funktion som har ersatts av spåra Fragment avkoda rutan tid (tfdt) anges i avsnittet [ISO/IEC 14496 12] 8.8.12.

>   **Obs**: en klient kan beräkna varaktigheten för ett fragment genom att summera exempel varaktigheten i spåra kör rutan (trun) eller att multiplicera antalet prov gånger exempel standardlängden. BaseMediaDecodeTime 'tfdt' plus fragment varaktighet är lika med URL-parametern för tiden för nästa fragment.

>   En producent referens rutan tid (prft) som ska infogas innan en film Fragment visas (moof) efter behov, för att ange den UTC-tid som motsvarar spåra Fragment avkoda tiden för det första exemplet refererar till rutan film Fragment som anges i [ISO/IEC 14496 -12]-avsnitt 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   Den **TfrfBox** är föråldrad och dess funktion som har ersatts av spåra Fragment avkoda rutan tid (tfdt) anges i avsnittet [ISO/IEC 14496 12] 8.8.12.

>   **Obs**: en klient kan beräkna varaktigheten för ett fragment genom att summera exempel varaktigheten i spåra kör rutan (trun) eller att multiplicera antalet prov gånger exempel standardlängden. BaseMediaDecodeTime 'tfdt' plus fragment varaktighet är lika med URL-parametern för tiden för nästa fragment. Se framåt adresser inte kan användas eftersom de fördröjning direktsänd strömning.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   Den **TfhdBox** och relaterade fält kapslar in standardinställningarna för per exempel metadata i avsnittet. Syntaxen för den **TfhdBox** fält är en strikt delmängd av syntaxen för rutan spåra Fragment sidhuvud definieras i [[ISO/IEC-14496-12]](http://go.microsoft.com/fwlink/?LinkId=183695) avsnittet 8.8.7.

>   **BaseDataOffset (8 byte):** Förskjutning, i byte, från början av den **MdatBox** till exempel fält i den **MdatBox** fältet. Om du vill skicka en signal denna begränsning, måste standard-bas-är-moof-flaggan (0x020000) anges.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   Den **TrunBox** och relaterade fält som kapslar in per exempel metadata för det begärda fragmentet. Syntaxen för **TrunBox** är en strikt delmängd av Version 1 spåra Fragment rutan Kör definierad i [[ISO/IEC-14496-](http://go.microsoft.com/fwlink/?LinkId=183695)*12]* avsnittet 8.8.8.

>   **SampleCompositionTimeOffset (4 byte):** i exemplet sammansättning tidsförskjutningen för varje prov justeras så att tiden för första presenterades exemplet i avsnittet presentation är lika med avkoda tiden för det första avkodade provet. Negativt video prov sammansättning förskjutningar användas

>   som definierats i [[ISO/IEC-14496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

>   Obs: Detta förhindrar en video synkroniseringsfel på grund av video släpar ljud lika med största avkodade bild buffert borttagning fördröjning och underhåller presentation tidsinställning mellan alternativa fragment som kan ha olika borttagning fördröjningar.

>   Syntaxen för de fält som definieras i det här avsnittet anges i ABNF [[RFC5234]](http://go.microsoft.com/fwlink/?LinkId=123096) förblir detsamma, utom på följande sätt:

>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Fragmentera svar gemensamma fält 

### <a name="225-sparse-stream-pointer"></a>2.2.5 pekaren utspridd ström 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Fragmentera ännu ej tillgänglig 

### <a name="227-live-ingest"></a>2.2.7 mata in live 

#### <a name="2271-filetype"></a>2.2.7.1 filtyp 

>   **Filtyp (variabel):** anger undertyp och avsedd användning av MPEG-4 ([[MPEG4 RA])](http://go.microsoft.com/fwlink/?LinkId=327787) fil- och övergripande attribut.

>   **MajorBrand (variabel):** större varumärke mediefilen. MÅSTE anges till ”isml”.

>   **MinorVersion (variabel):** den lägre versionen av mediefilen. MÅSTE anges till 1.

>   **CompatibleBrands (variabel):** anger MPEG-4 stöds varumärken.
>   MÅSTE innehålla ”ccff” och ”iso8”.

>   Syntaxen för de fält som definieras i det här avsnittet anges i ABNF [[RFC5234]](http://go.microsoft.com/fwlink/?LinkId=123096) är följande:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Obs**: kompatibilitet märken 'ccff' och 'iso8' indikerar att fragment överensstämmer med ”filformatet vanliga behållaren” och Common Encryption [ISO/IEC 23001 7] och ISO Base Media filen Format version 4 [ISO/IEC 14496 12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 spåra fragmentet utökats sidhuvud 

### <a name="228-server-to-server-ingest"></a>2.2.8 infognings--servrar 

# <a name="3-protocol-details"></a>3 protocol-information 


## <a name="31-client-details"></a>3.1 klientinformation 

### <a name="311-abstract-data-model"></a>3.1.1 abstrakt datamodellen 

#### <a name="3111-presentation-description"></a>3.1.1.1 presentation beskrivning 

>   Dataelementet presentationsbeskrivning kapslar in alla metadata för presentation.

>   Presentation Metadata: En uppsättning metadata som är gemensamma för alla strömmar i presentationen. Presentation Metadata består av följande fält som anges i avsnitt *2.2.2.1*:

>   * **MajorVersion**
>   * **MinorVersion**
>   * **Tidsrymd**
>   * **Varaktighet**
>   * **IsLive**
>   * **LookaheadCount**
>   * **DVRWindowLength**

>   Presentationer som innehåller HEVC dataströmmar skall:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Obs: rutor föråldrad)

>   Presentationer ska också ange:

    TimeScale = 90000

>   Dataströmmen samling: En samling dataströmmen beskrivning dataelement som anges i avsnitt *3.1.1.1.2*.

>   Skydd beskrivning: En samling skydd Metadata Systembeskrivning dataelement som anges i avsnitt *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 beskrivning av protection System Metadata 

>   Dataelementet skydd Systembeskrivning Metadata innehåller metadata som är specifika för ett enda Content Protection System. (Ingen ändring)

>   Skydd sidhuvud beskrivning: Innehållsskydd metadata som gäller för en enda Content Protection System. Beskrivning av Protection huvudet omfattar följande fält som anges i avsnitt *2.2.2.2*:

>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 strömma beskrivning 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 spåra beskrivning 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Attributbeskrivning 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 beskrivning för fragment-referens 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 spåra-specifika Fragment Referensbeskrivning 

#### <a name="3112-fragment-description"></a>3.1.1.2 fragment beskrivning 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Exempelbeskrivning 

### <a name="312-timers"></a>3.1.2 timers 

### <a name="313-initialization"></a>3.1.3 initiering 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 högre nivå utlöses händelser 

#### <a name="3141-open-presentation"></a>3.1.4.1 öppna Presentation 

#### <a name="3142-get-fragment"></a>3.1.4.2 hämta Fragment 

#### <a name="3143-close-presentation"></a>3.1.4.3 Stäng Presentation 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 bearbetningen av händelser och ordningsföljd regler 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifest begäran och svar Manifest 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Fragmentera begäran och Fragmentera svar

## <a name="32-server-details"></a>3.2-serverinformation

## <a name="33-live-encoder-details"></a>3.3 Livekodaren information 

# <a name="4-protocol-examples"></a>4 protocol-exempel 

# <a name="5-security"></a>5 säkerhet 

## <a name="51-security-considerations-for-implementers"></a>5.1 säkerhetsaspekter för implementerare 
-----------------------------------------

>   Om innehållet transporteras med detta protokoll har hög kommersiellt värde, ska ett System för skydd av innehåll användas för att förhindra obehörig användning av innehållet. Den **ProtectionElement** kan användas för att utföra metadata som rör användning av ett Content Protection System. Skyddade ljud- och videoinnehåll skall krypteras som angetts i MPEG vanliga kryptering, andra utgåvan: 2015 [ISO/IEC 23001 7].

>   **Obs**: HEVC video kan endast sektorn data i VCL NALs är krypterad. Sektorn sidhuvuden och andra NALs är tillgängliga för presentationen program innan dekryptering. i en säker video sökväg är inte krypterad information tillgänglig presentation av program.

# <a name="52-index-of-security-parameters"></a>5.2 Index för parametrar 
-----------------------------


| **Parametern för säkerhet**  | **Avsnittet**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Vanliga kryptering rutor | *[ISO/IEC 23001-7]* |

# <a name="53-common-encryption-boxes"></a>5.3 vanliga kryptering rutor
-----------------------

Följande rutor får vara närvarande i fragment svar när Common Encryption tillämpas och anges i [ISO/IEC 23001 7] eller [ISO/IEC 14496 12]:

1.  Skydd System specifik sidhuvud ruta (pssh)

2.  Kryptering i rutan exempel (senc)

3.  Extra Information förskjutning i rutan exempel (saio)

4.  Extra Information storlek i rutan exempel (saiz)

5.  Exempel beskrivning gruppruta (sgpd)

6.  Exempel för att gruppruta (sbgp)

-----------------------

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
