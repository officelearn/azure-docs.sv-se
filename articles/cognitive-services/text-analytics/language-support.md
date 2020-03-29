---
title: Språkstöd - API för textanalys
titleSuffix: Azure Cognitive Services
description: 'En lista över naturliga språk som stöds av API:et för textanalys. I den här artikeln beskrivs vilka språk som stöds för varje åtgärd: sentimentanalys, nyckelfrasutextrahering, språkidentifiering och entitetsigenkänning.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219284"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Språk- och regionstöd för API:et för textanalys

I den här artikeln beskrivs vilka språk som stöds för varje åtgärd: sentimentanalys, nyckelfrasutextrahering, språkidentifiering och namngiven entitetsigenkänning.

## <a name="language-detection"></a>Språkidentifiering

Api:et för textanalys kan identifiera ett brett spektrum av språk, varianter, dialekter och vissa regionala/kulturella språk.  Språkidentifiering returnerar ett språks "skript". Till exempel, för frasen "Jag har `en` en `en-US`hund" det kommer tillbaka i stället för . Det enda specialfallet är kinesiska, där `zh_CHS` språkidentifieringsfunktionen kommer tillbaka eller `zh_CHT` om den kan bestämma skriptet med tanke på den text som tillhandahålls. I situationer där ett visst skript inte kan identifieras `zh`för ett kinesiskt dokument returneras det helt enkelt .

Vi publicerar inte den exakta listan över språk för den här funktionen, men den kan upptäcka ett brett spektrum av språk, varianter, dialekter och vissa regionala / kulturella språk. 

Om du har innehåll som uttrycks på ett språk som används mindre ofta kan du prova Språkidentifiering för att se om den returnerar en kod. Svaret för språk som inte `unknown`kan identifieras är .

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Sentimentanalys, extrahering av nyckelfraser och namngiven entitetsigenkänning

För sentimentanalys, nyckelfrasutextraktion och entitetsigenkänning är listan över språk som stöds mer selektiv eftersom analysatorerna förfinas för att tillgodose de språkliga reglerna för ytterligare språk. I Namngiven entitetsigenkänning v2 är stödet för hela uppsättningen [entitetstyper](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) för närvarande begränsat till följande språk: 
* Svenska
* Kinesisk-förenklad
* Franska
* Tyska
* Spanska

Endast `Person`de `Location` `Organization` entiteter som anges och namnges returneras för de andra språken.

## <a name="language-list-and-status"></a>Språklista och status

Språkstöd lanseras ursprungligen i förhandsversion, examen till allmänt tillgängliga (GA) status, oberoende av varandra och textanalystjänsten totalt. Det är möjligt för språk att stanna kvar i förhandsgranskningen, även om API för textanalys övergår till allmänt tillgängliga.

> [!NOTE]
> Detaljerad språkstöd för den offentliga förhandsversionen av Namngiven entitetsigenkänning(NER) och information finns i [Namngivna entitetstyper](named-entity-types.md).

| Språk              | Språkkod | Sentiment | Nyckelfraser | Igenkänning av namngiven enhet | Entitetslänkning |       Anteckningar        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arabiska                |     `ar`      |           |             |           ✔\*           |                |                    |
| Tjeckiska                 |     `cs`      |           |             |           ✔\*           |                |                    |
| Kinesisk-förenklad    |   `zh-hans`   |  ✔\*\*   |             |            ✔             |                | `zh`också accepterat                   |
| Kinesisk-traditionell   |   `zh-hant`   |  ✔\*\*   |             |                          |                |                    |
| Danska                |     `da`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Nederländska                 |     `nl`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Svenska               |     `en`      |   ✔\**   |      ✔      |          ✔\*\*          |     ✔\**      |                    |
| Finska               |     `fi`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Franska                |     `fr`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Tyska                |     `de`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Grekiska                 |     `el`      |   ✔\*    |             |                          |                |                    |
| Ungerska             |     `hu`      |           |             |           ✔\*           |                |                    |
| Italienska               |     `it`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Japanska              |     `ja`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Koreansk                |     `ko`      |   ✔\*\*  |      ✔      |           ✔\*           |                |                    |
| Norska (Bokmål)   |     `no`      |   ✔\*    |      ✔      |           ✔\*           |                | `nb`också accepterat                   |
| Polska                |     `pl`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Portugisiska (Portugal) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔\*           |                | `pt`också accepterat |
| Portugisiska (Brasilien)   |    `pt-BR`    |           |      ✔      |           ✔\*           |                |                    |
| Ryska               |     `ru`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Spanska               |     `es`      |   ✔\**    |      ✔      |           ✔\*           |     ✔\**      |                    |
| Svenska               |     `sv`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Turkiska               |     `tr`      |   ✔\*    |             |           ✔\*           |                |                    |

\*Språkstöd är i förhandsgranskning

\** Finns också i [Sentiment Analysis v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) och / eller [named entity recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) offentliga förhandsvisningar.

## <a name="see-also"></a>Se även

[Dokumentationssida för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktsida om Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
