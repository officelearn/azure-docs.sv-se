---
title: Koda anpassad transformering med Azure Media Services v3 | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure Media Services v3 för att koda en anpassad transformering.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/17/2018
ms.author: juliako
ms.openlocfilehash: d298070877a366d04b2df1ef8ac63b08f8771de0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655673"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Koda med en anpassad transformering

När encoding med Azure Media Services, du kan komma igång snabbt med något av de rekommenderade inbyggda förinställda baserade på branschstandardens rekommendationer som visas i den [strömning filer](stream-files-tutorial-with-api.md) kursen, eller så kan du skapa en anpassad förinställda att fokusera på dina specifika krav för scenario eller enhet. 

> [!Note]
> I Azure Media Services v3 är alla kodning bithastigheter i bitar per sekund. Detta skiljer sig REST-v2 Media Encoder Standard förinställningar. Till exempel bithastighet i v2 måste anges som 128, men det skulle vara 128000 i v3.

## <a name="download-the-sample"></a>Hämta exemplet

Klona en GitHub-databas som innehåller fullständig .NET Core exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Anpassade förinställda exempel finns i den [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) mapp.

## <a name="create-a-transform-with-a-custom-preset"></a>Skapa en transformering med en anpassad förinställning 

När du skapar en ny [transformera](https://docs.microsoft.com/rest/api/media/transforms), måste du ange vad du vill skapa som utdata. Den obligatoriska parametern är objektet **TransformOutput** som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Följande **TransformOutput** skapar anpassade inställningar för codecs och lager utdata.

När du skapar en [Transformering](https://docs.microsoft.com/rest/api/media/transforms) bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan.  I Media Services v3 **hämta** metoderna på entiteter returnerar **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Nästa steg

[Direktuppspelning av filer](stream-files-tutorial-with-api.md) 
