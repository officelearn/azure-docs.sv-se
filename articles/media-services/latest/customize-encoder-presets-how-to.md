---
title: Koda anpassad transformering med Media Services v3 .NET - Azure | Microsoft-dokument
description: Det här avsnittet visar hur du använder Azure Media Services v3 för att koda en anpassad transformering med .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068037"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Så här kodar du med en anpassad transformering - .NET

När du kodar med Azure Media Services kan du komma igång snabbt med en av de rekommenderade inbyggda förinställningarna baserat på branschens bästa praxis som visas i självstudiekursen [för strömmande filer.](stream-files-tutorial-with-api.md) Du kan också skapa en anpassad förinställning för att rikta in dig på ditt specifika scenario eller enhetskrav.

## <a name="considerations"></a>Överväganden

När du skapar anpassade förinställningar gäller följande överväganden:

* Alla värden för höjd och bredd på AVC-innehåll måste vara en multipel av 4.
* I Azure Media Services v3 är alla kodningbithastigheter i bitar per sekund. Detta skiljer sig från förinställningar med våra v2 API: er, som använde kilobit / sekund som enhet. Om bithastigheten i v2 till exempel angavs som 128 (kilobit/sekund) i v3 skulle den vara inställd på 128000 (bitar/sekund).

## <a name="prerequisites"></a>Krav 

[Skapa ett Media Services-konto](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Hämta exemplet

Klona en GitHub-databas som innehåller hela .NET Core-exemplet till datorn med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Det anpassade förinställda exemplet finns i mappen [EncodeCustomTransform.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/)

## <a name="create-a-transform-with-a-custom-preset"></a>Skapa en transformering med en anpassad förinställning 

När du skapar en ny [transformering](https://docs.microsoft.com/rest/api/media/transforms)måste du ange vad du vill att den ska skapa som en utdata. Den obligatoriska parametern är objektet [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. **Förinställningen** beskriver steg-för-steg-instruktioner för video- och/eller ljudbearbetningsåtgärder som ska användas för att generera önskat **TransformOutput**. Följande **TransformOutput** skapar anpassade codec- och lagerutdatainställningar.

När du skapar en [Transformering](https://docs.microsoft.com/rest/api/media/transforms) bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan. I Media Services v3 **returnerar Hämta** metoder för entiteter **null** om entiteten inte finns (en skiftlägesokänslig kontroll av namnet).

### <a name="example"></a>Exempel

I följande exempel definieras en uppsättning utdata som vi vill ska genereras när den här transformeringen används. Vi lägger först till ett AacAudio-lager för ljudkodning och två H264Video-lager för videokodningen. I videolagren tilldelar vi etiketter så att de kan användas i utdatafilnamnen. Därefter vill vi att utdata även ska innehålla miniatyrer. I exemplet nedan anger vi bilder i PNG-format, genererade med 50 % av upplösningen på indatavideon och vid tre tidsstämplar - {25 %, 50 %, 75} av indatavideons längd. Slutligen anger vi formatet för utdatafilerna - en för video + ljud och en annan för miniatyrerna. Eftersom vi har flera H264Layers, måste vi använda makron som producerar unika namn per lager. Vi kan antingen `{Label}` `{Bitrate}` använda ett eller makro, visar exemplet den förra.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Nästa steg

[Direktuppspelningsfiler](stream-files-tutorial-with-api.md) 
