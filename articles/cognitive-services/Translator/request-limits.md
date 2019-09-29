---
title: Begär ande gränser – Translator Text API
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas begränsningar för begäran för Translator Text API. Avgifter debiteras baserat på antal tecken, inte begär ande frekvens med en gräns på 5 000 tecken per begäran. Tecken begränsningar är prenumerationer baserat, med F0 begränsad till 2 000 000 tecken per timme.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 79cf87cef4e58cf4e5a2039f30289d55038c8b92
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671953"
---
# <a name="request-limits-for-translator-text"></a>Begär ande gränser för Translator Text

Den här artikeln innehåller begränsnings gränser för Translator Text API. Tjänsterna omfattar översättning, transkriberingsspråk, identifiering av menings längd, språk identifiering och alternativa översättningar.

## <a name="character-and-array-limits-per-request"></a>Begränsningar för tecknen och matrisen per begäran

Varje översättnings förfrågan är begränsad till 5 000 tecken. Du debiteras per steg, inte av antalet begär Anden. Vi rekommenderar att du skickar kortare begär Anden.

I följande tabell visas mat ris element och Character-gränser för varje åtgärd i Translator Text API.

| Åtgärd | Maximal storlek för mat ris element |   Maximalt antal mat ris element |  Maximal begär ande storlek (tecken) |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10 000 | 100 |   50,000 |
| BreakSentence | 10 000    | 100 | 50,000 |
| Slå upp i ordlista| 100 |  10  | 1,000 |
| Ordlisteexempel | 100 för text och 100 för översättning (totalt 200)| 10|   2,000 |

## <a name="character-limits-per-hour"></a>Character-gränser per timme

Din tecken gräns per timme baseras på din Translator Text prenumerations nivå. 

Tim kvoten bör förbrukas jämnt under hela timmen. Till exempel, på F0-nivå gränsen på 2 000 000 tecken per timme, ska tecken förbrukas inte snabbare än med ungefär 33 300 tecken per minut i glidande fönster (2 000 000-tecken dividerat med 60 minuter).

Om du når eller överskrider de här gränserna eller skickar för stor del av kvoten under en kort tids period får du troligt vis ett kvot svar. Det finns inga begränsningar för samtidiga begär Anden.

| Nivå | Tecken gräns |
|------|-----------------|
| F0 | 2 000 000 tecken per timme |
| S1 | 40 000 000 tecken per timme |
| S2/C2 | 40 000 000 tecken per timme |
| S3/C3 | 120 000 000 tecken per timme |
| S4/C4 | 200 000 000 tecken per timme |

Begränsningar för [prenumerationer med flera tjänster](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) är samma som i S1-nivån.

Dessa gränser är begränsade till Microsofts standard översättnings modeller. Anpassade översättnings modeller som använder anpassad översättare är begränsade till 1 800 tecken per sekund.

## <a name="latency"></a>Svarstid

Translator Text API har en maximal fördröjning på 15 sekunder med standard modeller. Översättning med anpassade modeller har en maximal fördröjning på 25 sekunder. Vid den här tidpunkten har du fått ett resultat eller ett timeout-svar. Normalt returneras svar i 150 millisekunder till 300 millisekunder. Svars tiderna varierar beroende på storleken på begäran och språk paret. Om du inte får någon översättning eller ett [fel svar](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) inom den tids ramen bör du kontrol lera nätverks anslutningen och försöka igen.

## <a name="sentence-length-limits"></a>Längd begränsningar för mening

När du använder funktionen [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) är menings längden begränsad till 275 tecken. Det finns undantag för följande språk:

| Språk | Kod | Tecken gräns |
|----------|------|-----------------|
| Kinesiska | zh | 132 |
| Tyska | de | 290 |
| Italienska | it | 280 |
| Japanska | ja | 150 |
| Portugisiska | pt | 290 |
| Spanska | es | 280 |
| Italienska | it | 280 |
| Thai | i:te | 258 |

> [!NOTE]
> Den här begränsningen gäller inte för översättningar.

## <a name="next-steps"></a>Nästa steg

* [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regional tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator Text API referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
