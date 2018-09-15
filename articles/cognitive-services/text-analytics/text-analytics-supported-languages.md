---
title: Språk som stöds i API för textanalys
titleSuffix: Azure Cognitive Services
description: Lista över allmänt tillgängliga och förhandsversion språk stöd för API för textanalys åtgärder. Gäller för attitydanalys, extrahering av diskussionsämne och språkidentifiering.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: ashmaka
ms.openlocfilehash: 984947903a91837910493618bd7abc2cdfe0da71
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603251"
---
# <a name="supported-languages-in-the-text-analytics-cognitive-service"></a>Språk som stöds i Text Analytics Cognitive Service

Den här artikeln beskriver vilka språk som stöds för varje åtgärd: attitydanalys, extrahering av diskussionsämne och språkidentifiering.

## <a name="language-detection"></a>Språkidentifiering

API för textanalys kan identifiera upp till 120 olika språk. Språkidentifiering returnerar ”skript” för ett språk. Exempel för frasen ”jag har en hund” returneras `en` i stället för `en-US`. Endast specialfall är kinesiska, där identifieringsmöjligheter språk returnerar `zh_CHS` eller `zh_CHT` om det kan fastställa skriptet ges text som tillhandahålls. I situationer där ett visst skript inte kan identifieras för ett kinesiska dokument, returneras bara `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>Attitydanalys, extrahering av diskussionsämne och Entitetslänkning

För attitydanalys, extrahering av diskussionsämne och entitetslänkning, är listan över språk som stöds mer selektiv analysatorer är förfinade för språklig reglerna för ytterligare språk.

## <a name="language-list-and-status"></a>Språklista och status

Språk som stöds är inledningsvis lanseras i förhandsversion, de blivit allmänt tillgänglig (GA) status, oberoende av varandra och av övergripande Text Analytics-tjänsten. Det är möjligt för språk som ska behållas i förhandsversion, även under API för textanalys övergår till allmänt tillgänglig.

| Språk    | Språkkod | Sentiment | Nyckelfraser | Entity Linking |   Anteckningar  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Danska      | `da`          | ✔ \*     | ✔           |             |     |
| Nederländska       | `nl`          | ✔ \*     | ✔          |             |     |
| Svenska     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| Finska     | `fi`          | ✔ \*     | ✔           |             |     |
| Franska      | `fr`          | ✔        | ✔           |             |     |
| Tyska      | `de`          | ✔ \*     | ✔           |            |     |
| Grekiska       | `el`          | ✔ \*     |             |            |     |
| Italienska     | `it`          | ✔ \*     | ✔           |             |     |
| Japanska    | `ja`          |          | ✔           |            |     |
| Koreanska      | `ko`          |          | ✔           |            |     |
| Norska (Bokmål) | `no`          | ✔ \*     |  ✔          |             |     |
| Polska      | `pl`          | ✔ \*     |  ✔          |             |     |
| Portugisiska (Portugal) | `pt-PT`| ✔        |  ✔          |       |`pt` även accepterat|
| Portugisiska (Brasilien)   | `pt-BR`|          |  ✔   |         |     |
| Ryska     | `ru`          | ✔ \*     | ✔           |             |     |
| Spanska     | `es`          | ✔        | ✔           |     |     |
| Svenska     | `sv`          | ✔ \*     | ✔           |             |     |
| Turkiska     | `tr`          | ✔ \*     |             |             |     |

\* Anger språkstöd i förhandsversion

## <a name="see-also"></a>Se också

[Dokumentationssidan för cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktsida om Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
