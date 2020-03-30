---
title: Standardschema för mediakodare | Microsoft-dokument
description: I den här artikeln beskrivs några av de element och typer i XML-schemat som mediakodarstandardförinställningar baseras på.
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
ms.openlocfilehash: 622f14beabb1f2f109dff5d28c1591ffdd5aa000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901437"
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard-schema
I den här artikeln beskrivs några av de element och typer i XML-schemat som [mediakodarstandardförinställningar](media-services-mes-presets-overview.md) baseras på. Artikeln ger förklaring av element och deras giltiga värden.  

## <a name="preset-root-element"></a><a name="Preset"></a>Förinställning (rotelement)
Definierar en kodningsförinställning.  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Kodning** |[Kodning](media-services-mes-schema.md#Encoding) |Rotelementet anger att indatakällorna ska kodas. |
| **Utdata** |[Utdata](media-services-mes-schema.md#Output) |Insamling av önskade utdatafiler. |
| **StretchMode (tändmode)**<br/>minOccurs="0"<br/>default="AutoSize|xs:sträng|Styr utdatavideobildrutans storlek, utfyllnad, pixel eller visningsproportioner. **StretchMode** kan vara ett av följande värden: **Ingen**, **AutoSize** (standard) eller **Autopassa**.<br/><br/>**Ingen**: Följ strikt utdataupplösningen (till exempel **bredden** och **höjden** i förinställningen) utan att ta hänsyn till pixelproportioner eller visningsproportionen för indatavideon. Rekommenderas i scenarier som [beskärning](media-services-crop-video.md), där utdatavideon har ett annat bildförhållande jämfört med indata. <br/><br/>**Autostorlek:** Utdataupplösningen får plats i fönstret (Bredd * Höjd) som anges av förinställningen. Kodaren producerar dock en utdatavideo med bildförhållandet (1:1) pixel. Därför kan antingen utdatabredd eller utdatahöjd åsidosättas för att matcha visningsproportionens bildförhållande, utan utfyllnad. Om indata till exempel är 1920x1080 och kodningsförinställningen frågar efter 1280x1280 åsidosätts höjdvärdet i förinställningen och utdata kommer att vara 1280x720, vilket upprätthåller ingångsproportionen på 16:9. <br/><br/>**Autopass:** Om det behövs, pad utdatavideon (med antingen brevlåda eller pelarlåda) för att uppfylla önskad utdataupplösning, samtidigt som du ser till att det aktiva videoområdet i utdata har samma bildförhållande som ingången. Anta till exempel att indata är 1920x1080 och kodningsförinställningen frågar efter 1280x1280. Då utdatavideon kommer att vara på 1280x1280, men den kommer att innehålla en inre 1280x720 rektangel av "aktiv video" med bildförhållande på 16:9, och brevlåderegioner 280 pixlar högt upp och ner. Ett annat exempel, om indata är 1440x1080 och kodningsförinställningen frågar efter 1280x720, kommer utdata att vara på 1280x720, som innehåller en inre rektangel på 960x720 med bildförhållandet 4:3 och pelarboxen 160 pixlar bred till vänster och höger. 

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Version**<br/><br/> Krävs |**xs: decimal** |Den förinställda versionen. Följande begränsningar gäller: xs:fractionDigits value="1" och xs:minInclusive value="1" Till exempel **version="1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a>Kodning
Innehåller en sekvens av följande element:  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Inställningar för H.264-kodning av video. |
| **AACAudio (på andra sätt)** |[AACAudio (på andra sätt)](media-services-mes-schema.md#AACAudio) |Inställningar för AAC-kodning av ljud. |
| **BmpImage (BmpImage)** |[BmpImage (BmpImage)](media-services-mes-schema.md#BmpImage) |Inställningar för Bmp-bild. |
| **PngImage (1)** |[PngImage (1)](media-services-mes-schema.md#PngImage) |Inställningar för Png-bild. |
| **JpgBild** |[JpgBild](media-services-mes-schema.md#JpgImage) |Inställningar för jpg-bild. |

## <a name="h264video"></a><a name="H264Video"></a>H264Video
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **TwoPass (två)**<br/><br/> minOccurs="0" |**xs:booleska** |För närvarande stöds endast en-pass-kodning. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:tid** |Bestämmer det fasta avståndet mellan IDR-bildrutorna i sekunder. Kallas även gop-varaktigheten. Se **SceneChangeDetection** för att kontrollera om kodaren kan avvika från det här värdet. |
| **ScenÄndringDetection**<br/><br/> minOccurs="0"<br/><br/> default="false" |**xs: booleska** |Om den är inställd på true försöker kodaren identifiera scenändringar i videon och infogar en IDR-ram. |
| **Komplexitet**<br/><br/> minOccurs="0"<br/><br/> default="Balanserad" |**xs:sträng** |Styr avvägningen mellan kodhastighet och videokvalitet. Kan vara ett av följande värden: **Hastighet,** **Balanserad**eller **Kvalitet**<br/><br/> Standard: **Balanserad** |
| **SyncMode**<br/><br/> minOccurs="0" | |Funktionen kommer att exponeras i en framtida version. |
| **H264Lager**<br/><br/> minOccurs="0" |[H264Lager](media-services-mes-schema.md#H264Layers) |Insamling av utdatavideolager. |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Villkor** |**xs:sträng** | När ingången inte har någon video kanske du vill tvinga kodaren att infoga ett svartvitt videospår. För att göra det, använd Condition="InsertBlackIfNoVideoBottomLayerOnly" (för att infoga en video på endast den lägsta bitrate) eller Condition ="InsertBlackIfNoVideo" (för att infoga en video vid alla utdatabithastigheter). Mer information finns i [den här](media-services-advanced-encoding-with-mes.md#no_video) artikeln.|

## <a name="h264layers"></a><a name="H264Layers"></a>H264Lager

Om du skickar en indata till kodaren som bara innehåller ljud och ingen video innehåller utdatatillgången som standard filer med endast ljuddata. Vissa spelare kanske inte kan hantera sådana utdataströmmar. Du kan använda H264Videos **insertBlackIfNoVideo-attributinställning** för att tvinga kodaren att lägga till ett videospår i utdata i det scenariot. Mer information finns i [den här](media-services-advanced-encoding-with-mes.md#no_video) artikeln.
              
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **H264Lager**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Lager](media-services-mes-schema.md#H264Layer) |En samling H264-lager. |

## <a name="h264layer"></a><a name="H264Layer"></a>H264Lager
> [!NOTE]
> Videogränser baseras på de värden som beskrivs i tabellen [H264-nivåer.](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels)  
> 
> 

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs: sträng** |Kan vara av något av följande **xs: strängvärden:** **Auto**, **Baslinje**, **Huvud**, **Hög**. |
| **Nivå**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs: sträng** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |Bithastigheten som används för det här videolagret, som anges i kbit/s. |
| **MaxBitrate (maxbitrate)**<br/><br/> minOccurs="0" |**xs: int** |Den maximala bithastighet som används för det här videolagret, som anges i kbps. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: tid** |Videobuffertens längd. |
| **Bredd**<br/><br/> minOccurs="0" |**xs: int** |Bredd på utdatavideoramen i bildpunkter.<br/><br/> För närvarande måste du ange både bredd och höjd. Bredden och höjden måste vara jämna tal. |
| **Höjd**<br/><br/> minOccurs="0" |**xs:int** |Höjden på utdatavideoramen i bildpunkter.<br/><br/> För närvarande måste du ange både bredd och höjd. Bredden och höjden måste vara jämna tal.|
| **BFrames (BFrames)**<br/><br/> minOccurs="0" |**xs: int** |Antal B-ramar mellan referensramar. |
| **Referensramar**<br/><br/> minOccurs="0"<br/><br/> default="3" |**xs:int** |Antal referensramar i en gop. |
| **EntropyMode (entropymode)**<br/><br/> minOccurs="0"<br/><br/> default="Cabac" |**xs: sträng** |Kan vara ett av följande värden: **Cabac** och **Cavlc**. |
| **Framerate**<br/><br/> minOccurs="0" |rationellt tal |Bestämmer bildfrekvensen för utdatavideon. Använd standardvärdet "0/1" för att låta kodaren använda samma bildhastighet som indatavideon. Tillåtna värden förväntas vara vanliga videobildhastigheter. Det är dock tillåtet att göra en giltig rationalisering. Till exempel skulle 1 / 1 vara 1 fps och är giltig.<br/><br/> - 12/1 (12 fps)<br/><br/> - 15/1 (15 fps)<br/><br/> - 24/1 (24 fps)<br/><br/> - 24000/1001 (23,976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30000/1001 (29,97 fps) <br/> <br/>**OBS** Om du skapar en anpassad förinställning för kodning med flera bithastigheter **måste** alla lager i förinställningen använda samma värde som FrameRate.|
| **AdaptivBFrame**<br/><br/> minOccurs="0" |**xs: booleska** |Kopiera från Azure media-kodare |
| **Skivor**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Bestämmer hur många segment en ram är uppdelad i. Rekommendera att du använder standard. |

## <a name="aacaudio"></a><a name="AACAudio"></a>AACAudio (på andra sätt)
 Innehåller en sekvens av följande element och grupper.  

 Mer information om AAC finns i [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: sträng** |Kan vara ett av följande värden: **AACLC**, **HEAACV1**eller **HEAACV2**. |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Villkor** |**xs: sträng** |Om du vill tvinga kodaren att producera en tillgång som innehåller ett tyst ljudspår när ingången inte har något ljud anger du värdet "InsertSilenceIfNoAudio".<br/><br/> Om du skickar en indata till kodaren som bara innehåller video och inget ljud innehåller utdatatillgången som standard filer som bara innehåller videodata. Vissa spelare kanske inte kan hantera sådana utdataströmmar. Du kan använda den här inställningen för att tvinga kodaren att lägga till ett tyst ljudspår i utdata i det scenariot. |

### <a name="groups"></a>Grupper

| Referens | Beskrivning |
| --- | --- |
| [AudioGroup (ljudgrupp)](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Se beskrivning av [AudioGroup](media-services-mes-schema.md#AudioGroup) för att veta lämpligt antal kanaler, samplingsfrekvens och bithastighet som kan ställas in för varje profil. |

## <a name="audiogroup"></a><a name="AudioGroup"></a>AudioGroup (ljudgrupp)
Mer information om vilka värden som gäller för varje profil finns i tabellen "Audio codec details" som följer.  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Kanaler**<br/><br/> minOccurs="0" |**xs: int** |Antalet ljudkanaler som kodats. Följande är giltiga alternativ: 1, 2, 5, 6, 8.<br/><br/> Standard: 2. |
| **SamplingTra**<br/><br/> minOccurs="0" |**xs: int** |Ljudsamplingshastigheten, som anges i Hz. |
| **Bitrate**<br/><br/> minOccurs="0" |**xs: int** |Bithastigheten som används vid kodning av ljudet, som anges i kbit/s. |

### <a name="audio-codec-details"></a>Information om ljudcodec

Ljud Codec|Information  
-----------------|---  
**AACLC (olika)** |1:<br/><br/> - 11025: &lt;8 &lt; = bithastighet 16<br/><br/> - 12000: &lt;8 &lt; = bitrate 16<br/><br/> - 16000: &lt;8 &lt;= bitrate 32<br/><br/>- 22050: 24 &lt; &lt; = bitrate 32<br/><br/> - 24000: 24 &lt; &lt; = bithastighet 32<br/><br/> - 32000: 32 &lt; &lt;= bitrate = 192<br/><br/> - 44100: 56 &lt; &lt;= bitrate = 288<br/><br/> - 48000: 56 &lt; &lt;= bitrate = 288<br/><br/> - 88200 : &lt;128 &lt;= bitrate = 288<br/><br/> - 96000 : &lt;128 &lt;= bitrate = 288<br/><br/> 2:<br/><br/> - 11025: 16 &lt; &lt; = bithastighet 24<br/><br/> - 12000: 16 &lt; &lt; = bithastighet 24<br/><br/> - 16000: 16 &lt; &lt; = bithastighet 40<br/><br/> - 22050: 32 &lt; &lt; = bithastighet 40<br/><br/> - 24000 : &lt;32 &lt; = bitrate 40<br/><br/> - 32000: 40 &lt; &lt;= bitrate = 384<br/><br/> - 44100: 96 &lt; &lt;= bitrate = 576<br/><br/> - 48000 : &lt;96 &lt;= bitrate = 576<br/><br/> - 88200: 256 &lt; &lt;= bitrate = 576<br/><br/> - 96000: 256 &lt; &lt;= bitrate = 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt; &lt;= bitrate = 896<br/><br/> - 44100: 240 &lt; &lt;= bithastighet = 1024<br/><br/> - 48000: 240 &lt; &lt;= bithastighet = 1024<br/><br/> - 88200: 640 &lt; &lt;= bitrate = 1024<br/><br/> - 96000: 640 &lt; &lt;= bithastighet = 1024<br/><br/> 8:<br/><br/> - 32000 : &lt;224 &lt;= bitrate = 1024<br/><br/> - 44100 : &lt;384 &lt;= bitrate = 1024<br/><br/> - 48000: 384 &lt; &lt;= bithastighet = 1024<br/><br/> - 88200: 896 &lt; &lt;= bitrate = 1024<br/><br/> - 96000: 896 &lt; &lt;= bitrate = 1024  
**HEAACV1 (PÅ ANDRA)** |1:<br/><br/> - 22050: bitrate = 8<br/><br/> - 24000: &lt;8 &lt;= bithastighet = 10<br/><br/> - 32000: 12 &lt; &lt;= bitrate = 64<br/><br/> - 44100: 20 &lt; &lt;= bithastighet = 64<br/><br/> - 48000: 20 &lt; &lt;= bithastighet = 64<br/><br/> - 88200: bitrate = 64<br/><br/> 2:<br/><br/> - 32000: 16 &lt; &lt;= bitrate = 128<br/><br/> - 44100: 16 &lt; &lt;= bithastighet = 128<br/><br/> - 48000: 16 &lt; &lt;= bithastighet = 128<br/><br/> - 88200 : &lt;96 &lt;= bitrate = 128<br/><br/> - 96000: 96 &lt; &lt;= bitrate = 128<br/><br/> 5/6:<br/><br/> - 32000 : &lt;64 &lt;= bitrate = 320<br/><br/> - 44100: 64 &lt; &lt;= bitrate = 320<br/><br/> - 48000: 64 &lt; &lt;= bitrate = 320<br/><br/> - 88200 : &lt;256 &lt;= bitrate = 320<br/><br/> - 96000: 256 &lt; &lt;= bitrate = 320<br/><br/> 8:<br/><br/> - 32000: 96 &lt; &lt;= bitrate = 448<br/><br/> - 44100: 96 &lt; &lt;= bitrate = 448<br/><br/> - 48000: 96 &lt; &lt;= bitrate = 448<br/><br/> - 88200: 384 &lt; &lt;= bitrate = 448<br/><br/> - 96000: 384 &lt; &lt;= bitrate = 448  
**HEAACV2 (PÅ ANDRA)** |2:<br/><br/> - 22050: &lt;8 &lt;= bitrate = 10<br/><br/> - 24000: &lt;8 &lt;= bithastighet = 10<br/><br/> - 32000: 12 &lt; &lt;= bitrate = 64<br/><br/> - 44100: 20 &lt; &lt;= bithastighet = 64<br/><br/> - 48000: 20 &lt; &lt;= bithastighet = 64<br/><br/> - 88200: 64 &lt; &lt;= bitrate = 64  
  
## <a name="clip"></a><a name="Clip"></a>Klipp
### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Starttime** |**xs:varaktighet** |Anger starttiden för en presentation. Värdet på StartTime måste matcha de absoluta tidsstämplarna för indatavideon. Om till exempel den första bildrutan i indatavideon har en tidsstämpel på 12:00:10.000, ska StartTime vara minst 12:00:10.000 eller mer. |
| **Varaktighet** |**xs:varaktighet** |Anger hur länge en presentation är (till exempel utseendet på ett överlägg i videon). |

## <a name="output"></a><a name="Output"></a>Produktionen
### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Filnamn** |**xs:sträng** |Namnet på utdatafilen.<br/><br/> Du kan använda makron som beskrivs i följande tabell för att skapa utdatafilnamnen. Ett exempel:<br/><br/> **"Utdata": [ { "Filnamn": "{Basnamn}*{Resolution}*{Bitrate}.mp4", "Format": { "Type": "MP4Format" } } ]** |

### <a name="macros"></a>Makron

| Makro | Beskrivning |
| --- | --- |
| **{Basnamn}** |Om du gör VoD-kodning är {Basename} de första 32 tecknen i egenskapen AssetFile.Name för den primära filen i indatatillgången.<br/><br/> Om indatatillgången är ett direkt arkiv härleds {Basename} från trackName-attributen i servermanifestet. Om du skickar in ett underklippsjobb med TopBitrate, som i:\>"<VideoStream TopBitrate</VideoStream\>" och utdatafilen innehåller video, är {Basename} de första 32 tecknen i trackName för videolagret med den högsta bithastigheten.<br/><br/> Om du i stället skickar in ett underklippsjobb med alla indatabithastigheter,\>till exempel "<\>VideoStream *</VideoStream" och utdatafilen innehåller video, är {Basename} de första 32 tecknen i trackName för motsvarande videolager. |
| **{Codec}** |Kartor till "H264" för video och "AAC" för ljud. |
| **{Bithastighet}** |Målvideobitratet om utdatafilen innehåller video och ljud, eller målljudbithastighet om utdatafilen endast innehåller ljud. Det värde som används är bithastigheten i kbps. |
| **{Kanal}** |Ljudkanal räknas om filen innehåller ljud. |
| **{Bredd}** |Videons bredd, i bildpunkter, i utdatafilen, om filen innehåller video. |
| **{Höjd}** |Videons höjd, i bildpunkter, i utdatafilen, om filen innehåller video. |
| **{Tillägg}** |Ärver från egenskapen "Typ" för utdatafilen. Utdatafilnamnet har en förlängning som är en av: "mp4", "ts", "jpg", "png" eller "bmp". |
| **{Index}** |Obligatoriskt för miniatyrbild. Bör bara vara närvarande en gång. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a>Video (komplex typ ärver från Codec)
### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Start** |**xs:sträng** | |
| **Steg** |**xs:sträng** | |
| **Intervall** |**xs:sträng** | |
| **BevaraUpplösningEfterRotation** |**xs:booleska** |För detaljerad förklaring, se följande avsnitt: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a>BevaraUpplösningEfterRotation
Det rekommenderas att använda flaggan **PreserveResolutionAfterRotation** i kombination med upplösningsvärden uttryckta i procent (Width="100%" , Height="100%").  

Som standard är kodupplösningsinställningarna (Bredd, Höjd) i MES-förinställningarna (Media Encoder Standard) inriktade på videor med 0-graders rotation. Om indatavideon till exempel är 1280x720 med nollgradig rotation, säkerställer standardförinställningarna att utdata har samma upplösning.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Om indatavideon har fångats med rotation som inte är noll (till exempel en smartphone eller surfplatta som hålls lodrätt) tillämpar MES som standard kodupplösningsinställningarna (bredd, höjd) på indatavideon och kompenserar sedan för rotationen. Se till exempel bilden som följer. Den förinställda använder Bredd = "100%", Höjd = "100%", som MES tolkar som kräver att utdata är 1280 pixlar bred och 720 pixlar hög. När du har roterat videon krymper den sedan bilden så att den passar in i fönstret, vilket leder till pelarlådeområden till vänster och höger.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternativt kan du använda **flaggan PreserveResolutionAfterRotation** och ställa in den på "true" (standard är "false"). Så om din förinställning har Bredd = "100%", Höjd = "100%" och PreserveResolutionAfterRotation inställd på "true", en indatavideo, som är 1280 pixlar bred och 720 pixlar hög med 90-graders rotation ger en utgång med nollgradig rotation, men 720 pixlar bred och 1280 pixlar höga. Se följande bild:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a>FormatGrupp (grupp)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **BmpFormat (BmpFormat)** |**BmpFormat (BmpFormat)** | |
| **PngFormat (På andra sätt)** |**PngFormat (På andra sätt)** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a>BmpLayer (på ett sätt)
### <a name="element"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs="0" |**xs:int** | |
| **Höjd**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Villkor** |**xs:sträng** | |

## <a name="pnglayer"></a><a name="PngLayer"></a>PngLayer (på andra)
### <a name="element"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs="0" |**xs:int** | |
| **Höjd**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Villkor** |**xs:sträng** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a>JpgLagra
### <a name="element"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs="0" |**xs:int** | |
| **Höjd**<br/><br/> minOccurs="0" |**xs:int** | |
| **Kvalitet**<br/><br/> minOccurs="0" |**xs:int** |Giltiga värden: 1(sämst)-100(bäst) |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Villkor** |**xs:sträng** | |

## <a name="pnglayers"></a><a name="PngLayers"></a>PngLayers (Skiktare)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayer (på andra)**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer (på andra)](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a>BmpLayers (BmpLayers)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **BmpLayer (på ett sätt)**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer (på ett sätt)](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a>JpgLayers (på andra sätt)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **JpgLagra**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLagra](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a>BmpImage (komplex typ ärver från Video)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayers (Skiktare)**<br/><br/> minOccurs="0" |[PngLayers (Skiktare)](media-services-mes-schema.md#PngLayers) |Png-lager |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a>JpgImage (komplex typ ärver från Video)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayers (Skiktare)**<br/><br/> minOccurs="0" |[PngLayers (Skiktare)](media-services-mes-schema.md#PngLayers) |Png-lager |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a>PngImage (komplex typ ärver från video)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayers (Skiktare)**<br/><br/> minOccurs="0" |[PngLayers (Skiktare)](media-services-mes-schema.md#PngLayers) |Png-lager |

## <a name="examples"></a>Exempel
Se exempel på XML-förinställningar som bygger på det här schemat, se [Aktivitetsförinställningar för MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

