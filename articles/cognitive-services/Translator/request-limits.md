---
title: Begärandebegränsningar – Translator Text API
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar begäran gränser för Translator Text API. Debitering baserat på antal tecken, inte begäran frekvens med en gräns på 5 000 tecken per begäran. Tecknet gränserna är prenumerationsbaserade med F0 begränsat till 2 miljoner tecken per timme.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: c19e39918aa64730a35a27fcdadd70800f47f4fa
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514641"
---
# <a name="request-limits-for-translator-text"></a>Gränser för begäran för textöversättning

Den här artikeln innehåller begränsningar gränser för Translator Text API. Tjänsterna omfattar översättning, transkriberingsspråk, mening längd identifiering, språkidentifiering och alternativa översättningar.

## <a name="character-and-array-limits-per-request"></a>Tecken och matrisen gränserna per begäran

Varje Översätt-begäran är begränsad till 5 000 tecken. Du debiteras per tecken, inte av antalet begäranden. Vi rekommenderar att du skickar kortare begäranden.

Följande tabell visar matris element och tecknet begränsningar för varje åtgärd av Translator Text API.

| Åtgärd | Maximal storlek för matriselement |   Maxantalet matriselement |  Begär maxstorleken (tecken) |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10 000 | 100 |   50,000 |
| BreakSentence | 10 000    | 100 | 5,0000 |
| Slå upp i ordlista| 100 |  10  | 1,000 |
| Ordlisteexempel | 100 för text och 100 för översättning (200 totalt)| 10|   2,000 |

## <a name="character-limits-per-hour"></a>Tecknet gränserna per timme

Din teckengränsen per timme baserat på textöversättning prenumeration nivå. Timvis kvot ska förbrukas jämnt under hela timmen. Om du når eller överskrider gränserna eller skicka för stor del av en del av kvoten i en kort tidsperiod, får du sannolikt en out of kvot svar.

| Nivå | Maximalt antal tillåtna tecken |
|------|-----------------|
| F0 | 2 miljoner tecken per timme |
| S1 | 40 miljoner tecken per timme |
| S2 / C2 | 40 miljoner tecken per timme |
| S3 / C3 | 120 miljoner tecken per timme |
| S4 / C4 | 200 miljoner tecken per timme |

Nätverksgränser [flera tjänster prenumerationer](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) är samma som på nivån S1.

Dessa gränser är begränsade till Microsofts standard översättningsmodeller. Anpassade översättningsmodeller som använder anpassade Translator är begränsade till 1 800 tecken per sekund.

## <a name="latency"></a>Svarstid

Translator Text API har en maximal fördröjning på 15 sekunder med hjälp av standard-modeller. Översättning med hjälp av anpassade modeller har en maximal fördröjning på 25 sekunder. Vid den tidpunkten kommer du har fått ett resultat eller ett timeout-svar. Normalt returneras svar i 150 millisekunder till 300 millisekunder. Svarstider kan variera beroende på storleken på begäran och språk-par. Om du inte får en översättning eller ett [felsvar](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) inom den tidsramen bör du kontrollera nätverksanslutningen och försök igen.

## <a name="sentence-length-limits"></a>Längdbegränsningar för meningen

När du använder den [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) funktion, mening längden är begränsad till 275 tecken. Det finns undantag för följande språk:

| Språk | Kod | Maximalt antal tillåtna tecken |
|----------|------|-----------------|
| Kinesiska | zh | 132 |
| Tyska | de | 290 |
| Italienska | it | 280 |
| Japanska | ja | 150 |
| Portugisiska | pt | 290 |
| Spanska | es | 280 |
| Italienska | it | 280 |
| Thai | TH | 258 |

> [!NOTE]
> Den här gränsen gäller inte för översättningar.

## <a name="next-steps"></a>Nästa steg

* [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regional tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referens för Translator Text API v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
