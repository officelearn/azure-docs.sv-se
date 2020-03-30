---
title: En förinställning för innehållsmedveten kodning – Azure Media Services
description: I den här artikeln beskrivs innehållsmedveten kodning i Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772108"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Använd den innehållsmedvetna kodningsförinställningen för att hitta det optimala bithastighetsvärdet för en viss upplösning

För att förbereda innehåll för leverans genom [adaptiv bitrate streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)måste video kodas med flera bithastigheter (hög till låg). Detta säkerställer graciös försämring av kvaliteten, eftersom bitrate sänks så är upplösningen på videon. En sådan kodning med flera bithastigheter använder sig av en så kallad kodningsstege – en tabell med upplösningar och bithastigheter, se mediatjänsternas [inbyggda kodningsförinställningar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Du bör vara medveten om innehållet du bearbetar, och anpassa / ställa in kodning stege till komplexiteten i den enskilda videon. Vid varje upplösning finns det en bithastighet bortom vilken en ökning av kvaliteten inte är klarsynt - kodaren arbetar med detta optimala bithastighetsvärde. Nästa nivå av optimering är att välja upplösningar baserat på innehållet - till exempel en video av en PowerPoint-presentation inte dra nytta av att gå under 720p. Går vidare, kan kodaren få i uppdrag att optimera inställningarna för varje skott i videon. 

Microsofts [Adaptive Streaming](autogen-bitrate-ladder.md) förinställning delvis tar upp problemet med variationen i kvaliteten och upplösningen av källan videor. Våra kunder har en varierande blandning av innehåll, några på 1080p, andra på 720p, och några på SD och lägre upplösningar. Dessutom är inte allt källinnehåll högkvalitativa mezzaniner från film- eller TV-studior. Den anpassade direktuppspelningsförinställningen åtgärdar dessa problem genom att se till att bithastighetsstegen aldrig överskrider upplösningen eller den genomsnittliga bithastigheten för indata mezzaninen. Den här förinställningen undersöker dock inte andra källegenskaper än upplösning och bithastighet.

## <a name="the-content-aware-encoding"></a>Den innehållsmedvetna kodningen 

Den innehållsmedvetna kodningsförinställningen utökar mekanismen för "adaptiv bithastighetsströmning" genom att införliva anpassad logik som gör att kodaren kan söka det optimala bithastighetsvärdet för en viss upplösning, men utan att kräva omfattande beräkningsanalys. Den här förinställningen ger en uppsättning GOP-justerade MP4:er. Med tanke på allt indatainnehåll utför tjänsten en första lättviktsanalys av indatainnehållet och använder resultaten för att bestämma det optimala antalet lager, lämpliga bithastighets- och upplösningsinställningar för leverans genom adaptiv direktuppspelning. Denna förinställning är särskilt effektiv för låg och medelhög komplexitet videor, där utdatafilerna kommer att vara på lägre bithastigheter än Adaptive Streaming förinställda men med en kvalitet som fortfarande ger en bra upplevelse för tittarna. Utdata kommer att innehålla MP4-filer med video och ljud interfolierade

Följande exempeldiagram visar jämförelsen med kvalitetsmått som [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) och [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Källan skapades genom att sammanfoga korta klipp av hög komplexitet skott från filmer och TV-program, avsedda att betona kodaren. Per definition ger den här förinställningen resultat som varierar från innehåll till innehåll – det innebär också att det för visst innehåll kanske inte minskar bithastigheten eller kvalitetsförbättringen.

![Pris-distorsion (RD) kurva med PSNR](media/content-aware-encoding/msrv1.png)

**Bild 1: Hastighetsdistorsion (RD) med PSNR-mått för hög komplexitetskälla**

![Pris-distorsion (RD) kurva med VMAF](media/content-aware-encoding/msrv2.png)

**Bild 2: Hastighetsdistorsion (RD) med VMAF-mått för hög komplexitetskälla**

Nedan visas resultaten för en annan kategori av källinnehåll, där kodaren kunde fastställa att indata var av dålig kvalitet (många komprimeringsartefakter på grund av den låga bithastigheten). Observera att med innehållsmedvetna förinställning, beslutade kodaren att producera bara ett utdatalager - på en tillräckligt låg bithastighet så att de flesta klienter skulle kunna spela strömmen utan att stanna.

![Rd-kurva med PSNR](media/content-aware-encoding/msrv3.png)

**Bild 3: RD-kurva med PSNR för låg kvalitet ingång (vid 1080p)**

![Rd-kurva med VMAF](media/content-aware-encoding/msrv4.png)

**Bild 4: RD-kurva med VMAF för låg kvalitet ingång (vid 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Så här använder du den innehållsmedvetna kodningsförinställningen 

Du kan skapa transformeringar som använder den här förinställningen på följande sätt. 

> [!TIP]
> Se avsnittet [Nästa steg](#next-steps) för självstudier som använder tranform-utdata. Utdatatillgången kan levereras från Media Services-slutpunkter för direktuppspelning i protokoll som MPEG-DASH och HLS (som visas i självstudierna).


```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Kodning av jobb `ContentAwareEncoding` med förinställningen faktureras baserat på utdataminuterna. 

## <a name="next-steps"></a>Nästa steg

* [Självstudiekurs: Ladda upp, koda och strömma videor med Media Services v3](stream-files-tutorial-with-api.md)
* [Självstudiekurs: Koda en fjärrfil baserat på URL och strömma videon - REST](stream-files-tutorial-with-rest.md)
* [Självstudiekurs: Koda en fjärrfil baserat på URL och strömma videon - CLI](stream-files-cli-quickstart.md)
* [Självstudiekurs: Koda en fjärrfil baserat på URL och strömma videon - .NET](stream-files-dotnet-quickstart.md)
* [Självstudiekurs: Koda en fjärrfil baserat på URL och strömma videon - Node.js](stream-files-nodejs-quickstart.md)
