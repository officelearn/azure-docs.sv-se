---
title: Azure Media Services utdatametadataschema | Microsoft-dokument
description: Den här artikeln innehåller en översikt över Azure Media Services-metadataschema.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 3f0c6b60e2be625d1f869c3eda4acb9dfd3c6e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74886820"
---
# <a name="output-metadata"></a>Metadata för utdata
## <a name="overview"></a>Översikt
Ett kodningsjobb är associerat med en indatatillgång (eller tillgångar) som du vill utföra vissa kodningsuppgifter för. Koda till exempel en MP4-fil till H.264 MP4 adaptiva bitrate-uppsättningar. skapa en miniatyrbild. skapa överlägg. När en uppgift har slutförts produceras en utdata.  Utdatatillgången innehåller video, ljud, miniatyrer, etc. Utdatatillgången innehåller också en fil med metadata om utdatatillgången. Namnet på metadata-XML-filen har &lt;följande format:&gt;source_file_name _manifest.xml (till exempel BigBuckBunny_manifest.xml).  

Media Services söker inte i förebyggande syfte igenom indatatillgångar för att generera metadata. Indatametadata genereras endast som en artefakt när en indatatillgång bearbetas i ett jobb. Därför skrivs den här artefakten till utdatatillgången. Olika verktyg används för att generera metadata för indatatillgångar och utdatatillgångar. Därför har indatametadata ett något annorlunda schema än utdatametadata.

Om du vill undersöka metadatafilen kan du skapa en **SAS-positionerare** och hämta filen till den lokala datorn.  

I den här artikeln beskrivs de element och typer&lt;i XML-schemat som utdatametada ( source_file_name&gt;_manifest.xml) baseras på. Information om filen som innehåller metadata om indatatillgången finns i Indatametadata.  

Du hittar hela schemakoden och XML-exemplet i slutet av den här artikeln.  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a>Rotelement för AssetFiles
Insamling av AssetFile-transaktioner för kodningsjobbet.  

### <a name="child-elements"></a>Underordnade element
| Namn | Beskrivning |
| --- | --- |
| **Tillgångsfil**<br/><br/> minOccurs="0" maxOccurs="1" |Ett AssetFile-element som ingår i AssetFiles-samlingen. |

