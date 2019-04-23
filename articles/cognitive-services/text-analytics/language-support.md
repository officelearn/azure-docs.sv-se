---
title: Stöd för språk - API för textanalys
titleSuffix: Azure Cognitive Services
description: 'En lista med naturligt språk som stöds av API för textanalys. Den här artikeln beskriver vilka språk som stöds för varje åtgärd: attitydanalys, extrahering av diskussionsämne, språkidentifiering och igenkänning av entiteter.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 4f1ce8fd44a501f594f3093789d1ef03e664d018
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008502"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Stöd för språk och din region för Textanalys

Den här artikeln beskriver vilka språk som stöds för varje åtgärd: attitydanalys, extrahering av diskussionsämne, språkidentifiering och igenkänning av namngivna entiteter.

## <a name="language-detection"></a>Språkidentifiering

API för textanalys kan identifiera upp till 120 olika språk. Språkidentifiering returnerar ”skript” för ett språk. Exempel för frasen ”jag har en hund” returneras `en` i stället för `en-US`. Endast specialfall är kinesiska, där identifieringsmöjligheter språk returnerar `zh_CHS` eller `zh_CHT` om det kan fastställa skriptet ges text som tillhandahålls. I situationer där ett visst skript inte kan identifieras för ett kinesiska dokument, returneras bara `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Attitydanalys, extrahering av diskussionsämne och igenkänning av namngivna entiteter

För attitydanalys, extrahering av diskussionsämne och entitetsidentifiering är listan över språk som stöds mer selektiv analysatorer är förfinade för språklig reglerna för ytterligare språk.

## <a name="language-list-and-status"></a>Språklista och status

Språk som stöds är inledningsvis lanseras i förhandsversion, de blivit allmänt tillgänglig (GA) status, oberoende av varandra och av övergripande Text Analytics-tjänsten. Det är möjligt för språk som ska behållas i förhandsversion, även under API för textanalys övergår till allmänt tillgänglig.

| Språk    | Språkkod | Sentiment | Nyckelfraser | Igenkänning av namngiven enhet |   Anteckningar  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Arabiska      | `ar`          |           |             | ✔ \*                     | |
| Tjeckiska       | `cs`          |           |             | ✔ \*                     | |
| Chinese-Simplified | `zh-CN`|           |             | ✔ \*        |    |
| Danska      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Nederländska       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Svenska     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Finska     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Franska      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| Tyska      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| Grekiska       | `el`          | ✔ \*     |             |            |     |
| Ungerska   | `hu`          |           |             |  ✔ \*          |     | 
| Italienska     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japanska    | `ja`          |          | ✔           |  ✔ \*          |     |
| Koreanska      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norska (Bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Polska      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portugisiska (Portugal) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt` även accepterat|
| Portugisiska (Brasilien)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Ryska     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Spanska     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| Svenska     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Turkiska     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* Språk som stöds finns i förhandsversion

\*\* Med namnet Entitetsidentifiering och [entitetslänkning](how-tos/text-analytics-how-to-entity-linking.md) finns både för det här språket.    

## <a name="see-also"></a>Se också

[Dokumentationssidan för cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktsida om Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
