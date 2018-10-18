---
title: Koda anpassade transformering med Azure Media Services v3 | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure Media Services v3 för att koda en anpassad transformering.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: c3318020b54beb121ac48d993a8ce794ac8e377f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376643"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Koda med en anpassad transformering

När kodning med Azure Media Services, du kan komma igång snabbt med en av de rekommenderade inbyggda förinställningar baserat på branschens bästa praxis som visas i den [Streaming filer](stream-files-tutorial-with-api.md) självstudiekursen och du kan välja att skapa en anpassad förinställningen för att fokusera på dina specifika krav för scenario eller enhet. 

> [!Note]
> I Azure Media Services v3 är alla kodning bithastigheter i bitar per sekund. Detta skiljer sig från REST-v2 förinställningar för Media Encoder Standard. Till exempel bithastigheten i v2 skulle anges som 128, men det skulle vara 128000 i v3.

## <a name="download-the-sample"></a>Hämta exemplet

Klona en GitHub-lagringsplats som innehåller det fullständiga .NET Core-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Anpassad förinställning exemplet finns i den [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) mapp.

## <a name="create-a-transform-with-a-custom-preset"></a>Skapa en transformering med en anpassad förinställning 

När du skapar en ny [transformera](https://docs.microsoft.com/rest/api/media/transforms), måste du ange vad du vill ska kunna skapas som utdata. Den obligatoriska parametern är objektet **TransformOutput** som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Följande **TransformOutput** skapar anpassade codec och layer utdatainställningar.

När du skapar en [Transformering](https://docs.microsoft.com/rest/api/media/transforms) bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan.  I Media Services v3 **hämta** metoder på entiteter returnera **null** om entiteten inte finns (en skiftlägeskänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Nästa steg

[Strömmande filer](stream-files-tutorial-with-api.md) 
