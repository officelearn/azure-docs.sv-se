---
title: Talöversättning med taltjänst
titleSuffix: Azure Cognitive Services
description: Med taltjänsten kan du lägga till översättning av tal i sluten tid i realtid i realtid i dina program, verktyg och enheter. Samma API kan användas för både tal till tal- och tal till text-översättning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052623"
---
# <a name="what-is-speech-translation"></a>Vad är talöversättning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Talöversättning från taltjänsten möjliggör tal-till-tal-översättning i realtid och tal-till-text-översättning av ljudströmmar. Med Speech SDK har dina program, verktyg och enheter tillgång till källavskrifter och översättningsutgångar för medföljande ljud. Interimsresultat för transkription och översättning returneras när tal detekteras, och finalresultat kan omvandlas till syntetiserat tal.

Microsofts översättningsmotor drivs av två olika metoder: statistisk maskinöversättning (SMT) och neural maskinöversättning (NMT). SMT använder avancerad statistisk analys för att uppskatta bästa möjliga översättningar med tanke på några få ord. Med NMT används neurala nätverk för att ge mer exakta, naturliga översättningar genom att använda hela sammanhanget av meningar för att översätta ord.

Idag använder Microsoft NMT för översättning till de mest populära språken. Alla [språk som är tillgängliga för tal-till-tal-översättning](language-support.md#speech-translation) drivs av NMT. Tal-till-text-översättning kan använda SMT eller NMT beroende på språkparet. När målspråket stöds av NMT är den fullständiga översättningen NMT-driven. När målspråket inte stöds av NMT är översättningen en hybrid av NMT och SMT, med engelska som en "pivot" mellan de två språken.

## <a name="core-features"></a>Kärnfunktioner

Här är de funktioner som är tillgängliga via Tal SDK och REST API: er:

| Användningsfall | SDK | REST |
|----------|-----|------|
| Tal-till-text översättning med erkännande resultat. | Ja | Inga |
| Tal-till-tal översättning. | Ja | Inga |
| Resultat för interimserkännande och översättning. | Ja | Inga |

## <a name="get-started-with-speech-translation"></a>Komma igång med talöversättning

Vi erbjuder snabbstarter utformade för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller en lista över snabbstarter för talöversättning ordnade efter språk.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| [C#, .NET-kärna](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, .NET-ramverk](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Bläddra](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Bläddra](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exempelkod

Exempelkod för Tal-SDK finns på GitHub. Dessa exempel täcker vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel-shot erkännande / översättning, och arbeta med anpassade modeller.

* [Exempel på tal-till-text och översättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migreringsguider

Om dina program, verktyg eller produkter använder [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)har vi skapat guider som hjälper dig att migrera till taltjänsten.

* [Migrera från translator-tal-API:et till taltjänsten](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Tal-till-text](rest-speech-to-text.md)
* [REST API: Text-till-tal](rest-text-to-speech.md)
* [REST API: Batch transkription och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

* [Skaffa en prenumerationsnyckel för taltjänsten gratis](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
