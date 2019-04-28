---
title: Talöversättning med Azure Speech Services
titlesuffix: Azure Cognitive Services
description: Speech Services kan du lägga till slutpunkt till slutpunkt, i realtid, flera språk översättning av tal i dina program, verktyg och enheter. Samma API kan användas för både tal-till-tal- och tal till text.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 95682612b4b0fdb1baa5038039630e74abddb1a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460328"
---
# <a name="what-is-speech-translation"></a>Vad är talöversättning?

Talöversättning från Azure Speech Services gör det möjligt för i realtid, flera språk tal-till-tal och tal till text översättning av ljudströmmar. Med SDK för tal har program, verktyg och -enheter du åtkomst till källan avskrifter och translation utdata för angivna ljud. Tillfälliga transkription och translation resultaten returneras som tal identifieras, och finaler resultat kan konverteras till syntetiskt tal.

Microsofts translation motorn drivs av två olika sätt: statistiska maskinöversättning (SMT) och neural maskinöversättning (NMT). SMT använder avancerade statistiska analyser för att beräkna bästa möjliga översättningar som den aktuella kontexten några ord. Med NMT används neurala nätverk för att ange mer exakta, naturligt standardrösttyper översättningar genom att använda fullständig kontexten för meningar för att översätta ord.

Microsoft använder idag, NMT för översättning till mest populära språk. Alla [språk som är tillgängliga för tal till talöversättning](language-support.md#speech-translation) drivs av NMT. Tal till text translation kan använda SMT eller NMT beroende på språkparet. När målspråket som stöds av NMT, är hela översättningen NMT-baserade. När målspråket inte stöds av NMT, är översättningen en kombination av NMT och SMT, använda engelska som en ”pivot” mellan de två språk.

## <a name="core-features"></a>Kärnfunktioner

Här är funktionerna som är tillgängliga via tal SDK och REST API: er:

| Användningsfall | SDK | REST |
|----------|-----|------|
| Tal till text översättning med resultat. | Ja | Nej |
| Tal-till-talöversättning. | Ja | Nej |
| Tillfälliga erkännande och translation resultat. | Ja | Nej |

## <a name="get-started-with-speech-translation"></a>Kom igång med talöversättning

Vi erbjuder snabbstarter som utformats för att du köra kod i mindre än 10 minuter. Den här tabellen innehåller en lista med speech translation snabbstarter ordnade efter språk.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Bläddra](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows | [Bläddra](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exempelkod

Exempelkoden för tal SDK finns på GitHub. De här exemplen omfattar vanliga scenarier som läsa ljud från en fil eller dataström, kontinuerliga och inleveransen erkännande/översättning och arbeta med anpassade modeller.

* [Tal till text och translation exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migreringsguider

> [!WARNING]
> Translator för Talöversättning tas ur drift den 15 oktober 2019.

Om ditt program, verktyg eller produkter använder Talöversättning, har vi skapat guider som hjälper dig att migrera till Speech Services.

* [Migrera från API för Talöversättning till Speech Services](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Tal enheter SDK](speech-devices-sdk.md)
* [REST-API: Speech-to-text](rest-speech-to-text.md)
* [REST-API: Text till tal](rest-text-to-speech.md)
* [REST-API: Batch transkription och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

* [Skaffa en prenumerationsnyckel för Speech Services utan kostnad](get-started.md)
* [Hämta tal SDK](speech-sdk.md)
