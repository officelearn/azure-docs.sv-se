---
title: En experimentell för inställning för innehålls medveten kodning – Azure | Microsoft Docs
description: Den här artikeln beskriver innehålls medveten kodning i Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9389466b6291542563c068706479bf981c5880da
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896140"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Experimentell för inställning för innehålls medveten kodning

För att förbereda innehåll för leverans av [direkt uppspelning med anpassningsbar bit hastighet](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)måste video kodas med flera bit hastigheter (hög till låg). För att säkerställa en korrekt försämring av kvalitet, eftersom bit hastigheten sänks så är videons upplösning. Detta resulterar i en så kallad kodnings steg – en tabell över lösningar och bit hastigheter. Se Media Services [inbyggda kodnings för inställningar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

## <a name="overview"></a>Översikt

Intresse av att flytta bortom en metod med en för inställning som passar alla videor ökar när Netflix publicerade sin [blogg](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) i december 2015. Sedan dess har flera lösningar för innehålls medveten kodning lanserats på Marketplace. i [den här artikeln](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) finns en översikt. Idén är att vara medveten om innehållet – för att anpassa eller justera kodnings steg för att få en viss Videos komplexitet. Vid varje lösning är det en bit hastighet utöver vilken eventuell ökning av kvalitet inte är Perceptive – kodaren fungerar med detta värde för optimal bit hastighet. Nästa optimerings nivå är att välja lösningarna baserat på innehållet, till exempel om en video i en PowerPoint-presentation inte drar nytta av under 720p. Om du går vidare kan kodaren utföra åtgärder för att optimera inställningarna för varje bild i videon. Netflix beskrivs [i](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) 2018.

Tidigt i 2017 lanserade [Microsoft den förvalda för hands](autogen-bitrate-ladder.md) versionen för att lösa problemet med variabiliteten i käll videoklippets kvalitet och upplösning. Våra kunder hade en varierande blandning av innehåll, vissa på 1080p, andra vid 720p och några få vid SD och lägre upplösning. Dessutom var inte allt käll innehåll mezzanines av hög kvalitet från film-eller TV-Studios. För hands inställningen för anpassad direkt uppspelning åtgärdar problemen genom att se till att bit hastighets stegen aldrig överskrider upplösningen eller den genomsnittliga bit hastigheten för indata-mezzaninfil.

Den experimentella kodnings för inställningen utökar den mekanismen, genom att inkludera anpassad logik som gör det möjligt för kodaren att söka efter optimalt bit hastighets värde för en specifik lösning, men utan att kräva omfattande beräknings analys. Netto resultatet är att den här nya för hands inställningen genererar utdata som har lägre bit hastighet än för hands inställningen för adaptiv strömning, men med en högre kvalitet. Se följande exempel diagram som visar jämförelsen med kvalitets mått som [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) och [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Källan skapades genom sammanfogning av korta klipp med höga komplexitets dum par från filmer och TV-program som är avsedda att stressa kodaren. Enligt definition ger den här inställningen resultat som varierar från innehåll till innehåll – det innebär också att för visst innehåll, kan det hända att det inte finns någon betydande minskning av bit hastigheten eller förbättring av kvaliteten.

![Hastighets förvrängnings kurva (RD) med PSNR](media/cae-experimental/msrv1.png)

**Bild 1: hastighets förvrängning (RD) kurva med PSNR mått för hög komplexitets källa**

![Hastighets förvrängnings kurva (RD) med VMAF](media/cae-experimental/msrv2.png)

**Bild 2: hastighets förvrängning (RD) kurva med VMAF mått för hög komplexitets källa**

För hands inställningen är för närvarande justerad för hög komplexitet, bild video med hög kvalitet (filmer, TV-program). Arbetet pågår för att anpassa till låg komplexitets innehåll (t. ex. PowerPoint-presentationer) och även dåliga kvalitets videor. Den här inställningen använder också samma uppsättning lösningar som för hands inställningen för anpassad direkt uppspelning. Microsoft arbetar med metoder för att välja en minimal uppsättning lösningar baserat på innehållet. Som följer är resultatet för en annan kategori av käll innehåll, där kodaren kunde fastställa att indatamängden hade låg kvalitet (många komprimerings artefakter på grund av den låga bit hastigheten). Observera att med den experimentella för inställningen, beslutade kodaren att endast skapa ett utmatnings lager – till en låg bit hastighet, så att de flesta klienter kan spela upp strömmen utan att stoppas.

![RD-kurva med PSNR](media/cae-experimental/msrv3.png)

**Bild 3: RD-kurva som använder PSNR för låg kvalitets kvalitet (vid 1080p)**

![RD-kurva med VMAF](media/cae-experimental/msrv4.png)

**Bild 4: RD-kurva som använder VMAF för låg kvalitets kvalitet (på 1080p)**

## <a name="use-the-experimental-preset"></a>Använd experimentell för inställning

Du kan skapa transformeringar som använder den här för inställningen enligt följande. Om du använder en självstudie som [detta](stream-files-tutorial-with-api.md)kan du uppdatera koden på följande sätt:

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> Prefixet "experimentell" används här för att signalera att de underliggande algoritmerna fortfarande utvecklas. Det kan och kommer att förändras över tid till den logik som används för att skapa bit hastighets steg, med målet att konvergera på en algoritm som är robust och anpassas till en mängd olika ingångs villkor. Kodning av jobb med hjälp av den här för inställningen faktureras fortfarande utifrån utmatnings minuter och utmatnings till gången kan levereras från våra slut punkter för direkt uppspelning i protokoll som bindestreck och HLS.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om detta nya alternativ för att optimera dina videor, bjuder vi in dig för att prova. Du kan skicka feedback till oss med hjälp av länkarna i slutet av den här artikeln eller engagera oss direkt på <amsved@microsoft.com>.
