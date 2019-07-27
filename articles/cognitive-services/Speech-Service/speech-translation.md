---
title: Tal översättning med Speech service
titleSuffix: Azure Cognitive Services
description: Med tal tjänsten kan du lägga till en fullständig översättning av tal från slut punkt till slut punkt i real tid till dina program, verktyg och enheter. Samma API kan användas för både tal-till-tal- och tal till text.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cfcefd0b18831163324519b61dbea305f90f44bc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552647"
---
# <a name="what-is-speech-translation"></a>Vad är tal Översättning?

Tal översättning från Azure Speech Services, aktiverar real tids-, tal-till-tal-och tal-till-text-översättning i real tid av ljud strömmar. Med tal-SDK har dina program, verktyg och enheter åtkomst till käll avskrifter och översättnings utdata för tillhandahållen ljud. Interimistiska avskrifter och översättnings resultat returneras som tal identifieras och slutliga resultat kan konverteras till syntetiskt tal.

Microsofts översättnings motor drivs av två olika metoder: statistisk maskin översättning (SMT) och neurala Machine Translation (NMT). SMT använder avancerad statistisk analys för att uppskatta de bästa möjliga översättningarna med några få ord. Med NMT används neurala-nätverk för att tillhandahålla mer exakta, naturligt förekommande översättningar genom att använda den fullständiga kontexten för meningar för att översätta ord.

Idag använder Microsoft NMT för översättning till de flesta populära språken. Alla [språk som är tillgängliga för tal till talöversättning](language-support.md#speech-translation) drivs av NMT. Tal till text translation kan använda SMT eller NMT beroende på språkparet. När mål språket stöds av NMT, är den fullständiga översättningen NMT. När mål språket inte stöds av NMT, är översättningen en hybrid av NMT och SMT, med engelska som en "Pivot"-lösning mellan de två språken.

## <a name="core-features"></a>Kärn funktioner

Här är de funktioner som är tillgängliga via API: er för tal-SDK och REST:

| Användningsfall | SDK | REST |
|----------|-----|------|
| Översättning av tal till text med igenkännings resultat. | Ja | Nej |
| Översättning av tal till tal. | Ja | Nej |
| Resultat av löpande igenkänning och översättning. | Ja | Nej |

## <a name="get-started-with-speech-translation"></a>Kom igång med tal Översättning

Vi erbjuder snabb starter som har utformats för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller en lista över tal översättnings snabb starter ordnade efter språk.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Mellan](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Mellan](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Mellan](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Mellan](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows, Linux, macOS | [Mellan](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exempelkod

Exempel koden för talet SDK finns på GitHub. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning/översättning och arbeta med anpassade modeller.

* [Exempel på tal-till-text-och översättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Stöd linjer för migrering

Om dina program, verktyg eller produkter använder [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)har vi skapat guider som hjälper dig att migrera till tal tjänsterna.

* [Migrera från Translator Speech API till tal tjänsterna](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Tal enheter SDK](speech-devices-sdk.md)
* [REST API: Tal till text](rest-speech-to-text.md)
* [REST API: Text till tal](rest-text-to-speech.md)
* [REST API: Batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
