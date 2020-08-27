---
title: Tal översättning med Speech service
titleSuffix: Azure Cognitive Services
description: Med tal tjänsten kan du lägga till en fullständig översättning av tal från slut punkt till slut punkt i real tid till dina program, verktyg och enheter. Samma API kan användas för både tal till tal- och tal till text-översättning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 670d7f160285283bd44371b893c63904b2685926
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934505"
---
# <a name="what-is-speech-translation"></a>Vad är talöversättning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Tal översättning från Speech service möjliggör real tids-, tal-till-tal-och tal-till-text-översättning i real tid av ljud strömmar. Med tal-SDK har dina program, verktyg och enheter åtkomst till käll avskrifter och översättnings utdata för tillhandahållen ljud. Interimistiska avskrifter och översättnings resultat returneras som tal identifieras och slutliga resultat kan konverteras till syntetiskt tal.

Microsofts översättnings motor drivs av två olika metoder: statistisk maskin översättning (SMT) och neurala Machine Translation (NMT). SMT använder avancerad statistisk analys för att uppskatta de bästa möjliga översättningarna med några få ord. Med NMT används neurala-nätverk för att tillhandahålla mer exakta, naturligt förekommande översättningar genom att använda den fullständiga kontexten för meningar för att översätta ord.

Idag använder Microsoft NMT för översättning till de flesta populära språken. Alla [språk som är tillgängliga för översättning av tal till tal](language-support.md#speech-translation) drivs av NMT. Översättning av tal till text kan använda SMT-eller NMT beroende på språk paret. När mål språket stöds av NMT, är den fullständiga översättningen NMT. När mål språket inte stöds av NMT, är översättningen en hybrid av NMT och SMT, med engelska som en "Pivot"-lösning mellan de två språken.

## <a name="core-features"></a>Kärn funktioner

Här är de funktioner som är tillgängliga via API: er för tal-SDK och REST:

| Användningsfall | SDK | REST |
|----------|-----|------|
| Översättning av tal till text med igenkännings resultat. | Ja | Inga |
| Översättning av tal till tal. | Ja | Inga |
| Resultat av löpande igenkänning och översättning. | Ja | Inga |

## <a name="get-started-with-speech-translation"></a>Kom igång med tal Översättning

Vi erbjuder snabb starter som har utformats för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller en lista över tal översättnings snabb starter ordnade efter språk.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Bläddra](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Bläddra](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exempelkod

Exempel koden för talet SDK finns på GitHub. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning/översättning och arbeta med anpassade modeller.

* [Exempel på tal-till-text-och översättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migreringsguider

Om dina program, verktyg eller produkter använder [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)har vi skapat guider som hjälper dig att migrera till tal tjänsten.

* [Migrera från Translator Speech API till tal tjänsten](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referens dokument

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: tal till text](rest-speech-to-text.md)
* [REST API: text till tal](rest-text-to-speech.md)
* [REST API: batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
