---
title: Azure Media Services inkommande metadataschema | Microsoft Docs
description: Avsnittet ger en översikt över Azure Media Services inkommande metadataschema.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d72848e2-4b65-4c84-94bc-e2a90a6e7f47
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: 9edfdee82a289d3b966510281305dc45a451c35f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="input-metadata"></a>Ange Metadata
Ett kodningsjobb är associerad med en inkommande tillgång (eller tillgångar) om du vill utföra vissa uppgifter som kodning.  En utdatatillgången skapas vid slutförandet av uppgiften.  Utdatatillgången innehåller video, ljud, miniatyrer, manifestet osv. Utdatatillgången innehåller också en fil med metadata om inkommande tillgången. Namnet på XML-metadatafilen har följande format: &lt;asset_id&gt;_metadata.xml (till exempel 41114ad3-eb5e - 4c 57 8d 92-5354e2b7d4a4_metadata.xml), där &lt;asset_id&gt; AssetId värdet för den inkommande tillgången.  

Om du vill undersöka metadatafilen kan du skapa en **SAS** positionerare och hämta filen till den lokala datorn. Du hittar ett exempel på hur du skapar en SAS-positionerare och hämta en fil [med hjälp av Media Services .NET SDK-tilläggen](media-services-dotnet-get-started.md).  

Den här artikeln beskriver de element och typer av XML-schemat som den inkommande metada (&lt;asset_id&gt;_metadata.xml) är baserad.  Information om den fil som innehåller metadata om utdatatillgången finns [utdata Metadata](media-services-output-metadata-schema.md).  

