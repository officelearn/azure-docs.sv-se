---
title: Media Encoder Standard schemat | Microsoft Docs
description: Artikeln ger en översikt över Media Encoder Standard schemat.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: e936f5c47abe5bb5531f9af3be48662ea2f48c97
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard schema
Den här artikeln beskrivs några av de elementen och typer av XML-schemat som [Media Encoder Standard förinställningar](media-services-mes-presets-overview.md) baseras. Artikeln ger förklaring av element och sina giltiga värden.  

## <a name="Preset"></a> Förinställda (rotelementet)
Definierar en kodning förinställning.  

### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Kodning** |[Kodning](media-services-mes-schema.md#Encoding) |Rotelementet, visar att indatakällor ska kodas. |
| **Utdata** |[Utdata](media-services-mes-schema.md#Output) |Samling av önskade utdatafilerna. |

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Version**<br/><br/> Krävs |**Xs: decimal** |Den förinställda versionen. Följande begränsningar gäller: xs:fractionDigits värde = ”1” och xs:minInclusive value = ”1” till exempel **version = ”1.0”**. |

## <a name="Encoding"></a> Kodning
Innehåller en sekvens av följande element:  

### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Inställningar för H.264-kodning av video. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Inställningar för AAC kodning av ljud. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Inställningar för bitmappsbild. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Inställningar för Png-bild. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Inställningar för Jpg-bild. |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**Xs:Boolean** |För närvarande stöds endast en pass-kodning. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**Xs: Time** |Anger fast avstånd mellan IDR ramar i antal sekunder. Kallas även GOP varaktighet. Se **SceneChangeDetection** för att styra om kodaren kan avvika från det här värdet. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> standard = ”false” |**Xs: booleskt** |Om värdet är true, kodare försöker identifiera scen ändra i videon och infogar en IDR ram. |
| **Komplexitet**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**Xs:String** |Styr en kompromiss mellan koda hastigheten och video. Kan vara någon av följande värden: **hastighet**, **balanserad**, eller **kvalitet**<br/><br/> Standard: **belastningsutjämnade** |
| **SyncMode**<br/><br/> minOccurs="0" | |Funktionen kommer att exponeras i en framtida version. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Samling av utdata video lager. |

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Villkor** |**Xs:String** | När indata har ingen bild, kanske du vill tvinga kodaren att infoga ett monokromt video spår. Göra genom att använda villkor = ”InsertBlackIfNoVideoBottomLayerOnly” (för att infoga en video på endast de lägsta bithastigheten) eller villkor = ”InsertBlackIfNoVideo” (att infoga en video på alla utdata bithastighet). Mer information finns i [den här artikeln](media-services-advanced-encoding-with-mes.md#no_video).|

## <a name="H264Layers"></a> H264Layers

Om du skickar indata till den kodare som innehåller endast ljud och ingen bild innehåller utdatatillgången som standard filer med ljud data. Vissa spelare kanske inte kan hantera dessa utdataströmmar. Du kan använda H264Video **InsertBlackIfNoVideo** attributet inställningen för att tvinga kodaren att lägga till en video Spåra utdata i scenariot. Mer information finns i [den här artikeln](media-services-advanced-encoding-with-mes.md#no_video).
              
### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |En samling H264 lager. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> Video gränsvärdena baseras på de värden som beskrivs i den [H264 nivåer](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tabell.  
> 
> 

### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> standard = ”Auto” |**Xs: sträng** |Kan bero på något av följande **xs: sträng** värden: **automatisk**, **baslinjen**, **Main**, **hög**. |
| **Nivå**<br/><br/> minOccurs="0"<br/><br/> standard = ”Auto” |**Xs: sträng** | |
| **Bitrate**<br/><br/> minOccurs="0" |**Xs:int** |Bithastighet som används för den här videon skikt som angetts i kbit/s. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**Xs: int** |Den maximala bithastighet som används för den här videon skikt som angetts i kbit/s. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**Xs: tid** |Video buffertens längd. |
| **Bredd**<br/><br/> minOccurs="0" |**Xs: int** |Bredden på utdata video ramen, i bildpunkter.<br/><br/> För närvarande måste du ange både bredd och höjd. Bredden och höjden måste vara jämnt tal. |
| **Höjd**<br/><br/> minOccurs="0" |**Xs:int** |Höjden på utdata video ramen, i bildpunkter.<br/><br/> För närvarande måste du ange både bredd och höjd. Bredden och höjden måste vara jämnt tal.|
| **BFrames**<br/><br/> minOccurs="0" |**Xs: int** |Antal B ramar mellan referens ramar. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> standard = ”3” |**Xs:int** |Antal bildrutor referens i en GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> standard = ”Cabac” |**Xs: sträng** |Kan vara någon av följande värden: **Cabac** och **Cavlc**. |
| **Ramhastighet**<br/><br/> minOccurs="0" |rationellt tal |Anger bildfrekvens av utdata-video. Använd standardvärdet ”0-1” om du vill låta kodaren använder samma bildfrekvens som indata video. Tillåtna värden förväntas vara vanliga bildruta priser. Men alla giltiga rationell tillåts. 1-1 är 1 fps och är giltigt.<br/><br/> -12-1 (12 fps)<br/><br/> -15-1 (15 fps)<br/><br/> -24-1 (24 fps)<br/><br/> 24000/1001 (23.976 fps)<br/><br/> -25-1 (25 fps)<br/><br/>  -30-1 (30 fps)<br/><br/> 30000/1001 (29,97 fps) <br/> <br/>**Obs** om du skapar en anpassad förinställning för flera bithastigheter kodning sedan alla lager i förinställningen **måste** använda ramhastighet samma värde.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**Xs: booleskt** |Kopiera från Azure media-kodaren |
| **Segment**<br/><br/> minOccurs="0"<br/><br/> default="0" |**Xs:int** |Anger hur många segment som en ram är uppdelad i. Du bör använda standard. |

## <a name="AACAudio"></a> AACAudio
 Innehåller en sekvens av följande element och grupper.  

 Läs mer om AAC [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = ”0”<br/><br/> default="AACLC" |**Xs: sträng** |Kan vara någon av följande värden: **AACLC**, **HEAACV1**, eller **HEAACV2**. |

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Villkor** |**Xs: sträng** |Ange värdet ”InsertSilenceIfNoAudio” om du vill tvinga kodaren att skapa en tillgång som innehåller en tyst ljud spåra när indata har inget ljud.<br/><br/> Som standard om du skickar indata till den kodare som bara innehåller video, och inget ljud innehåller utdatatillgången filer som innehåller endast video data. Vissa spelare kanske inte kan hantera dessa utdataströmmar. Du kan använda den här inställningen för att tvinga kodaren att lägga till en tyst ljud Spåra utdata i scenariot. |

### <a name="groups"></a>Grupper
| Referens | Beskrivning |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Se beskrivningen av [AudioGroup](media-services-mes-schema.md#AudioGroup) veta ett lämpligt antal kanaler, samplingsfrekvensen och bithastighet som kan anges för varje profil. |

## <a name="AudioGroup"></a> AudioGroup
Mer information om vilka värden är giltiga för varje profil finns i tabellen ”ljud codec information” nedan.  

### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **kanaler**<br/><br/> minOccurs="0" |**Xs: int** |Antal ljud kanaler kodad. Följande är giltiga alternativ: 1, 2, 5, 6, 8.<br/><br/> Standard: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**Xs: int** |Ljud samplingsfrekvensen, anges i Hz. |
| **Bitrate**<br/><br/> minOccurs="0" |**Xs: int** |Bithastighet som används när kodning ljud, anges i kbit/s. |

### <a name="audio-codec-details"></a>Ljud-codec information
Ljud-Codec|Information  
-----------------|---  
**AACLC**|1:<br/><br/> -11025: 8 &lt;= bithastighet &lt; 16<br/><br/> -12000: 8 &lt;= bithastighet &lt; 16<br/><br/> -16000: 8 &lt;= bithastighet &lt;32<br/><br/>-22050: 24 &lt;= bithastighet &lt; 32<br/><br/> -24000: 24 &lt;= bithastighet &lt; 32<br/><br/> -32000: 32 &lt;= bithastighet &lt;= 192<br/><br/> -44100: 56 &lt;= bithastighet &lt;= 288<br/><br/> -48000: 56 &lt;= bithastighet &lt;= 288<br/><br/> -88200: 128 &lt;= bithastighet &lt;= 288<br/><br/> -96000: 128 &lt;= bithastighet &lt;= 288<br/><br/> 2:<br/><br/> -11025: 16 &lt;= bithastighet &lt; 24<br/><br/> -12000: 16 &lt;= bithastighet &lt; 24<br/><br/> -16000: 16 &lt;= bithastighet &lt; 40<br/><br/> -22050: 32 &lt;= bithastighet &lt; 40<br/><br/> -24000: 32 &lt;= bithastighet &lt; 40<br/><br/> -32000: 40 &lt;= bithastighet &lt;= 384<br/><br/> -44100: 96 &lt;= bithastighet &lt;= 576<br/><br/> -48000: 96 &lt;= bithastighet &lt;= 576<br/><br/> -88200: 256 &lt;= bithastighet &lt;= 576<br/><br/> -96000: 256 &lt;= bithastighet &lt;= 576<br/><br/> 5/6:<br/><br/> -32000: 160 &lt;= bithastighet &lt;= 896<br/><br/> -44100: 240 &lt;= bithastighet &lt;= 1024<br/><br/> -48000: 240 &lt;= bithastighet &lt;= 1024<br/><br/> -88200: 640 &lt;= bithastighet &lt;= 1024<br/><br/> -96000: 640 &lt;= bithastighet &lt;= 1024<br/><br/> 8:<br/><br/> -32000: 224 &lt;= bithastighet &lt;= 1024<br/><br/> -44100: 384 &lt;= bithastighet &lt;= 1024<br/><br/> -48000: 384 &lt;= bithastighet &lt;= 1024<br/><br/> -88200: 896 &lt;= bithastighet &lt;= 1024<br/><br/> -96000: 896 &lt;= bithastighet &lt;= 1024  
**HEAACV1**|1:<br/><br/> -22050: bithastighet = 8<br/><br/> -24000: 8 &lt;= bithastighet &lt;= 10<br/><br/> -32000: 12 &lt;= bithastighet &lt;= 64<br/><br/> -44100: 20 &lt;= bithastighet &lt;= 64<br/><br/> -48000: 20 &lt;= bithastighet &lt;= 64<br/><br/> -88200: bithastighet = 64<br/><br/> 2:<br/><br/> -32000: 16 &lt;= bithastighet &lt;= 128<br/><br/> -44100: 16 &lt;= bithastighet &lt;= 128<br/><br/> -48000: 16 &lt;= bithastighet &lt;= 128<br/><br/> -88200: 96 &lt;= bithastighet &lt;= 128<br/><br/> -96000: 96 &lt;= bithastighet &lt;= 128<br/><br/> 5/6:<br/><br/> -32000: 64 &lt;= bithastighet &lt;= 320<br/><br/> -44100: 64 &lt;= bithastighet &lt;= 320<br/><br/> -48000: 64 &lt;= bithastighet &lt;= 320<br/><br/> -88200: 256 &lt;= bithastighet &lt;= 320<br/><br/> -96000: 256 &lt;= bithastighet &lt;= 320<br/><br/> 8:<br/><br/> -32000: 96 &lt;= bithastighet &lt;= 448<br/><br/> -44100: 96 &lt;= bithastighet &lt;= 448<br/><br/> -48000: 96 &lt;= bithastighet &lt;= 448<br/><br/> -88200: 384 &lt;= bithastighet &lt;= 448<br/><br/> -96000: 384 &lt;= bithastighet &lt;= 448  
**HEAACV2**|2:<br/><br/> -22050: 8 &lt;= bithastighet &lt;= 10<br/><br/> -24000: 8 &lt;= bithastighet &lt;= 10<br/><br/> -32000: 12 &lt;= bithastighet &lt;= 64<br/><br/> -44100: 20 &lt;= bithastighet &lt;= 64<br/><br/> -48000: 20 &lt;= bithastighet &lt;= 64<br/><br/> -88200: 64 &lt;= bithastighet &lt;= 64  
  

## <a name="Clip"></a> Clip
### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **StartTime** |**Duration** |Anger starttiden för en presentation. Värdet för StartTime måste matcha inkommande videon absolut tidsstämplar. Till exempel om den första bildrutan indata har en tidsstämpel för 12:00:10.000, sedan StartTime bör vara minst 12:00:10.000 eller större. |
| **Varaktighet** |**Duration** |Varaktighet för en presentation (till exempel utseendet på ett överlägg i videon). |

## <a name="Output"></a> Utdata
### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **FileName** |**Xs:String** |Namnet på utdatafilen.<br/><br/> Du kan använda makron som beskrivs i följande tabell för att skapa filen utdatanamn. Exempel:<br/><br/> **”Utdata”: [{”FileName” ”: {Basename}*{upplösning}*{bithastighet} .mp4”, ”Format”: {”typ”: ”MP4Format”}}]** |

### <a name="macros"></a>Makron
| Macro | Beskrivning |
| --- | --- |
| **{Basename}** |Om du gör VoD kodning, är {Basename} först 32 tecken från egenskapen AssetFile.Name för den primära filen i inkommande tillgången.<br/><br/> Om den inkommande är en levande Arkiv, härleds {Basename} från trackName attribut i manifestet server. Om du skickar ett underklipp jobb med hjälp av TopBitrate, som i ”: < VideoStream\>TopBitrate < / VideoStream\>”, och utdatafilen innehåller video och sedan {Basename} är den första 32 tecknen i trackName video skiktets med den högsta bithastigheten.<br/><br/> Om du skickar i stället ett underklipp jobb med hjälp av alla indata bithastighet som ”< VideoStream\>* < / VideoStream\>”, och utdatafilen innehåller video och sedan {Basename} är den första 32 tecknen i trackName av den motsvarande video lager. |
| **{Codec}** |Mappas till ”H264” video och ”AAC” för ljud. |
| **{Bitrate}** |På önskad video bithastighet om utdatafilen innehåller video och ljud eller mål ljud bithastighet om utdatafilen innehåller endast ljud. Det värde som används är bithastighet i kbit/s. |
| **{Channel}** |Antal ljud kanaler om filen innehåller ljud. |
| **{Bredd}** |Bredd, i bildpunkter i filen, om filen innehåller video. |
| **{Höjd}** |Höjden på video, i bildpunkter i filen, om filen innehåller video. |
| **{Tillägg}** |Ärver från egenskapen ”Type” för utdatafilen. Namnet på utdatafilen har ett tillägg som är en av: ”mp4”, ”ts”, ”jpg”, ”png” eller ”bmp”. |
| **{Index}** |Obligatorisk för miniatyr. Ska endast finnas en gång. |

## <a name="Video"></a> Video (komplex typ ärver från Codec)
### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Börja** |**Xs:String** | |
| **Steg** |**Xs:String** | |
| **intervallet** |**Xs:String** | |
| **PreserveResolutionAfterRotation** |**Xs:Boolean** |Detaljerad förklaring finns i följande avsnitt: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Det rekommenderas att använda den **PreserveResolutionAfterRotation** flaggan i kombination med upplösningar uttryckt i procent (Width = ”100%”, höjd = ”100%”).  

Som standard riktas koda inställningarna för matchning av (bredden och höjden) i Media Encoder Standard (MES) förinställningar videor med rotation 0 grader. Om din indatavideo är minst 1 280 x 720 med noll graders rotation, sedan kontrollera standardförinställningar exempelvis att utdata har samma upplösning.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Om inkommande video har spelats in med noll kan rotera (till exempel en smartphone eller surfplatta hålls lodrätt), sedan MES som standard tillämpar inställningarna för matchning av koda (bredden och höjden) inkommande video och sedan kompensera för rotationen. Till exempel se bilden nedan. Förinställningen använder Width = ”100%”, höjd = ”100%”, som MES tolkas som att kräva att utdata ska vara minst 1 280 bildpunkter och 720 bildpunkter. När du roterar videon krymper sedan bilden passar i fönstret, vilket leder till pillar-box områden till vänster och höger.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternativt kan du använda den **PreserveResolutionAfterRotation** flagga och inställd på ”true” (standardvärdet är ”false”). Så om förinställning har Width = ”100%”, höjd = ”100%” och PreserveResolutionAfterRotation inställt på ”true” och en indatavideo som är minst 1 280 bildpunkter och 720 bildpunkter med 90 grader rotation producerar utdata med noll graders rotation, men 720 bildpunkter bred och 1280 bildpunkter. Se följande bild:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a> FormatGroup (grupp)
### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs="0" |**Xs:int** | |
| **Höjd**<br/><br/> minOccurs="0" |**Xs:int** | |

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Villkor** |**Xs:String** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs="0" |**Xs:int** | |
| **Höjd**<br/><br/> minOccurs="0" |**Xs:int** | |

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Villkor** |**Xs:String** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs="0" |**Xs:int** | |
| **Höjd**<br/><br/> minOccurs="0" |**Xs:int** | |
| **Kvalitet**<br/><br/> minOccurs="0" |**Xs:int** |Giltiga värden: 1(worst)-100(best) |

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Villkor** |**Xs:String** | |

## <a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a> BmpImage (komplex typ ärver från Video)
### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG-lager |

## <a name="JpgImage"></a> JpgImage (komplex typ ärver från Video)
### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG-lager |

## <a name="PngImage"></a> PngImage (komplex typ ärver från Video)
### <a name="elements"></a>Element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG-lager |

## <a name="examples"></a>Exempel
Finns exempel på XML-förinställda som skapas utifrån det här schemat finns [aktivitet förinställningar för MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

