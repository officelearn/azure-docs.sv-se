---
title: Azure Media Services – Smooth Streaming-protokollet (MS-SSTR)-tillägg för – HEVC | Microsoft Docs
description: Den här specifikationen beskriver protokollet och format för fragmenterad MP4-baserade direktsänd strömning med – HEVC i Azure Media Services. Det här är en ändring av Smooth Streaming-protokollet dokumentation (MS-SSTR) att inkludera stöd för – HEVC mata in och strömning. Endast de ändringar som krävs för att leverera – HEVC har angetts i den här artikeln, förutom har ”(ingen ändring)” anger text kopieras för endast information.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: b26252ddda7997bebd730bb4c1007f76b3e645a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650717"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Jämn direktuppspelning Protocol (MS-SSTR)-tillägg för – HEVC 

## <a name="1-introduction"></a>1 introduktion 

Den här artikeln innehåller detaljerade ändringar som ska tillämpas på specifikationen Smooth Streaming-protokollet [MS-SSTR] att aktivera Smooth Streaming för – HEVC kodad video. I den här specifikationen beskriver vi de ändringar som krävs för att leverera video-codec – HEVC. Artikeln följer samma numrering schema som [MS-SSTR]-specifikationen. Tom rubriker som visas i hela artikeln tillhandahålls för att förstå läsaren till deras position i [MS-SSTR]-specifikationen.  ”(Ingen ändring)” anger text kopieras endast för information.

Artikeln innehåller teknisk implementeringskrav för signalering av – HEVC video-codec i ett Smooth Streaming-manifest och normativa referenser uppdateras för att referera till de aktuella MPEG-standarder som innehåller – HEVC, gemensam kryptering av – HEVC och box namn för ISO Base Media-filformat har uppdaterats för att stämma överens med de senaste specifikationerna. 

