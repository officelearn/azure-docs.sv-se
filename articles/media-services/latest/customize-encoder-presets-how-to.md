---
title: Koda anpassade transformering med hjälp av Media Services v3 .NET – Azure | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure Media Services v3 att koda en anpassad transformering med hjälp av .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: 848da2996b71b137c6112225c9bef7e93b457c7d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837243"
---
# <a name="how-to-encode-with-a-custom-transform-by-using-net"></a>Koda med en anpassad transformering med hjälp av .NET

När kodning med Azure Media Services, du kan komma igång snabbt med en av de rekommenderade inbyggda förinställningar baserat på branschens bästa praxis som visas i den [Streaming filer](stream-files-tutorial-with-api.md) självstudien. Du kan också skapa en anpassad förinställning om du vill rikta in dina specifika krav för scenario eller enhet.

## <a name="considerations"></a>Överväganden

När du skapar anpassade förinställningar, gäller följande:

* Alla värden för höjd och bredd på AVC innehåll måste vara en multipel av 4.
* I Azure Media Services v3 är alla kodning bithastighet i bitar per sekund. Detta skiljer sig från förinställningar med våra v2 API: er, som används kilobit per sekund som enheten. Till exempel om bithastigheten i v2 har angetts som 128 (kbit/s) skulle i v3 det ställas in till 128000 (bitar per sekund).

## <a name="prerequisites"></a>Förutsättningar 

[Skapa ett Media Services-konto](create-account-cli-how-to.md). <br/>Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto. 

## <a name="download-the-sample"></a>Hämta exemplet

Klona en GitHub-lagringsplats som innehåller det fullständiga .NET Core-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Anpassad förinställning exemplet finns i den [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) mapp.

## <a name="create-a-transform-with-a-custom-preset"></a>Skapa en transformering med en anpassad förinställning 

När du skapar en ny [transformera](https://docs.microsoft.com/rest/api/media/transforms), måste du ange vad du vill ska kunna skapas som utdata. Den obligatoriska parametern är objektet [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Följande **TransformOutput** skapar anpassade codec och layer utdatainställningar.

När du skapar en [Transformering](https://docs.microsoft.com/rest/api/media/transforms) bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan. I Media Services v3 **hämta** metoder på entiteter returnera **null** om entiteten inte finns (en skiftlägeskänslig kontroll av namnet).

### <a name="example"></a>Exempel

I följande exempel definierar en uppsättning utdata som vi vill genereras när den här transformeringen används. Vi först lägga till ett AacAudio lager för ljud encoding och två H264Video lager för den videokodning. I video lager tilldela vi etiketter, så att de kan användas i utdata-filnamn. Nu ska vill vi utdata till att även omfatta miniatyrer. I exemplet nedan anger vi bilder i PNG-format, genereras på 50% av lösningen på indatavideon och på tre tidsstämplar - {25%, 50%, 75} med längden på indatavideon. Slutligen kan vi ange formatet för utdatafilerna – en för video och ljud, och en annan för miniatyrbilderna. Eftersom vi har flera H264Layers, måste vi använda makron som producerar unika namn per lager. Vi kan använda en `{Label}` eller `{Bitrate}` makro exemplet visar tidigare.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Nästa steg

[Strömmande filer](stream-files-tutorial-with-api.md) 
