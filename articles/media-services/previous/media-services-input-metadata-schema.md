---
title: Azure Media Services indatametadataschema | Microsoft-dokument
description: Den här artikeln innehåller en översikt över Azure Media Services indatametadataschema.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a81d6edfd887dc935a53742b7bc1492651c9bda5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887127"
---
# <a name="input-metadata"></a>Metadata för indata 

Ett kodningsjobb är associerat med en indatatillgång (eller tillgångar) som du vill utföra vissa kodningsuppgifter för.  När en uppgift har slutförts produceras en utdata.  Utdatatillgången innehåller video, ljud, miniatyrer, manifest, etc. Utdatatillgången innehåller också en fil med metadata om indatatillgången. Namnet på metadata XML-filen har &lt;följande format:&gt;asset_id _metadata.xml (till exempel 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), &lt;där asset_id&gt; är AssetId-värdet för indatatillgången.  

Media Services söker inte i förebyggande syfte igenom indatatillgångar för att generera metadata. Indatametadata genereras endast som en artefakt när en indatatillgång bearbetas i ett jobb. Därför skrivs den här artefakten till utdatatillgången. Olika verktyg används för att generera metadata för indatatillgångar och utdatatillgångar. Därför har indatametadata ett något annorlunda schema än utdatametadata.

Om du vill undersöka metadatafilen kan du skapa en **SAS-positionerare** och hämta filen till den lokala datorn. Du hittar ett exempel på hur du skapar en SAS-positionerare och hämtar en fil [med hjälp av Media Services .NET SDK-tillägg](media-services-dotnet-get-started.md).  

I den här artikeln beskrivs de element och typer&lt;i XML-schemat som indatametada ( asset_id&gt;_metadata.xml) baseras på.  Information om filen som innehåller metadata om utdatatillgången finns i [Utdatametadata](media-services-output-metadata-schema.md).  

