---
title: En experimentell förinställning för innehåll-medvetna encoding – Azure | Microsoft Docs
description: Den här artikeln beskriver innehåll-medvetna kodning i Azure Media Services
services: media-services
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: sethm
ms.custom: ''
ms.openlocfilehash: 3c50502a8b873503ee937914fac5f2d92cb23a2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733494"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Experimentella förinställning för innehåll-medvetna encoding

För att förbereda innehåll för leverans av [strömning med anpassad bithastighet](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), video måste kodas på flera bithastigheter (högt till lågt). För att säkerställa att rätt försämring av kvaliteten, som bithastigheten sjunker så är lösning av videon. Detta resulterar i en s.k. kodning ladder – en tabell med upplösningar och bithastigheter för utdata, som du ser en del av vår fast förinställningar för kodning, till exempel [H264MultipleBitrate1080p](../previous/media-services-mes-preset-h264-multiple-bitrate-1080p.md).

## <a name="overview"></a>Översikt

Intresserad av mer än en metod för en förinställning-anpassas-alla-videor ökat när Netflix publicerat sina [blogg](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) i December 2015. Sedan dess har redan flera lösningar för innehåll-medvetna encoding har publicerats i marketplace; Se [i den här artikeln](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) en översikt. Tanken är att känna till innehåll – anpassa eller finjustera kodning ladder på ett enskilda videon. Med varje upplösning som är finns det en bithastighet utanför vilka en ökning i kvalitet är inte perceptive – kodaren fungerar på det här värdet för optimal bithastighet. Nästa nivå i optimering är att välja den upplösning baserat på innehållet – till exempel en video med en PowerPoint-presentation åtnjuter inte kommer nedan 720p. Fortsätter, kan vilken kodare som ha uppgiften för att optimera inställningarna för varje skott i videon. Netflix beskrivs [ett sådant tillvägagångssätt](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) under 2018.

I början av 2017 släppte Microsoft den [Adaptiv direktuppspelning](autogen-bitrate-ladder.md) förinställda att åtgärda problemet med variationer i kvalitet och källvideorna upplösning. Våra kunder hade en varierande blandning av innehåll, vissa vid 1080p, andra vid 720p och några i SD och lägre upplösningar. Dessutom var inte alla källinnehållet högkvalitativa mezzanines från film eller TV studios. Den Adaptiv direktuppspelning förinställda adresser dessa problem genom att se till att bithastighetsstege aldrig överskrider lösningen eller genomsnittlig bithastigheten av inkommande mezzanine.

Experimentella innehåll-medvetna förinställningen för kodningen utökar den mekanismen genom att införliva anpassad logik som gör att kodaren söker optimala bithastighet värdet för en viss lösning, men utan omfattande databaserad analys. Resultatet är att den här nya förinställningen skapar utdata som har lägre bithastighet än förinställningen Adaptiv direktuppspelning, men med högre kvalitet. Se i följande exempel-diagram som visar en jämförelse med hjälp av kvalitet mätvärden som [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) och [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Källan har skapats genom att sammanfoga korta klipp av hög komplexiteten skärmbilder från filmer och TV-program, avsedd att vara påfrestande kodaren. Per definition, den här förinställda ger resultat som varierar från innehåll för – innebär det också att för vissa innehåll, det inte kanske betydande minskning av bithastighet eller förbättringar i kvalitet.

![Pris-förvrängningar (RD)-kurva med PSNR](media/cae-experimental/msrv1.png)

**Bild 1: Pris-förvrängningar (RD) kurva med PSNR mått för hög komplexiteten källa**

![Pris-förvrängningar (RD)-kurva med VMAF](media/cae-experimental/msrv2.png)

**Bild 2: Pris-förvrängningar (RD) kurva med VMAF mått för hög komplexiteten källa**

Förinställningen för närvarande är anpassad för hög komplexitet, hög kvalitet källvideorna (filmer, TV-program). Arbete pågår efter behov till lågt komplexiteten innehåll (till exempel PowerPoint-presentationer), samt sämre kvalitet videor. Denna inställning använder även samma uppsättning lösningar som Adaptiv direktuppspelning förinställning. Microsoft arbetar på metoder för att välja en mindre uppsättning lösningar baserat på innehållet. Följande är resultatet för en annan kategori av källinnehållet, där kodaren gick att fastställa att inmatning av dålig kvalitet (många komprimering artefakter på grund av låg bithastigheten). Observera att med den experimentella förinställd, kodaren bestämde sig att skapa en utdata-lager – på en låg bithastighet så att de flesta klienter kommer att kunna spela upp dataströmmen utan fördröjs.

![RD-kurva med PSNR](media/cae-experimental/msrv3.png)

**Bild 3: RD-kurva med PSNR för låg kvalitet inmatningen (vid 1080p)**

![RD-kurva med VMAF](media/cae-experimental/msrv4.png)

**Bild 4: RD-kurva med VMAF för låg kvalitet inmatningen (vid 1080p)**

## <a name="use-the-experimental-preset"></a>Använda experimentella förinställd

Du kan skapa transformeringar som använder denna förinställning på följande sätt. Om du använder en självstudiekurs [som detta](stream-files-tutorial-with-api.md), kan du uppdatera koden på följande sätt:

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
> Prefixet ”experimentella” används här för att signalera att de underliggande algoritmerna är fortfarande under utveckling. Det kan och kommer att ändringar över tid för att den logik som används för att generera bithastighet stegar, med målet att Konvergera på en algoritm som är robust och anpassas till en mängd olika indata villkor. Kodning jobb med hjälp av den här förinställda kommer ändå att debiteras baserat på utmatningsminuter och utdatatillgången kan levereras från våra slutpunkter för direktuppspelning i protokoll, till exempel DASH och HLS.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om det här nya alternativet för att optimera dina videor, erbjuder vi dig att testa den. Du kan skicka feedback via länkarna i slutet av den här artikeln eller engagera oss mer direkt på <amsved@microsoft.com>.