## <a name="assetfile-element"></a><a name="AssetFile"></a>Element för Tillgångsfil
Du hittar ett XML-exempel på [XML.](#xml)  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Namn**<br/><br/> Krävs |**xs:sträng** |Filnamnet för medietillgång. |
| **Storlek**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:lång** |Tillgångsfilens storlek i byte. |
| **Varaktighet**<br/><br/> Krävs |**xs:varaktighet** |Innehållet uppspelningslängd. |

### <a name="child-elements"></a>Underordnade element
| Namn | Beskrivning |
| --- | --- |
| **Källor** |Insamling av indata-/källmediefiler, som bearbetades för att producera denna AssetFile. Mer information finns i Källelement. |
| **VideoTracks (videotracks)**<br/><br/> minOccurs="0" maxOccurs="1" |Varje fysisk AssetFile kan innehålla noll eller fler videor spår interfolierade till en lämplig behållare format. Mer information finns i VideoTracks-elementet. |
| **AudioTracks (ljudspår)**<br/><br/> minOccurs="0" maxOccurs="1" |Varje fysisk AssetFile kan innehålla noll eller fler ljudspår som inte är anpassade till ett lämpligt behållarformat. Detta är samlingen av alla dessa ljudspår. Mer information finns i AudioTracks-elementet. |

## <a name="sources-element"></a><a name="Sources"></a>Element för källor
Insamling av indata-/källmediefiler, som bearbetades för att producera denna AssetFile.  

Du hittar ett XML-exempel på [XML.](#xml)  

### <a name="child-elements"></a>Underordnade element
| Namn | Beskrivning |
| --- | --- |
| **Källkod**<br/><br/> minOccurs="1" maxOccurs="unbounded" |En indata-/källfil som används vid genererande av den här tillgången. Mer information finns i Källelement. |

## <a name="source-element"></a><a name="Source"></a>Källelement
En indata-/källfil som används vid genererande av den här tillgången.  

Du hittar ett XML-exempel på [XML.](#xml)  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Namn**<br/><br/> Krävs |**xs:sträng** |Filnamn för indatakälla. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a>VideoTracks-element
Varje fysisk AssetFile kan innehålla noll eller fler videor spår interfolierade till en lämplig behållare format. **VideoTracks-elementet** representerar en samling av alla videospår.  

Du hittar ett XML-exempel på [XML.](#xml)  

### <a name="child-elements"></a>Underordnade element
| Namn | Beskrivning |
| --- | --- |
| **VideoTrack (videotrack)**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Ett specifikt videospår i den överordnade AssetFile. Mer information finns i VideoTrack-elementet. |

## <a name="videotrack-element"></a><a name="VideoTrack"></a>VideoTrack-element
Ett specifikt videospår i den överordnade AssetFile.  

Du hittar ett XML-exempel på [XML.](#xml)  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:int** |Nollbaserat index för det här videospåret. **Anm.:**  Detta **ID** är inte nödvändigtvis TrackID som används i en MP4-fil. |
| **Fourcc**<br/><br/> Krävs |**xs:sträng** |Video codec FourCC-kod. |
| **Profil** |**xs:sträng** |H264-profil (gäller endast H264 codec). |
| **Nivå** |**xs:sträng** |H264-nivå (gäller endast H264 codec). |
| **Bredd**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:int** |Kodad videobredd i pixlar. |
| **Höjd**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:int** |Kodad videohöjd i pixlar. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:dubbel** |Videovisning av bildförhållande täljare. |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:dubbel** |Konfessionen för bildproportioner. |
| **Bildfrekvens**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:decimal** |Uppmätt videobildhastighet i .3f-format. |
| **TargetFramerate**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:decimal** |Förinställd videobildbildhastighet i 0,3f-format. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:int** |Genomsnittlig videobithastighet i kilobit per sekund, beräknat från AssetFile. Räknar endast den elementära strömnyttolasten och inkluderar inte förpackningens omkostnader. |
| **TargetBitrate (MålBitrat)**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:int** |Målgenomsnittsbitrat för det här videospåret, som begärs via kodningsförinställningen, i kilobit per sekund. |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |Max GOP genomsnittliga bitrate för denna video spår, i kilobits per sekund. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a>Elementet AudioTracks
Varje fysisk AssetFile kan innehålla noll eller fler ljudspår som inte är anpassade till ett lämpligt behållarformat. **AudioTracks-elementet** representerar en samling av alla dessa ljudspår.  

Du hittar ett XML-exempel på [XML.](#xml)  

### <a name="child-elements"></a>Underordnade element
| Namn | Beskrivning |
| --- | --- |
| **AudioTrack (ljudspår)**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Ett specifikt ljudspår i den överordnade AssetFile. Mer information finns i AudioTrack-elementet. |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a>AudioTrack-element
Ett specifikt ljudspår i den överordnade AssetFile.  

Du hittar ett XML-exempel på [XML.](#xml)  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:int** |Nollbaserat index för det här ljudspåret. **Anm.:**  Detta är inte nödvändigtvis TrackID som används i en MP4-fil. |
| **Codec** |**xs:sträng** |Ljudspår codec sträng. |
| **EncoderVersion** |**xs:sträng** |Valfri kodarversionssträng, som krävs för EAC3. |
| **Kanaler**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:int** |Antal ljudkanaler. |
| **SamplingTra**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:int** |Ljudsamplingshastighet i sampling/sek eller Hz. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:int** |Genomsnittlig ljudbithastighet i bitar per sekund, beräknat från AssetFile. Räknar endast den elementära strömnyttolasten och inkluderar inte förpackningens omkostnader. |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> Krävs |**xs:int** |Bitar per prov för formattypen wFormatTag. |

### <a name="child-elements"></a>Underordnade element
| Namn | Beskrivning |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |Loudness mätning resultatparametrar. Mer information finns i LoudnessMeteringResultParameters element. |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a>LoudnessMeteringResultParameters element
Loudness mätning resultatparametrar.  

Du hittar ett XML-exempel på [XML.](#xml)  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:sträng** |**Dolby** professionell loudness mätning utveckling kit version. |
| **DialogNormalisering**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> Krävs |**xs:int** |DialogNormalisering som genereras via DPLM, krävs när LoudnessMetering är inställd |
| **Integreradlighet**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> Krävs |**xs:flyta** |Integrerad ljudstyrka |
| **IntegratedLoudnessUnit**<br/><br/> Krävs |**xs:sträng** |Integrerad loudness enhet. |
| **IntegreradLoudnessGatingMethod**<br/><br/> Krävs |**xs:sträng** |Gating-identifierare |
| **IntegreradLoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:flyta** |Talinnehåll över programmet, i procent. |
| **SamplePeak**<br/><br/> Krävs |**xs:flyta** |Högsta absoluta provvärde, sedan återställningen eller sedan det senast rensades, per kanal.  Enheterna är dBFS. |
| **SamplePeakUnit**<br/><br/> fast="dBFS"<br/><br/> Krävs |**xs:anySimpleType** |Provtoppenhet. |
| **TruePeak (1)**<br/><br/> Krävs |**xs:flyta** |Högsta verkliga toppvärde enligt ITU-R BS.1770-2, sedan återställningen eller sedan den senast rensades per kanal. Enheterna är dBTP. |
| **TruePeakUnit (Senaste det är sant)**<br/><br/> fast="dBTP"<br/><br/> Krävs |**xs:anySimpleType** |True peak enhet. |

## <a name="schema-code"></a>Schemakod
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               elementFormDefault="qualified">  
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
                  <xs:element name="Sources">  
                    <xs:annotation>  
                      <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:attribute name="Name" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>input source file name</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
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
                            <xs:attribute name="FourCC" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video codec FourCC code</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Profile" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Level" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                            <xs:attribute name="Framerate" use="required">  
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
                            <xs:attribute name="TargetFramerate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:decimal">  
                                  <xs:minInclusive value="0"/>  
                                  <xs:fractionDigits value="3"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="TargetBitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:sequence>  
                              <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                                <xs:annotation>  
                                  <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                                </xs:annotation>  
                                <xs:complexType>  
                                  <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="DialogNormalization" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:int">  
                                        <xs:minInclusive value="-31"/>  
                                        <xs:maxInclusive value="-1"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudness" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation>Integrated loudness</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="-70"/>  
                                        <xs:maxInclusive value="10"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Gating identifier</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                    <xs:annotation>  
                                      <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="0"/>  
                                        <xs:maxInclusive value="100"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                                  </xs:attribute>  
                                </xs:complexType>  
                              </xs:element>  
                            </xs:sequence>  
                            <xs:attribute name="Id" use="required">  
                              <xs:annotation>  
                                <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Codec" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>audio track codec string</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="EncoderVersion" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="BitsPerSample" use="required">  
                              <xs:annotation>  
                                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
                <xs:attribute name="Duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:duration"/>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  



## <a name="xml-example"></a><a name="xml"></a>EXEMPEL PÅ XML

Följande XML är ett exempel på metadatafilen för utdata.  

    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
                xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
      <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