Den refererade Smooth Streaming protokollspecifikationen [MS-SSTR] beskriver det kabelformat som användes för att tillhandahålla live och på begäran digitala media som ljud och video, på följande sätt: från en kodare till en server från en server till en annan server och från en Server för att en HTTP-klient.
Användning av en MPEG-4 ([[MPEG4 RA])](https://go.microsoft.com/fwlink/?LinkId=327787)-baserat struktur dataöverföring via HTTP kan sömlös växling i nära realtid mellan olika kvalitetsnivå av komprimerade medieinnehåll. Resultatet är en konstant uppspelningsupplevelse för slutanvändaren för HTTP-klienten även om Nätverks- och videorendering villkor ändrar för klientdatorn eller enheten.

## <a name="11-glossary"></a>1.1-ordlista 

Följande villkor är definierade i *[MS-GLOS]*:

>   **globalt unik identifierare (GUID) universell unik identifierare (UUID)**

Följande villkor är specifika för det här dokumentet:

>  **sammansättning tid:** Tid ett exempel presenteras på klienten, som definieras i [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   **CENC**: Common Encryption och som definierats i [ISO/IEC 23001 7], andra utgåvan.
> 
>   **avkoda tid:** Tid ett exempel krävs för att att avkoda på klienten, som definieras i [[ISO/IEC https://go.microsoft.com/fwlink/?LinkId=18369514496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)

**fragment:** En oberoende nedladdningsbara enhet **media** som består av en eller flera **exempel**.

>   **HEVC:** Hög effektivitet Video kodning enligt definitionerna i [ISO/IEC 23008 2]
> 
>   **manifest:** Metadata om den **presentation** som gör det möjligt för en klient att göra begäranden **media**. **media:** Komprimerade data för ljud, video och text som används av klienten för att spela upp en **presentation**. **Mediaformat:** Ett väldefinierat format för att representera ljud- eller videoklipp som en komprimerad **exempel**.
> 
>   **presentation:** Uppsättningen med alla **strömmar** och tillhörande metadata som behövs för att spela upp en enda film. **Begäran:** Ett HTTP-meddelande som skickats från klienten till servern, enligt definitionen i [[RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372) **Svar:** Ett HTTP-meddelande som skickats från servern till klienten, enligt definitionen i [[RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **Exempel:** Den minsta grundläggande enheten (till exempel en ram) där **media** lagras och bearbetas.
> 
>   **MAJ, BÖR, MÅSTE, BÖR INTE, FÅR INTE:** Dessa villkor (i versaler) används enligt beskrivningen i [[RFC2119].](https://go.microsoft.com/fwlink/?LinkId=90317) Alla instruktioner för valfritt beteende kan antingen SHOULD eller bör inte.

## <a name="12-references"></a>1.2-referenser

>   Referenser till öppen specifikationer för Microsoft-dokumentationen inkluderar inte en publishing år eftersom länkar går till den senaste versionen av dokument, som uppdateras ofta. Referenser till andra dokument som innehåller ett publishing år när någon är tillgänglig.

### <a name="121-normative-references"></a>1.2.1 normativa referenser 

>  [MS-SSTR] Smooth Streaming-protokollet *v20140502* [https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496 – 12] Internationella standardiseringsorganisationen ”informationsteknik--kodning av audiovisuellt objekt – del 12: ISO grundläggande mediafilformat ”, ISO/IEC 14496-12:2014, version 4 samt korrigering 1, ändringar 1 och 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496 15] Internationella standardiseringsorganisationen ”informationsteknik--kodning av audiovisuellt objekt – del 15: Transport av NAL enhet strukturerade video i ISO Base Media Format ”, ISO 14496-15:2015, version 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008 2] Informationsteknik--hög effektivitet kodning och media leverans i heterogena miljöer – del 2: Hög effektivitet video kodning: 2013 eller senaste version   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001 7] Informationsteknik – MPEG system tekniker – del 7: Gemensam kryptering i ISO grundläggande file format mediafiler, CENC Edition 2:2015 <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, ”” codec ”och” profiler ”-parametrar för” Bucket ”medietyper” <https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4 RA] MP4-registreringsutfärdare ”MP4REG” [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., ”nyckelord för användning i RFC ange kravet nivåer”, BCP 14, RFC 2119, mars 1997   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Informationsreferenser 

>   [MS-GLOS] Microsoft Corporation ”*Windows protokoll Master ordlista*”.
> 
>   [RFC3548] Josefsson, S., Ed. ”den Base16 Base32 och Base64 Data kodningar”, RFC 3548, juli 2003 [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., och Overell, P. ”, förstärkta BNF för Syntax specifikationer: ABNF", STD 68, RFC 5234, January 2008,   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3-översikt 

>   Endast ändringar Smooth Streaming-specifikationen som krävs för leverans av – HEVC anges nedan. Oförändrade avsnittsrubrikerna visas om du vill behålla plats i den refererade Smooth Streaming-specifikationen [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 relation till andra protokoll 

## <a name="15-prerequisitespreconditions"></a>1.5 krav/villkoren 

## <a name="16-applicability-statement"></a>1.6 tillämplighet instruktionen 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 versionshantering och funktionen förhandling 

## <a name="18-vendor-extensible-fields"></a>1.8 utökningsbar leverantör fält 

>   Följande metod användas identifiera strömmar i formatet – HEVC video:
> 
>   * **Anpassade beskrivande koder för medieformat:** Den här funktionen tillhandahålls av den **FourCC** fältet som anges i avsnittet *2.2.2.5*.
>   Administratörer kan se till att tilläggen inte står i konflikt genom att registrera tillägget koder med MPEG4-RA, som anges i [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 standarder tilldelningar 

## <a name="2-messages"></a>2 meddelanden 

## <a name="21-transport"></a>2.1-transport

## <a name="22-message-syntax"></a>2.2 message Syntax 

### <a name="221-manifest-request"></a>2.2.1 manifest begäran 

### <a name="222-manifest-response"></a>2.2.2 manifest svar 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **LägreVersion (variabel):** Lägre version av Manifest svarsmeddelandet. MÅSTE anges till 2. (Ingen ändring)
> 
>   **Tidsskalan (variabel):** Skala för attributet varaktighet anges som antalet steg i en sekund. Standardvärdet är
> 1. (Ingen ändring)
> 
>    Det rekommenderade värdet är 90000 för att representera exakta varaktigheten för bildrutor och fragment som innehåller bråkdelar ramhastighet video (till exempel 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

ProtectionElement skall vara närvarande när gemensam kryptering (CENC) har kopplats till video- eller ljudinnehåll strömmar. – HEVC krypterade strömmar motsvara Common Encryption 2nd Edition [ISO/IEC 23001 7]. Utsnittsdata i VCL NAL enheter ska krypteras.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (variabel):** Skala varaktighet- och tidsvärden i den här strömmen anges som antalet steg i en sekund. Ett värde av 90000 rekommenderas för – HEVC strömmar. Ett värde som matchar samplingsfrekvens Wave (till exempel 48000 eller 44100) rekommenderas för ljudströmmar.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (variabel):** En kod på fyra tecken som identifierar vilka medieformat används för varje exempel. Följande värdeintervallet reserveras med följande semantiska innebörd:
> 
> * ”hev1”: Video-exempel för videon spåra användning – HEVC med hjälp av hev1 exempel Beskrivningsformat som anges i [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (variabel):** Data som anger parametrar som är specifika för media format och gemensamma för alla exempel i kursen, representeras som en sträng med hex-kodade byte. Formatera och semantisk betydelse för byte-sekvens varierar beroende på värdet för den **FourCC** fältet på följande sätt:
> 
>   * När en TrackElement beskriver – HEVC video, den **FourCC** fältet ska vara lika med **”hev1”** och;
> 
>   Den **CodecPrivateData** fältet bör innehålla en hex-kodad sträng som innehåller den följande byte-sekvens som anges i ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (ingen ändring av MS-SSTR)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField innehåller sekvens Parameter angetts (Service Pack).
> 
>   * PPSField innehåller sektorn Parameter angetts (PPS).
> 
>   Obs! Video Parameter angetts (VPS) ingår inte i CodecPrivateData, men ska finnas i filhuvudet lagrade filernas i rutan ”hvcC”. System med hjälp av Smooth Streaming-protokollet måste skicka en signal ytterligare avkodning parametrar (till exempel – HEVC nivå) med hjälp av det anpassade attributet ”codec”.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   Den **Smoothstreamingmedia's MajorVersion** fält måste anges till 2, och **LägreVersion** fält måste anges till 2. (Ingen ändring)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 fragment begäran 

>   **Obs!** Standardformat för media som begärdes för **LägreVersion** 2 och ”hev1” är ”iso8” varumärke ISO Base Media filformatet anges i [ISO/IEC 14496 – 12] ISO Base Media File Format fjärde utgåvan och [ISO/IEC 23001 7] Common Encryption andra Utgåva.

### <a name="224-fragment-response"></a>2.2.4 fragment svar 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   Den **TfxdBox** är inaktuell och dess funktion som har ersatts av spåra Fragment avkoda rutan tid (tfdt) [ISO/IEC 14496 – 12]-avsnitt 8.8.12.
> 
>   **Obs!** En klient kan beräkna varaktigheten för ett fragment genom att summera exempel varaktigheterna visas i spåra rutan Kör (trun) eller multiplicera antalet exempel som exempel standardlängden. BaseMediaDecodeTime 'tfdt' plus fragment varaktigheten är lika med parametern URL tiden för nästa fragment.
> 
>   En producent referens tid ruta (prft) som ska infogas innan en film Fragment ruta (moof) efter behov, för att ange UTC-tid som motsvarar spåra Fragment avkoda tidpunkten för första exemplet refererar till rutan film Fragment som anges i [ISO/IEC 14496 – 12] avsnittet 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   Den **TfrfBox** är inaktuell och dess funktion som har ersatts av spåra Fragment avkoda rutan tid (tfdt) [ISO/IEC 14496 – 12]-avsnitt 8.8.12.
> 
>   **Obs!** En klient kan beräkna varaktigheten för ett fragment genom att summera exempel varaktigheterna visas i spåra rutan Kör (trun) eller multiplicera antalet exempel som exempel standardlängden. BaseMediaDecodeTime 'tfdt' plus fragment varaktigheten är lika med parametern URL tiden för nästa fragment. Söka framåt adresser är föråldrade eftersom de fördröjning direktsänd strömning.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   Den **TfhdBox** och relaterade fält kapsla in standardinställningarna för per exempel metadata i avsnittet. Syntaxen för den **TfhdBox** fältet är en strikt delmängd av syntaxen för rutan spåra Fragment sidhuvud definieras i [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) avsnittet 8.8.7.
> 
>   **BaseDataOffset (8 byte):** Förskjutningen i byte, från början av den **MdatBox** fältet till fältet exemplet i den **MdatBox** fält. Om du vill skicka en signal denna begränsning, måste standard-bas-är-moof-flaggan (0x020000) anges.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   Den **TrunBox** och relaterade fält som kapslar in per exempel metadata för det begärda fragmentet. Syntaxen för **TrunBox** är en strikt delmängd av Version 1 spåra Fragment rutan Kör definieras i [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* avsnittet 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 byte):** Exemplet sammansättning tidsförskjutningen för varje exempel justeras så att tiden för första uppvisade exemplet i avsnittet presentation är lika med avkodningen tiden för den första avkodade exemplet. Negativt video prov sammansättning förskjutningar skall användas
> 
>   enligt definitionen i [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Obs! Detta förhindrar en video synkroniseringsfel på grund av video släpar ljud som motsvarar den största avkodade bild buffert borttagning fördröjningen och underhåller presentation tidsinställning mellan alternativa fragment som kan ha olika borttagning av fördröjningar.
> 
>   Syntaxen för de fält som definierats i det här avsnittet anges i ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) förblir detsamma utom på följande sätt:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Fragmentera svarsfälten 

### <a name="225-sparse-stream-pointer"></a>2.2.5 sparse Stream pekare 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Fragmentera har ännu inte tillgänglig 

### <a name="227-live-ingest"></a>2.2.7 Liveinmatning 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **FileType (variabel):** anger undertyp och avsedd användning av MPEG-4 ([[MPEG4 RA])](https://go.microsoft.com/fwlink/?LinkId=327787) fil- och avancerade attribut.
> 
>   **MajorBrand (variabel):** Större varumärket för mediefilen. MÅSTE vara inställt på ”isml”.
> 
>   **LägreVersion (variabel):** Lägre version av mediefilen. MÅSTE anges till 1.
> 
>   **CompatibleBrands (variabel):** Anger de stödda varumärkena av MPEG-4.
>   MÅSTE innehålla ”ccff” och ”iso8”.
> 
>   Syntaxen för de fält som definierats i det här avsnittet anges i ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) är följande:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Obs!** Kompatibilitet varumärken 'ccff' och 'iso8' tyda på att fragment överensstämmer med ”filformatet vanliga behållare” och Common Encryption [ISO/IEC 23001 7] och ISO Base Media filen Format version 4 [ISO/IEC 14496 – 12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 spåra Fragment utökade rubrik 

### <a name="228-server-to-server-ingest"></a>2.2.8 mata in server-till-Server 

## <a name="3-protocol-details"></a>3 protocol-information 


## <a name="31-client-details"></a>3.1 klientinformation 

### <a name="311-abstract-data-model"></a>3.1.1 abstrakt datamodell 

#### <a name="3111-presentation-description"></a>3.1.1.1 presentation beskrivning 

>   Dataelementet presentationsbeskrivning kapslar in alla metadata för presentation.
> 
>   Presentation Metadata: En uppsättning metadata som är gemensamma för alla dataströmmar i presentationen. Presentation Metadata består av följande fält som anges i avsnitt *2.2.2.1*:
> 
> * **MajorVersion**
> * **MinorVersion**
> * **TimeScale**
> * **Varaktighet**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   Presentationer som innehåller – HEVC strömmar skall:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Observera: Rutorna inaktuell)
> 
>   Presentationer bör också ange:

    TimeScale = 90000

>   Insamling av Stream: En samling Stream beskrivning dataelementen, som anges i avsnittet *3.1.1.1.2*.
> 
>   Beskrivning av skydd: En samling av Metadata för skydd Systembeskrivning dataelementen, som anges i avsnittet *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Metadata för protection Systembeskrivning 

>   Dataelementet Protection Systembeskrivning Metadata innehåller metadata som är specifika för ett enda System för Content Protection. (Ingen ändring)
> 
>   Beskrivning av Protection rubrik: Innehållsskydd metadata som tillhör ett enda System för Content Protection. Beskrivning av rubriken Protection består av följande fält som anges i avsnitt *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Stream beskrivning 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 spåra beskrivning 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 beskrivning av anpassat attribut 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 fragment Referensbeskrivning 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 spåra specifika Fragment Referensbeskrivning 

#### <a name="3112-fragment-description"></a>3.1.1.2 fragment beskrivning 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Exempelbeskrivning 

### <a name="312-timers"></a>3.1.2 timers 

### <a name="313-initialization"></a>3.1.3 initiering 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 högre nivå utlösta händelser 

#### <a name="3141-open-presentation"></a>3.1.4.1 öppna Presentation 

#### <a name="3142-get-fragment"></a>3.1.4.2 hämta Fragment 

#### <a name="3143-close-presentation"></a>3.1.4.3 Stäng Presentation 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 bearbetning av händelser och ordningsföljd regler 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifest begäran och svar-Manifest 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Fragmentera begäran och Fragmentera svar

## <a name="32-server-details"></a>3.2-serverinformation

## <a name="33-live-encoder-details"></a>3.3 Livekodare information 

## <a name="4-protocol-examples"></a>4 protocol-exempel 

## <a name="5-security"></a>5 säkerhet 

## <a name="51-security-considerations-for-implementers"></a>5.1 säkerhetsöverväganden för implementerare

>   En Content Protection System bör användas för att förhindra obehörig användning av innehållet om innehållet transporteras med detta protokoll har hög kommersiella värde. Den **ProtectionElement** kan användas för att utföra metadata som hör till användning av ett Content Protection-System. Skyddade ljud- och videoinnehåll skall krypteras som angetts i MPEG gemensam kryptering, andra utgåvan: 2015 [ISO/IEC 23001-7].
> 
>   **Obs!** – HEVC video krypteras utsnittsdata i VCL NALs. Sektorn rubriker och andra NALs är tillgängliga för presentation program innan dekryptering. i en säker video sökväg är inte krypterad information tillgänglig till presentationen program.

## <a name="52-index-of-security-parameters"></a>5.2 Index för parametrar 


| **Parametern för säkerhet**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Vanliga kryptering rutorna | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 common Encryption rutorna

Följande rutor kan finnas i fragment svar när Common Encryption tillämpas och anges i [ISO/IEC 23001 7] eller [ISO/IEC 14496 – 12]:

1.  Skydd System specifik rubrik ruta (pssh)

2.  Rutan för exempel-kryptering (senc)

3.  Extra Information förskjutning i rutan exempel (saio)

4.  Extra Information storleksrutan exempel (saiz)

5.  Beskrivning för Exempelgruppruta (sgpd)

6.  Exemplet för att grupprutan (sbgp)

-----------------------

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
