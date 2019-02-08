---
title: Stöd för språk - API för textanalys
titleSuffix: Azure Cognitive Services
description: 'En lista med naturligt språk som stöds av API för textanalys. Den här artikeln beskriver vilka språk som stöds för varje åtgärd: attitydanalys, extrahering av diskussionsämne, språkidentifiering och igenkänning av entiteter.'
services: cognitive-services
author: ashmaka
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: 421d891ea6c580cfbdd7cfc40755b43e1d4b207e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871293"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Stöd för språk och din region för Textanalys

Den här artikeln beskriver vilka språk som stöds för varje åtgärd: attitydanalys, extrahering av diskussionsämne och språkidentifiering.

## <a name="language-detection"></a>Språkidentifiering

API för textanalys kan identifiera upp till 120 olika språk. Språkidentifiering returnerar ”skript” för ett språk. Exempel för frasen ”jag har en hund” returneras `en` i stället för `en-US`. Endast specialfall är kinesiska, där identifieringsmöjligheter språk returnerar `zh_CHS` eller `zh_CHT` om det kan fastställa skriptet ges text som tillhandahålls. I situationer där ett visst skript inte kan identifieras för ett kinesiska dokument, returneras bara `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-recognition"></a>Attitydanalys, extrahering av diskussionsämne och Entitetsidentifiering

För attitydanalys, extrahering av diskussionsämne och entitetsidentifiering är listan över språk som stöds mer selektiv analysatorer är förfinade för språklig reglerna för ytterligare språk.

## <a name="language-list-and-status"></a>Språklista och status

Språk som stöds är inledningsvis lanseras i förhandsversion, de blivit allmänt tillgänglig (GA) status, oberoende av varandra och av övergripande Text Analytics-tjänsten. Det är möjligt för språk som ska behållas i förhandsversion, även under API för textanalys övergår till allmänt tillgänglig.

| Språk    | Språkkod | Sentiment | Nyckelfraser | Igenkänning av enhet |   Anteckningar  |
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
| Spanska     | `es`          | ✔        | ✔           |   ✔ \*\*      |     |
| Svenska     | `sv`          | ✔ \*     | ✔           |             |     |
| Turkiska     | `tr`          | ✔ \*     |             |             |  |

\* Anger språkstöd i förhandsversion

\*\* Entitetextrahering för spanska finns bara i [(version 2.1-förhandsversion)](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)

## <a name="see-also"></a>Se också

[Dokumentationssidan för cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktsida om Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
