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
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 97b0b6256b7aaf7b42565fe9453fb87a0c414569
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861578"
---
# <a name="request-limits-for-translator-text"></a>Gränser för begäran för textöversättning

Den här artikeln innehåller begränsningar gränser för Translator Text API. Tjänsterna omfattar översättning, transkriberingsspråk, mening längd identifiering, språkidentifiering och alternativa översättningar.

## <a name="character-limits-per-request"></a>Tecknet gränserna per begäran

Varje begäran är begränsad till 5 000 tecken. Du debiteras per tecken, inte av antalet begäranden. Vi rekommenderar att kortare sändningsförfrågningar och har några förfrågningar utestående vid en given tidpunkt.

Det finns ingen gräns för antalet väntande förfrågningar till Translator Text API.

## <a name="character-limits-per-hour"></a>Tecknet gränserna per timme

Din teckengränsen per timme baserat på textöversättning prenumeration nivå. Om du når eller överskrider gränserna, får du sannolikt en out of kvot svaret:

| Nivå | Maximalt antal tillåtna tecken |
|------|-----------------|
| F0 | 2 miljoner tecken per timme |
| S1 | 40 miljoner tecken per timme |
| S2 | 40 miljoner tecken per timme |
| S3 | 120 miljoner tecken per timme |
| S4 | 200 miljoner tecken per timme |

Dessa gränser är begränsade till Microsofts allmänna system. Anpassade översättningssystem som använder Microsoft Translator hubb är begränsade till 1 800 tecken per sekund.

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
| Thai | :e | 258 |

> [!NOTE]
> Den här gränsen gäller inte för översättningar.

## <a name="next-steps"></a>Nästa steg

* [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regional tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referens för Translator Text API v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
