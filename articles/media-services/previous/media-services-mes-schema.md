---
title: Media Encoder Standard schemat | Microsoft Docs
description: Artikeln ger en översikt över Media Encoder Standard schemat.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 837235e04ce190a4481e1f19789d8e9ff9cb7578
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61131593"
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard-schema
Den här artikeln beskriver några av de elementen och typer av XML-schema som [Media Encoder Standard förinställningar](media-services-mes-presets-overview.md) baseras. Artikeln ger förklaring av element och deras giltiga värden.  

## <a name="Preset"></a> Förinställning (rotelementet)
Definierar en förinställningen för kodningen.  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Kodning** |[Kodning](media-services-mes-schema.md#Encoding) |Rotelementet, visar att indatakällorna ska kodas. |
| **Utdata** |[Utdata](media-services-mes-schema.md#Output) |Samling av önskad utdatafilerna. |
| **StretchMode**<br/>minOccurs="0"<br/>default="AutoSize|Xs:String|Styra videon bildrutestorlek, utfyllnad, pixel eller visa proportionerna. **StretchMode** kan vara något av följande värden: **Ingen**, **AutoSize** (standard), eller **Autopassa**.<br/><br/>**Ingen**: Strikt följer på utdataupplösningen (till exempel den **bredd** och **höjd** i förinställningen) utan att överväga pixelproportionerna eller visa proportionerna på indatavideon. Rekommenderas i scenarier som [beskärning](media-services-crop-video.md), där utdata videon har andra proportioner jämfört med indata. <br/><br/>**AutoSize**: Upplösningen för utdata kommer passar i fönstret (bredd * höjd) anges av förinställning. Kodaren ger dock en utdata-videon som innehåller den fyrkantiga (1:1) pixlar proportionerna. Därför antingen utdata bredd eller höjd-utdata kan åsidosättas för att matcha visningsproportionerna av indata utan utfyllnad. Till exempel om indata är 1 920 x 1 080 och förinställningen för kodningen begär 1 280 x 1 280, sedan höjdvärdet i förinställningen åsidosätts och utdata är tillgänglig med minst 1 280 x 720 som innehåller indata förhållandet 16:9. <br/><br/>**AutoFit**: Om det behövs, Fyll ut utdata-video (med brevlåda eller pillarbox-format) för att respektera på önskad utdataupplösningen, samtidigt som man säkerställer att den aktiva video regionen i utdata har samma proportioner som indata. Anta exempelvis att indata är 1 920 x 1 080 och förinställningen för kodningen begär 1 280 x 1 280. Sedan utdata video är tillgänglig med minst 1 280 x 1 280, men den kommer att innehålla en inre 1 280 x 720 rektangel 'active video ”med proportionerna 16:9 och brevlåda regioner 280 bildpunkter hög högst upp och längst ned. För ett annat exempel är om indata är 1 440 x 1 080 och förinställningen för kodningen begär 1 280 x 720 är sedan utdata tillgänglig med minst 1 280 x 720 som innehåller en inre rektangel av 960 x 720 proportionerna på 4:3 och pelare box regioner 160 pixlar bred på vänster och höger. 

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Version**<br/><br/> Krävs |**xs: decimal** |Den förinställda versionen. Följande begränsningar gäller: xs:fractionDigits värde = ”1” och xs:minInclusive value = ”1” till exempel **version = ”1.0”**. |

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
| **TwoPass**<br/><br/> minOccurs="0" |**Xs:Boolean** |För närvarande stöds endast en pass kodning. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**Xs: Time** |Anger fast avståndet mellan IDR bildrutor i antal sekunder. Kallas även GOP-varaktighet. Se **SceneChangeDetection** för att kontrollera om kodaren kan avvika från det här värdet. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> standard = ”false” |**Xs: booleskt** |Om värdet är sant, kodare försöker identifiera scen ändringar i videon och infogar en IDR ram. |
| **Komplexitet**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**Xs:String** |Styr en kompromiss mellan koda hastigheten och video. Kan vara något av följande värden: **Hastighet**, **belastningsutjämnade**, eller **kvalitet**<br/><br/> Standard: **Belastningsutjämnade** |
| **SyncMode**<br/><br/> minOccurs="0" | |Funktionen exponeras i en framtida version. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Samling av utdata video lager. |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **villkor** |**Xs:String** | När indata har ingen bild kan du tvinga kodaren att infoga ett svartvit video spår. Gör detta genom att använda villkor = ”InsertBlackIfNoVideoBottomLayerOnly” (för att infoga en video på lägsta bithastigheten) eller ett villkor = ”InsertBlackIfNoVideo” (att infoga en video på alla utmatningen bithastighet). Mer information finns i [den här artikeln](media-services-advanced-encoding-with-mes.md#no_video).|

## <a name="H264Layers"></a> H264Layers

Som standard innehåller utdatatillgången filer med endast ljud data om du skickar indata till den kodare som innehåller endast ljud och ingen bild. Vissa spelare kanske inte kan hantera sådana utdataströmmar. Du kan använda H264Video **InsertBlackIfNoVideo** attributet inställningen för att tvinga kodaren att lägga till en video spåra till utdata i det här scenariot. Mer information finns i [den här artikeln](media-services-advanced-encoding-with-mes.md#no_video).
              
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |En samling av H264 lager. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> Videogränser baseras på de värden som beskrivs i den [H264 nivåer](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tabell.  
> 
> 

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**Xs: sträng** |Kan vara i något av följande **xs: sträng** värden: **Automatisk**, **baslinje**, **Main**, **hög**. |
| **Nivå**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**Xs: sträng** | |
| **Bithastighet**<br/><br/> minOccurs="0" |**xs:int** |Bithastigheten används för den här videon skikt som angetts i kbit/s. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**Xs: int** |Den högsta bithastighet som används för den här videon skikt som angetts i kbit/s. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**Xs: tid** |Video buffertens längd. |
| **Bredd**<br/><br/> minOccurs="0" |**Xs: int** |Bredd för utdata video ramen, i bildpunkter.<br/><br/> För närvarande måste du ange både bredd och höjd. Bredden och höjden måste vara jämna tal. |
| **Höjd**<br/><br/> minOccurs="0" |**xs:int** |Höjd för utdata video ramen, i bildpunkter.<br/><br/> För närvarande måste du ange både bredd och höjd. Bredden och höjden måste vara jämna tal.|
| **BFrames**<br/><br/> minOccurs="0" |**Xs: int** |Antal B bildrutor mellan referens bildrutor. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> default=”3” |**xs:int** |Antal referens bildrutor i en GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> standard = ”Cabac” |**Xs: sträng** |Kan vara något av följande värden: **Cabac** och **Cavlc**. |
| **Ramhastighet**<br/><br/> minOccurs="0" |rationellt tal |Anger bildfrekvens videons utdata. Använd standardvärdet ”0/1” så att kodaren använder samma bildfrekvens som indata video. Tillåtna värden förväntas vara vanliga video bildrutehastigheter. Men alla giltiga rationella tillåts. Till exempel 1/1 skulle vara 1 fps och är giltig.<br/><br/> – 12/1 (12 fps)<br/><br/> – 15/1 (15 fps)<br/><br/> -24/1 (24 fps)<br/><br/> 24000/1001 (23.976 fps)<br/><br/> -25/1 (25 fps)<br/><br/>  -30/1 (30 bilder per sekund)<br/><br/> 30000/1001 (29,97 fps) <br/> <br/>**Obs** om du skapar en anpassad förinställning för flera bithastigheter encoding, sedan alla lager i förinställningen **måste** använda samma värde för ramhastighet.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**Xs: booleskt** |Kopiera från Azure media encoder |
| **Sektorer**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Anger hur många segment som en ram är uppdelad i. Rekommendera att du använder standard. |

## <a name="AACAudio"></a> AACAudio
 Innehåller en sekvens av följande element och grupper.  

 Läs mer om AAC [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**Xs: sträng** |Kan vara något av följande värden: **AACLC**, **HEAACV1**, eller **HEAACV2**. |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **villkor** |**Xs: sträng** |Ange värdet som ”InsertSilenceIfNoAudio” om du vill framtvinga kodaren att skapa en tillgång som innehåller en tyst ljudspår när indata har inget ljud.<br/><br/> Som standard om du skickar indata till den kodare som innehåller endast video och inga ljud innehåller utdatatillgången filer som innehåller endast video data. Vissa spelare kanske inte kan hantera sådana utdataströmmar. Du kan använda den här inställningen för att tvinga kodaren att lägga till en tyst ljudspår till utdata i det här scenariot. |

### <a name="groups"></a>Grupper

| Referens | Beskrivning |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Se beskrivning av [AudioGroup](media-services-mes-schema.md#AudioGroup) veta hur många kanaler, samplingsfrekvensen och bithastighet som kan ställas in för varje profil. |

## <a name="AudioGroup"></a> AudioGroup
Mer information om vilka värden är giltiga för varje profil finns i ”ljud codec information”-tabellen som följer.  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **kanaler**<br/><br/> minOccurs="0" |**Xs: int** |Antal ljud kanaler kodad. Följande är giltiga alternativ: 1, 2, 5, 6, 8.<br/><br/> Standard: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**Xs: int** |Ljud samplingsfrekvensen, anges i Hz. |
| **Bithastighet**<br/><br/> minOccurs="0" |**Xs: int** |Bithastigheten används när kodning ljudet, anges i kbit/s. |

### <a name="audio-codec-details"></a>Ljudcodec information

Ljudcodec|Information  
-----------------|---  
**AACLC** |1:<br/><br/> - 11025: 8 &lt;= bithastighet &lt; 16<br/><br/> - 12000: 8 &lt;= bithastighet &lt; 16<br/><br/> - 16000: 8 &lt;= bithastighet &lt;32<br/><br/>- 22050: 24 &lt;= bithastighet &lt; 32<br/><br/> - 24000: 24 &lt;= bithastighet &lt; 32<br/><br/> - 32000: 32 &lt;= bithastighet &lt;= 192<br/><br/> - 44100: 56 &lt;= bithastighet &lt;= 288<br/><br/> - 48000: 56 &lt;= bithastighet &lt;= 288<br/><br/> - 88200 : 128 &lt;= bithastighet &lt;= 288<br/><br/> - 96000 : 128 &lt;= bithastighet &lt;= 288<br/><br/> 2:<br/><br/> - 11025: 16 &lt;= bithastighet &lt; 24<br/><br/> - 12000: 16 &lt;= bithastighet &lt; 24<br/><br/> - 16000: 16 &lt;= bithastighet &lt; 40<br/><br/> - 22050: 32 &lt;= bithastighet &lt; 40<br/><br/> - 24000 : 32 &lt;= bithastighet &lt; 40<br/><br/> - 32000:  40 &lt;= bithastighet &lt;= 384<br/><br/> - 44100: 96 &lt;= bithastighet &lt;= 576<br/><br/> - 48000 : 96 &lt;= bithastighet &lt;= 576<br/><br/> - 88200: 256 &lt;= bithastighet &lt;= 576<br/><br/> - 96000: 256 &lt;= bithastighet &lt;= 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt;= bithastighet &lt;= 896<br/><br/> - 44100: 240 &lt;= bithastighet &lt;= 1 024<br/><br/> - 48000: 240 &lt;= bithastighet &lt;= 1 024<br/><br/> - 88200: 640 &lt;= bithastighet &lt;= 1 024<br/><br/> - 96000: 640 &lt;= bithastighet &lt;= 1 024<br/><br/> 8:<br/><br/> - 32000 : 224 &lt;= bithastighet &lt;= 1 024<br/><br/> - 44100 : 384 &lt;= bithastighet &lt;= 1 024<br/><br/> - 48000: 384 &lt;= bithastighet &lt;= 1 024<br/><br/> - 88200: 896 &lt;= bithastighet &lt;= 1 024<br/><br/> - 96000: 896 &lt;= bithastighet &lt;= 1 024  
**HEAACV1** |1:<br/><br/> -22050: bithastighet = 8<br/><br/> - 24000: 8 &lt;= bithastighet &lt;= 10<br/><br/> - 32000: 12 &lt;= bithastighet &lt;= 64<br/><br/> - 44100: 20 &lt;= bithastighet &lt;= 64<br/><br/> - 48000: 20 &lt;= bithastighet &lt;= 64<br/><br/> -88200: bithastighet = 64<br/><br/> 2:<br/><br/> - 32000: 16 &lt;= bithastighet &lt;= 128<br/><br/> - 44100: 16 &lt;= bithastighet &lt;= 128<br/><br/> - 48000: 16 &lt;= bithastighet &lt;= 128<br/><br/> - 88200 : 96 &lt;= bithastighet &lt;= 128<br/><br/> - 96000: 96 &lt;= bithastighet &lt;= 128<br/><br/> 5/6:<br/><br/> - 32000 : 64 &lt;= bithastighet &lt;= 320<br/><br/> - 44100: 64 &lt;= bithastighet &lt;= 320<br/><br/> - 48000: 64 &lt;= bithastighet &lt;= 320<br/><br/> - 88200 : 256 &lt;= bithastighet &lt;= 320<br/><br/> - 96000: 256 &lt;= bithastighet &lt;= 320<br/><br/> 8:<br/><br/> - 32000: 96 &lt;= bithastighet &lt;= 448<br/><br/> - 44100: 96 &lt;= bithastighet &lt;= 448<br/><br/> - 48000: 96 &lt;= bithastighet &lt;= 448<br/><br/> - 88200: 384 &lt;= bithastighet &lt;= 448<br/><br/> - 96000: 384 &lt;= bithastighet &lt;= 448  
**HEAACV2** |2:<br/><br/> - 22050: 8 &lt;= bithastighet &lt;= 10<br/><br/> - 24000: 8 &lt;= bithastighet &lt;= 10<br/><br/> - 32000: 12 &lt;= bithastighet &lt;= 64<br/><br/> - 44100: 20 &lt;= bithastighet &lt;= 64<br/><br/> - 48000: 20 &lt;= bithastighet &lt;= 64<br/><br/> - 88200: 64 &lt;= bithastighet &lt;= 64  
  
## <a name="Clip"></a> Clip
### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **StartTime** |**xs:duration** |Anger starttiden för en presentation. Värdet för StartTime måste matcha absolut tidsstämplarna på indatavideon. Till exempel om den första bildrutan på indatavideon har en tidsstämpel för 12:00:10.000, sedan StartTime bör vara minst 12:00:10.000 eller större. |
| **Varaktighet** |**xs:duration** |Varaktighet för en presentation (till exempel utseendet på ett överlägg i videon). |

## <a name="Output"></a> Utdata
### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Filnamn** |**Xs:String** |Namnet på utdatafilen.<br/><br/> Du kan använda makron som beskrivs i följande tabell för att skapa utdata-filnamn. Exempel:<br/><br/> **”Utdata”: [{”FileName” ”: {Basename}*{matchning}*{bithastighet} .mp4”, ”Format”: {”Type”: "MP4Format"       }     }   ]** |

### <a name="macros"></a>Makron

| Makrot | Beskrivning |
| --- | --- |
| **{Basename}** |Om du genomför VoD-kodning, är de första 32 tecknen i egenskapen AssetFile.Name för den primära filen i indatatillgången {Basename}.<br/><br/> Om indatatillgången är en live-arkivet, härleds {Basename} från trackName attribut i server-manifestet. Om du skickar en underklipp-jobb med hjälp av TopBitrate, som i ”: < VideoStream\>TopBitrate < / VideoStream\>”, och utdatafilen innehåller videon blir {Basename} är de första 32 tecknen i trackName skiktets video med högsta bithastighet.<br/><br/> Om du skickar i stället ett underklipp jobb med hjälp av alla indata bithastighet, till exempel ”< VideoStream\>* < / VideoStream\>”, och utdatafilen innehåller videon blir {Basename} är de första 32 tecknen i trackName av den motsvarande video lager. |
| **{Codec}** |Mappas till ”H264” för video och ”AAC” för ljud. |
| **{Bitrate}** |Target video bithastigheten om utdatafilen innehåller video och ljud eller önskad ljud bithastighet om utdatafilen innehåller endast ljud. Det värde som används är bithastigheten i kbit/s. |
| **{Channel}** |Ljud kanal antal om filen innehåller ljud. |
| **{Width}** |Bredden på videon i bildpunkter i filen, om filen innehåller video. |
| **{Höjd}** |Höjd för videon i bildpunkter i filen, om filen innehåller video. |
| **{Extension}** |Ärver från egenskapen ”Type” för utdatafilen. Namnet på utdatafilen har ett tillägg som är en av: ”mp4”, ”ts”, ”jpg”, ”png” eller ”bmp”. |
| **{Index}** |Obligatorisk för miniatyrbilden. Ska bara finnas en gång. |

## <a name="Video"></a> Video (komplex typ ärver från Codec)
### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Börja** |**Xs:String** | |
| **Steg** |**Xs:String** | |
| **Adressintervall** |**Xs:String** | |
| **PreserveResolutionAfterRotation** |**Xs:Boolean** |Detaljerad förklaring finns i följande avsnitt: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Det rekommenderas att använda den **PreserveResolutionAfterRotation** flaggan i kombination med upplösningar uttryckt i procent (Width = ”100%”, höjd = ”100%”).  

Som standard riktas koda upplösningsinställningarna (bredd, höjd) i Media Encoder Standard (MES) förinställningar videor med 0 grader rotation. Exempel: om din indatavideo är 1 280 x 720 med noll-graders rotation, standardförinställningar ser du till att utdata har samma upplösning.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Om indatavideon har spelats in med noll rotation (till exempel en smartphone eller surfplatta som hålls i lodrätt), sedan MES som standard gäller inställningarna för matchning koda (bredd, höjd) för indata video och sedan kompensera för rotationen. Till exempel se bilden nedan. Förinställningen använder Width = ”100%”, Height = ”100%”, vilket MES tolkas som att kräva att utdata ska vara minst 1 280 bildpunkter på bredden och 720 bildpunkter. När du roterar videon krymper sedan bilden ska passas in i fönstret, vilket leder till pillar-box områden till vänster och höger.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternativt kan du använda den **PreserveResolutionAfterRotation** flaggan och ge den värdet ”true” (standardvärdet är ”false”). Så om din förinställda har bredd = ”100%”, Height = ”100%” och värdet ”true”, en indatavideon PreserveResolutionAfterRotation som är minst 1 280 bildpunkter på bredden och 720 bildpunkter med 90 grader rotation producerar utdata med noll-graders rotation, men 720 bildpunkter bred och 1280 bildpunkter. Se följande bild:  

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
| **Bredd**<br/><br/> minOccurs="0" |**xs:int** | |
| **Höjd**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **villkor** |**Xs:String** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs="0" |**xs:int** | |
| **Höjd**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **villkor** |**Xs:String** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs="0" |**xs:int** | |
| **Höjd**<br/><br/> minOccurs="0" |**xs:int** | |
| **Kvalitet**<br/><br/> minOccurs="0" |**xs:int** |Giltiga värden: 1(worst)-100(Best) |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **villkor** |**Xs:String** | |

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
Se exempel på XML-förinställningar som byggts baserat på det här schemat finns i [uppgift förinställningar för MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

