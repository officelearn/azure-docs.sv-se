---
title: Begär gränser - Translator Text API
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas begärandens begränsningar för translatortext-API:et. Avgifter uppstår baserat på teckenantal, inte begäranfrekvens med en gräns på 5 000 tecken per begäran. Teckengränser är prenumerationsbaserade, med F0 begränsad till 2 miljoner tecken per timme.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: swmachan
ms.openlocfilehash: 8d26efec2783d6f121c319e46b1b505b6e1b1e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498933"
---
# <a name="request-limits-for-translator-text"></a>Begär gränser för översättare text

Den här artikeln innehåller begränsningsgränser för Translator Text API. Tjänsterna omfattar översättning, translitterering, meningslängdsidentifiering, språkidentifiering och alternativa översättningar.

## <a name="character-and-array-limits-per-request"></a>Tecken- och matrisgränser per begäran

Varje översättningsbegäran är begränsad till 5 000 tecken, på alla målspråk som du översätter till. Om du till exempel skickar en översättsbegäran på 1 500 tecken för att översätta till 3 olika språk resulterar det i en begäranstorlek på 1 500 x3 = 4 500 tecken, vilket uppfyller begärandens gräns. Du debiteras per tecken, inte av antalet begäranden. Vi rekommenderar att du skickar kortare begäranden.

I följande tabell visas matriselement och teckengränser för varje åtgärd i Translator Text API.

| Åtgärd | Maximal storlek för arrayelement |   Maximalt antal matriselement |  Maximal begärandestorlek (tecken) |
|:----|:----|:----|:----|
| Översätt | 5 000 | 100   | 5 000 |
| Transkribera | 5 000 | 10    | 5 000 |
| Upptäcka | 10 000 | 100 |   50 000 |
| BreakSentence | 10 000    | 100 | 50 000 |
| Slå upp i ordlista| 100 |  10  | 1,000 |
| Ordlisteexempel | 100 för text och 100 för översättning (200 totalt)| 10|   2 000 |

## <a name="character-limits-per-hour"></a>Teckengränser per timme

Din teckengräns per timme baseras på prenumerationsnivån Translator Text. 

Timkvoten bör förbrukas jämnt under hela timmen. På F0-nivågränsen på 2 miljoner tecken per timme bör tecken till exempel inte förbrukas snabbare än ungefär 33 300 tecken per minuts skjutfönster (2 miljoner tecken dividerat med 60 minuter).

Om du når eller överskrider dessa gränser, eller skickar för stor del av kvoten på kort tid, får du förmodligen ett kvotsvar. Det finns inga begränsningar för samtidiga begäranden.

| Nivå | Teckengräns |
|------|-----------------|
| F0 | 2 miljoner tecken per timme |
| S1 | 40 miljoner tecken per timme |
| S2 / C2 | 40 miljoner tecken per timme |
| S3 / C3 | 120 miljoner tecken per timme |
| S4 / C4 | 200 miljoner tecken per timme |

Begränsningar för [prenumerationer med flera tjänster](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) är desamma som S1-nivån.

Dessa gränser är begränsade till Microsofts standardöversättningsmodeller. Anpassade översättningsmodeller som använder Custom Translator är begränsade till 1 800 tecken per sekund.

## <a name="latency"></a>Svarstid

Translator Text API har en maximal svarstid på 15 sekunder med standardmodeller och 120 sekunder när du använder anpassade modeller. Vanligtvis returneras svar *för text inom 100 tecken* i 150 millisekunder till 300 millisekunder. De anpassade översättarmodellerna har liknande svarstidsegenskaper på ihållande begärandehastighet och kan ha en högre latens när din begäranhet är intermittent. Svarstiderna varierar beroende på storleken på begäran och språkparet. Om du inte får någon översättning eller ett [felmeddelande](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) inom den tidsramen kontrollerar du koden, nätverksanslutningen och försöker igen. 

## <a name="sentence-length-limits"></a>Gränser för meningslängd

När funktionen [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) används begränsas meningslängden till 275 tecken. Det finns undantag för dessa språk:

| Språk | Kod | Teckengräns |
|----------|------|-----------------|
| Kinesiska | Zh | 132 |
| Tyska | de | 290 |
| Italienska | it | 280 |
| Japanska | ja | 150 |
| Portugisiska | pt | 290 |
| Spanska | ES | 280 |
| Italienska | it | 280 |
| Thailändska | Th | 258 |

> [!NOTE]
> Den här gränsen gäller inte översättningar.

## <a name="next-steps"></a>Nästa steg

* [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regional tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referens för v3 Translator Text API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
