---
title: Koda anpassad transformering med Media Services v3 .NET – Azure | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure Media Services v3 för att koda en anpassad transformering med hjälp av .NET.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80068037"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Koda med en anpassad transformering – .NET

När du kodar med Azure Media Services kan du snabbt komma igång med en av de rekommenderade inbyggda för inställningarna baserat på bransch bästa praxis som visas i själv studie kursen för [strömmande filer](stream-files-tutorial-with-api.md) . Du kan också bygga en anpassad för inställning för att rikta in dig på specifika scenario-eller enhets krav.

## <a name="considerations"></a>Att tänka på

När du skapar anpassade för inställningar gäller följande aspekter:

* Alla värden för höjd och bredd på AVC-innehåll måste vara en multipel av 4.
* I Azure Media Services v3 är alla kodnings bit hastigheter i bitar per sekund. Detta skiljer sig från för inställningarna med våra v2-API: er, som använde kilobit/sekund som enhet. Om bit hastigheten i v2 exempelvis angavs som 128 (kilobit/sekund), skulle den vara inställd på 128000 (bitar/sekund) i v3.

## <a name="prerequisites"></a>Förutsättningar 

[Skapa ett Media Services-konto](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Ladda ned exemplet

Klona en GitHub-lagringsplats som innehåller det fullständiga .NET Core-exemplet på din dator med hjälp av följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Det anpassade för inställnings exemplet finns i [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) -mappen.

## <a name="create-a-transform-with-a-custom-preset"></a>Skapa en transformering med en anpassad för inställning 

När du skapar en ny [transformering](https://docs.microsoft.com/rest/api/media/transforms)måste du ange vad du vill att den ska producera som utdata. Den obligatoriska parametern är objektet [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. För **inställningen** beskrivs de stegvisa instruktionerna för video-och/eller ljud bearbetnings åtgärder som ska användas för att generera önskad **TransformOutput**. Följande **TransformOutput** skapar anpassade inställningar för codec-och lager-utdata.

När du skapar en [Transformering](https://docs.microsoft.com/rest/api/media/transforms) bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan. I Media Services v3 kan du **Hämta** metoder i entiteter returnerar **Null** om entiteten inte finns (en Skift läges okänslig kontroll av namnet).

### <a name="example"></a>Exempel

I följande exempel definieras en uppsättning utdata som vi vill ska genereras när denna transformering används. Först lägger vi till ett AacAudio-lager för ljud kodningen och två H264Video-lager för video kodningen. I video lager tilldelar vi etiketter så att de kan användas i utdatafilernas namn. Nu vill vi att utdata även ska innehålla miniatyrer. I exemplet nedan anger vi bilder i PNG-format, genererade med 50% av upplösningen för Indataporten och vid tre tidsstämplar – {25%, 50%, 75} av den angivna videons längd. Slutligen anger vi formatet för utdatafilerna – ett för video + ljud och en annan för miniatyr bilderna. Eftersom vi har flera H264Layers måste vi använda makron som producerar unika namn per lager. Vi kan antingen använda ett `{Label}` eller `{Bitrate}` -makro, exemplet visar det tidigare.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Nästa steg

[Direktuppspelningsfiler](stream-files-tutorial-with-api.md) 