Du hittar ett [XML-exempel](media-services-input-metadata-schema.md#xml) i [schemakoden](media-services-input-metadata-schema.md#code) i slutet av den här artikeln.  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a>Element för AssetFiles (rotelement)
Innehåller en samling [AssetFile-element](media-services-input-metadata-schema.md#AssetFile)för kodningsjobbet.  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

| Namn | Beskrivning |
| --- | --- |
| **Tillgångsfil**<br /><br /> minOccurs="1" maxOccurs="unbounded" |Ett enda underordnat element. Mer information finns i [Element assetfile](media-services-input-metadata-schema.md#AssetFile). |

## <a name="assetfile-element"></a><a name="AssetFile"></a>Element för Tillgångsfil
 Innehåller attribut och element som beskriver en tillgångsfil.  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Namn**<br /><br /> Krävs |**xs:sträng** |Tillgångsfilnamn. |
| **Storlek**<br /><br /> Krävs |**xs:lång** |Tillgångsfilens storlek i byte. |
| **Varaktighet**<br /><br /> Krävs |**xs:varaktighet** |Innehållet uppspelningslängd. Exempel: Duration="PT25M37.757S". |
| **AntalOfStreams**<br /><br /> Krävs |**xs:int** |Antal strömmar i tillgångsfilen. |
| **FormatNamn**<br /><br /> Krävs |**xs: sträng** |Formatera namn. |
| **FormatVerboseNames**<br /><br /> Krävs |**xs: sträng** |Formatera utförliga namn. |
| **Starttime** |**xs:varaktighet** |Starttid för innehåll. Exempel: StartTime="PT2.669S". |
| **OverallBitRate** |**xs: int** |Genomsnittlig bithastighet för tillgångsfilen i kbps. |

> [!NOTE]
> Följande fyra underordnade element måste visas i en sekvens.  
> 
> 

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" | |Samling av alla [programelement](media-services-input-metadata-schema.md#Programs) när tillgångsfilen är i MPEG-TS-format. |
| **VideoTracks (videotracks)**<br /><br /> minOccurs="0" | |Varje fysisk tillgångsfil kan innehålla noll eller fler videospår som interfolieras till ett lämpligt behållarformat. Det här elementet innehåller en samling videospår som ingår i [tillgångsfilen.](media-services-input-metadata-schema.md#VideoTracks) |
| **AudioTracks (ljudspår)**<br /><br /> minOccurs="0" | |Varje fysisk tillgångsfil kan innehålla noll eller fler ljudspår som inte är inkopplade i ett lämpligt behållarformat. Det här elementet innehåller en samling [audiotracks](media-services-input-metadata-schema.md#AudioTracks) som ingår i tillgångsfilen. |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataTyp](media-services-input-metadata-schema.md#MetadataType) |Tillgångsfilens metadata representeras som key\value-strängar. Ett exempel:<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a>TrackType (spårtyp)
Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Id**<br /><br /> Krävs |**xs:int** |Nollbaserat index för det här ljud- eller videospåret.<br /><br /> Detta är inte nödvändigtvis att TrackID som används i en MP4-fil. |
| **Codec** |**xs:sträng** |Video spår codec sträng. |
| **CodecLongName (CodecLongName)** |**xs: sträng** |Ljud- eller videospårcodec långt namn. |
| **TimeBase (Tidsbas)**<br /><br /> Krävs |**xs:sträng** |Tidsbas. Exempel: TimeBase="1/48000" |
| **Antalrutor** |**xs:int** |Antal bildrutor (närvarande för videospår). |
| **Starttime** |**xs: varaktighet** |Spåra starttid. Exempel: StartTime="PT2.669S" |
| **Varaktighet** |**xs:varaktighet** |Spårets varaktighet. Exempel: Duration="PTSampleFormat M37.757S". |

> [!NOTE]
> Följande två underordnade element måste visas i en sekvens.  
> 
> 

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Disposition**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Innehåller presentationsinformation (till exempel om ett visst ljudspår är för synskadade tittare). |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataTyp](media-services-input-metadata-schema.md#MetadataType) |Allmänna nyckel-/värdesträngar som kan användas för att lagra en mängd information. Till exempel key="language" och value="eng". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a>AudioTrackType (ärver från TrackType)
 **AudioTrackType** är en global komplex typ som ärver från [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Typen representerar ett visst ljudspår i tillgångsfilen.  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **SampleFormat (SampleFormat)** |**xs:sträng** |Exempelformat. |
| **KanalLägg** |**xs: sträng** |Kanallayout. |
| **Kanaler**<br /><br /> Krävs |**xs:int** |Nummer (0 eller fler) av ljudkanaler. |
| **SamplingTra**<br /><br /> Krävs |**xs:int** |Ljudsamplingshastighet i sampling/sek eller Hz. |
| **Bitrate** |**xs:int** |Genomsnittlig ljudbithastighet i bitar per sekund, beräknat från tillgångsfilen. Endast den elementära strömnyttolasten räknas och förpackningsomkostnaderna ingår inte i det här antalet. |
| **BitsPerSample** |**xs:int** |Bitar per prov för formattypen wFormatTag. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a>VideoTrackType (ärver från TrackType)
**VideoTrackType** är en global komplex typ som ärver från [TrackType](media-services-input-metadata-schema.md#TrackType).  

Typen representerar ett visst videospår i tillgångsfilen.  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Fourcc**<br /><br /> Krävs |**xs:sträng** |Video codec FourCC-kod. |
| **Profil** |**xs: sträng** |Videospårets profil. |
| **Nivå** |**xs: sträng** |Videospårets nivå. |
| **PixelFormat** |**xs: sträng** |Videospårets pixelformat. |
| **Bredd**<br /><br /> Krävs |**xs:int** |Kodad videobredd i pixlar. |
| **Höjd**<br /><br /> Krävs |**xs:int** |Kodad videohöjd i pixlar. |
| **DisplayAspectRatioNumerator**<br /><br /> Krävs |**xs: dubbel** |Videovisning av bildförhållande täljare. |
| **DisplayAspectRatioDenominator**<br /><br /> Krävs |**xs:dubbel** |Konfessionen för bildproportioner. |
| **DisplayAspectRatioDenominator**<br /><br /> Krävs |**xs: dubbel** |Videoexempel till stämpling täljare. |
| **SampleAspectRatioNumerator** |**xs: dubbel** |Videoexempel till stämpling täljare. |
| **SampleAspectRatioNumerator** |**xs:dubbel** |Det som är en nämnare för videoexempel. |
| **Framerate**<br /><br /> Krävs |**xs:decimal** |Uppmätt videobildhastighet i .3f-format. |
| **Bitrate** |**xs:int** |Genomsnittlig videobithastighet i kilobit per sekund, beräknat från tillgångsfilen. Endast den elementära strömnyttolasten räknas och förpackningens omkostnader ingår inte. |
| **MaxGOPBitrate** |**xs: int** |Max GOP genomsnittliga bitrate för denna video spår, i kilobits per sekund. |
| **HasBFrames (ar)** |**xs:int** |Videospår antal B-bildrutor. |

## <a name="metadatatype"></a><a name="MetadataType"></a>MetadataTyp
**MetadataType** är en global komplex typ som beskriver metadata för en tillgångsfil som nyckel-/värdesträngar. Till exempel key="language" och value="eng".  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **key**<br /><br /> Krävs |**xs:sträng** |Nyckeln i nyckel/värdepar. |
| **värde**<br /><br /> Krävs |**xs:sträng** |Värdet i nyckel-/värdeparet. |

## <a name="programtype"></a><a name="ProgramType"></a>ProgramType (olika)
**ProgramType** är en global komplex typ som beskriver ett program.  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **ProgramId (programId)**<br /><br /> Krävs |**xs:int** |Program-ID |
| **Antal program**<br /><br /> Krävs |**xs:int** |Antal program. |
| **PmtPid (på andra sätt)**<br /><br /> Krävs |**xs:int** |Programkarttabeller innehåller information om program.  Mer information finns i [PMt](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid (dator)**<br /><br /> Krävs |**xs: int** |Används av dekoder. Mer information finns i [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: lång** |Startar tidsstämpel för presentation. |
| **Slutptörer** |**xs: lång** |Slutar presentationstidsstämpeln. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a>StreamDispositionType
**StreamDispositionType** är en global komplex typ som beskriver strömmen.  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Default**<br /><br /> Krävs |**xs: int** |Ange det här attributet till 1 för att ange att detta är standardpresentationen. |
| **Dub**<br /><br /> Krävs |**xs:int** |Ange det här attributet till 1 för att ange att detta är den dubbade presentationen. |
| **Ursprungliga**<br /><br /> Krävs |**xs: int** |Ange det här attributet till 1 för att visa att detta är den ursprungliga presentationen. |
| **Kommentar**<br /><br /> Krävs |**xs:int** |Ställ in det här attributet på 1 för att ange att det här spåret innehåller kommentarer. |
| **Lyrics**<br /><br /> Krävs |**xs:int** |Ställ in det här attributet på 1 för att ange att det här spåret innehåller texter. |
| **Karaoke**<br /><br /> Krävs |**xs:int** |Ställ in det här attributet på 1 för att indikera att detta representerar karaokespåret (bakgrundsmusik, ingen sång). |
| **Tvingade**<br /><br /> Krävs |**xs:int** |Ange det här attributet till 1 för att ange att detta är den påtvingade presentationen. |
| **UtfrågningImpaired**<br /><br /> Krävs |**xs:int** |Ställ in det här attributet på 1 för att ange att det här spåret är för personer som har nedsatt hörsel. |
| **VisualImpaired**<br /><br /> Krävs |**xs:int** |Ange det här attributet till 1 för att ange att det här spåret är för synskadade. |
| **CleanEffects**<br /><br /> Krävs |**xs: int** |Ställ in det här attributet på 1 för att indikera att det här spåret har rena effekter. |
| **Bifogad Bild**<br /><br /> Krävs |**xs: int** |Ställ in det här attributet på 1 för att visa att det här spåret har bilder. |

## <a name="programs-element"></a><a name="Programs"></a>Program element
Omslagselement som innehåller flera **programelement.**  

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType (olika)](media-services-input-metadata-schema.md#ProgramType) |För tillgångsfiler som är i MPEG-TS-format innehåller information om program i tillgångsfilen. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a>VideoTracks-element
 Omslagselement som innehåller flera **VideoTrack-element.**  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **VideoTrack (videotrack)**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (ärver från TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Innehåller information om videospår i tillgångsfilen. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a>Elementet AudioTracks
 Omslagselement som innehåller flera **AudioTrack-element.**  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **AudioTrack (ljudspår)**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (ärver från TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Innehåller information om ljudspår i tillgångsfilen. |

## <a name="schema-code"></a><a name="code"></a>Schemakod
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="xml-example"></a><a name="xml"></a>EXEMPEL PÅ XML
Följande är ett exempel på metadatafilen för indata.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