> [!NOTE]
> Du hittar den [schemat kod](media-services-input-metadata-schema.md#code) en [XML-exempel](media-services-input-metadata-schema.md#xml) i slutet av den här artikeln.  
> 
> 

## <a name="AssetFiles"></a> AssetFiles element (rotelementet)
Innehåller en samling [AssetFile elementet](media-services-input-metadata-schema.md#AssetFile)s för kodningsjobbet.  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

| Namn | Beskrivning |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = ”1” maxOccurs = ”unbounded” |En enda underordnade element. Mer information finns i [AssetFile elementet](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a> AssetFile element
 Innehåller attribut och element som beskriver en resursfil.  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Namn**<br /><br /> Krävs |**Xs:String** |Filnamnet för tillgången. |
| **Storlek**<br /><br /> Krävs |**Xs:Long** |Storleken på resursfilen i byte. |
| **Varaktighet**<br /><br /> Krävs |**Duration** |Innehåll play tillbaka varaktighet. Exempel: Duration = ”PT25M37.757S”. |
| **NumberOfStreams**<br /><br /> Krävs |**Xs:int** |Antal dataströmmar i resursfilen. |
| **FormatNames**<br /><br /> Krävs |**Xs: sträng** |Formatnamn. |
| **FormatVerboseNames**<br /><br /> Krävs |**Xs: sträng** |Utförlig formatnamn. |
| **StartTime** |**Duration** |Starttid för innehåll. Exempel: StartTime = ”PT2.669S”. |
| **OverallBitRate** |**Xs: int** |Genomsnittlig bithastighet i resursfilen i kbit/s. |

> [!NOTE]
> Följande fyra underordnade element måste visas i en sekvens.  
> 
> 

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" | |Samling av alla [program elementet](media-services-input-metadata-schema.md#Programs) när resursfilen har MPEG-TS-format. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Varje fysisk tillgång-fil kan innehålla noll eller fler videor spår överlagrat till en lämplig behållare format. Det här elementet innehåller en samling med alla [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) som ingår i resursfilen. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Varje fysisk tillgång-fil kan innehålla noll eller flera ljud spårar överlagrat till en lämplig behållare format. Det här elementet innehåller en samling med alla [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) som ingår i resursfilen. |
| **Metadata**<br /><br /> minOccurs = ”0” maxOccurs = ”unbounded” |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Tillgångsinformation filens metadata representeras som key\value strängar. Exempel:<br /><br /> **&lt;Metadatanyckel = ”språk” value = ”eng” /&gt;** |

## <a name="TrackType"></a> TrackType
Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Id**<br /><br /> Krävs |**Xs:int** |Nollbaserade indexet för den här ljud- och spåra.<br /><br /> Detta är inte nödvändigtvis att TrackID som används i en MP4-fil. |
| **Codec** |**Xs:String** |Video spåra codec sträng. |
| **CodecLongName** |**Xs: sträng** |Ljud- och spåra codec långt namn. |
| **Tidsvärde**<br /><br /> Krävs |**Xs:String** |Bas. Exempel: Tidsvärde = ”1/48000” |
| **NumberOfFrames** |**Xs:int** |Antal bildrutor (finns i video spår). |
| **StartTime** |**Xs: duration** |Spåra starttid. Exempel: StartTime = ”PT2.669S” |
| **Varaktighet** |**Duration** |Spåra varaktighet. Exempel: Duration = ”PTSampleFormat M37.757S”. |

> [!NOTE]
> Följande två underordnade element måste visas i en sekvens.  
> 
> 

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Disposition**<br /><br /> minOccurs = ”0” maxOccurs = ”1” |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Innehåller information om presentation (till exempel om en viss ljud spåra är för personer med nedsatt). |
| **Metadata**<br /><br /> minOccurs = ”0” maxOccurs = ”unbounded” |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Allmän nyckel/värde-strängar som kan användas för att lagra en mängd information. Till exempel nyckel = ”språk”, och värdet = ”eng”. |

## <a name="AudioTrackType"></a> AudioTrackType (ärver från TrackType)
 **AudioTrackType** är en global komplex typ som ärver från [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Typen representerar ett visst ljud spår i resursfilen.  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **SampleFormat** |**Xs:String** |Exempel-format. |
| **ChannelLayout** |**Xs: sträng** |Kanal layout. |
| **kanaler**<br /><br /> Krävs |**Xs:int** |Antal (0 eller fler) ljud kanaler. |
| **SamplingRate**<br /><br /> Krävs |**Xs:int** |I prover per sekund eller Hz ljud samplingsfrekvensen. |
| **Bithastighet** |**Xs:int** |Genomsnittlig ljud bithastighet i bitar per sekund som beräknas från resursfilen. Grundläggande dataströmmen nyttolasten räknas och paketering kostnader ingår inte i det här antalet. |
| **BitsPerSample** |**Xs:int** |Bitar per prov för formatet wFormatTag typ. |

## <a name="VideoTrackType"></a> VideoTrackType (ärver från TrackType)
**VideoTrackType** är en global komplex typ som ärver från [TrackType](media-services-input-metadata-schema.md#TrackType).  

Typen representerar ett specifikt video spår i resursfilen.  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **FourCC**<br /><br /> Krävs |**Xs:String** |Video-codec FourCC-kod. |
| **Profil** |**Xs: sträng** |Video spåra profil. |
| **Nivå** |**Xs: sträng** |Video spåra nivå. |
| **PixelFormat** |**Xs: sträng** |Video spåra bildpunktsformat. |
| **Bredd**<br /><br /> Krävs |**Xs:int** |Kodad video bredd i bildpunkter. |
| **Höjd**<br /><br /> Krävs |**Xs:int** |Kodad video höjd i bildpunkter. |
| **DisplayAspectRatioNumerator**<br /><br /> Krävs |**Xs: dubbel** |Videoenhet proportionerna täljaren. |
| **DisplayAspectRatioDenominator**<br /><br /> Krävs |**Xs:Double** |Videoenhet proportionerna nämnare. |
| **DisplayAspectRatioDenominator**<br /><br /> Krävs |**Xs: dubbel** |Video exempel proportionerna täljaren. |
| **SampleAspectRatioNumerator** |**Xs: dubbel** |Video exempel proportionerna täljaren. |
| **SampleAspectRatioNumerator** |**Xs:Double** |Video exempel proportionerna nämnare. |
| **Ramhastighet**<br /><br /> Krävs |**Xs:decimal** |Mätt video bildfrekvens .3f format. |
| **Bithastighet** |**Xs:int** |Genomsnittlig video bithastighet i kilobit per sekund som beräknas från resursfilen. Grundläggande dataströmmen nyttolasten räknas och paketering kostnader ingår inte. |
| **MaxGOPBitrate** |**Xs: int** |Max GOP genomsnittlig bithastighet för den här videon spår i kilobit per sekund. |
| **HasBFrames** |**Xs:int** |Video spåra antalet B ramar. |

## <a name="MetadataType"></a> MetadataType
**MetadataType** är en global komplex typ som beskriver metadata för en resursfil som nyckel/värde-strängar. Till exempel nyckel = ”språk”, och värdet = ”eng”.  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **nyckel**<br /><br /> Krävs |**Xs:String** |Nyckeln i nyckel/värde-par. |
| **värde**<br /><br /> Krävs |**Xs:String** |Värdet i nyckel/värde-par. |

## <a name="ProgramType"></a> ProgramType
**ProgramType** är en global komplex typ som beskriver ett program.  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **ProgramId**<br /><br /> Krävs |**Xs:int** |Program-Id |
| **NumberOfPrograms**<br /><br /> Krävs |**Xs:int** |Antal program. |
| **PmtPid**<br /><br /> Krävs |**Xs:int** |Programmet kartan tabeller (PMTs) innehåller information om program.  Mer information finns i [betalning](http://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Krävs |**Xs: int** |Används av avkodarens. Mer information finns i [PCR](http://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**Xs: lång** |Starta en presentation tidsstämpel. |
| **EndPTS** |**Xs: lång** |Avslutas presentation tidsstämpel. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** är en global komplex typ som beskriver dataströmmen.  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Standard**<br /><br /> Krävs |**Xs: int** |Ange det här attributet 1 för att ange detta är standardpresentationen. |
| **Dub**<br /><br /> Krävs |**Xs:int** |Ange det här attributet 1 för att ange detta är går under benämningen presentationen. |
| **Ursprungliga**<br /><br /> Krävs |**Xs: int** |Ange det här attributet 1 för att ange detta är den ursprungliga presentationen. |
| **Kommentar**<br /><br /> Krävs |**Xs:int** |Ange det här attributet 1 för att ange den här spåra innehåller kommentar. |
| **Texter**<br /><br /> Krävs |**Xs:int** |Ange det här attributet 1 för att ange den här spåra innehåller texter. |
| **Karaoke**<br /><br /> Krävs |**Xs:int** |Värdet 1 för att ange karaoke spåra (bakgrundsmusik, ingen sångrösten) representerar det här attributet. |
| **Tvingad**<br /><br /> Krävs |**Xs:int** |Ange det här attributet 1 för att ange detta är framtvingad presentationen. |
| **HearingImpaired**<br /><br /> Krävs |**Xs:int** |Ange det här attributet 1 för att ange spåret gäller för den nedsatt hörsel. |
| **VisualImpaired**<br /><br /> Krävs |**Xs:int** |Ange det här attributet 1 för att ange spåret gäller för den nedsatt. |
| **CleanEffects**<br /><br /> Krävs |**Xs: int** |Ange det här attributet 1 för att ange spåret har ren effekter. |
| **AttachedPic**<br /><br /> Krävs |**Xs: int** |Ange det här attributet 1 för att ange spåret har bilder. |

## <a name="Programs"></a> Program-element
-Elementet innehåller flera **programmet** element.  

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Programmet**<br /><br /> minOccurs = ”0” maxOccurs = ”unbounded” |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Innehåller information om program i resursfilen för tillgångsfiler i MPEG-TS-format. |

## <a name="VideoTracks"></a> VideoTracks element
 -Elementet innehåller flera **VideoTrack** element.  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs = ”0” maxOccurs = ”unbounded” |[VideoTrackType (ärver från TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Innehåller information om video spår i resursfilen. |

## <a name="AudioTracks"></a> AudioTracks element
 -Elementet innehåller flera **AudioTrack** element.  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs = ”0” maxOccurs = ”unbounded” |[AudioTrackType (ärver från TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Innehåller information om ljud spår i resursfilen. |

## <a name="code"></a> Schemat kod
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
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


## <a name="xml"></a> XML-exempel
Följande är ett exempel på indata-metadatafil.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
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

