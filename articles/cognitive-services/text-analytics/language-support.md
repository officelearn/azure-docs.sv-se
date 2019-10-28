---
title: Språk stöd – API för textanalys
titleSuffix: Azure Cognitive Services
description: 'En lista över naturliga språk som stöds av API för textanalys. I den här artikeln förklaras vilka språk som stöds för varje åtgärd: sentiment analys, extrahering av nyckel fraser, språk identifiering och enhets igenkänning.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 846650fce6701bb7e382df049902f427390b3051
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931217"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Språk-och region stöd för API för textanalys

I den här artikeln förklaras vilka språk som stöds för varje åtgärd: sentiment analys, extrahering av nyckel fraser, språk identifiering och identifiering av namngivna entiteter.

## <a name="language-detection"></a>Språkidentifiering

API för textanalys kan identifiera en mängd olika språk, varianter, dialekter och vissa regionala/kulturella språk.  Språkidentifiering returnerar "skript" för ett språk. För frasen "Jag har en hund" kommer den att returnera `en` i stället för `en-US`. Det enda särskilda fallet är kinesiska, där språk identifierings funktionen returnerar `zh_CHS` eller `zh_CHT` om det kan fastställa skriptet med texten som anges. I situationer där det inte går att identifiera ett särskilt skript för ett kinesiskt dokument kommer det bara att returnera `zh`.

Vi publicerar inte den exakta listan över språk för den här funktionen, men den kan identifiera en mängd olika språk, varianter, dialekter och vissa regionala/kulturella språk. 

Om du har innehåll som uttrycks på ett mindre vanligt språk kan du prova Språkidentifiering för att se om det returnerar en kod. Svaret för språk som inte kan identifieras är `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Attitydanalys, Extrahering av diskussionsämne och identifiering av namngiven entitet

För sentiment-analys, extrahering av nyckel fraser och enhets igenkänning är listan över språk som stöds mer selektiv eftersom analys enheterna är förfinade för att hantera språkliga regler för ytterligare språk. Stöd för den fullständiga uppsättningen [entitetstyper](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition) är för närvarande begränsat till följande språk: 
* Svenska
* Kinesiska – Förenklad
* Franska
* Tyska
* Spanska

Endast `Person`, `Location` och `Organization` namngivna entiteter returneras för de andra språken.

## <a name="language-list-and-status"></a>Språk lista och status

Språk stöd är inlednings vis insamlat i för hands version, som är i allmänhet tillgänglig (GA) status, oberoende av varandra och för Textanalys tjänsten. Det är möjligt att språken förblir i för hands versionen, även om API för textanalys över gångar till allmänt tillgängliga.

| Språk    | Språkkod | Sentiment | Nyckelfraser | Igenkänning av namngiven entitet |   Anteckningar  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Arabiska      | `ar`          |           |             | ✔ \*                     | |
| Tjeckiska       | `cs`          |           |             | ✔ \*                     | |
| Kinesiska – Förenklad | `zh-hans`| ✔ \***     |             | ✔         |    |
| Kinesiska (traditionell) | `zh-hant`| ✔ \***     |             |          |    |
| Danska      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Nederländska       | `nl`          | ✔ \***     | ✔          |  ✔ \*           |     |
| Svenska     | `en`          | ✔ \***       | ✔           |  ✔ \*\*     |      |
| Finska     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Franska      | `fr`          | ✔ \***       | ✔           |  ✔            |     |
| Tyska      | `de`          | ✔ \***     | ✔           |  ✔           |     |
| Grekiska       | `el`          | ✔ \*     |             |            |     |
| Ungerska   | `hu`          |           |             |  ✔ \*          |     | 
| Italienska     | `it`          | ✔ \***     | ✔           |  ✔ \*           |     |
| Japanska    | `ja`          | ✔ \***         | ✔           |  ✔ \*          |     |
| Koreanska      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norska (bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Polska      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portugisiska (Portugal) | `pt-PT`| ✔ \***        |  ✔          | ✔ \*      |`pt` också accepterat|
| Portugisiska (Brasilien)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Ryska     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Spanska     | `es`          | ✔ \***       | ✔           |   ✔ \*\*      |     | 
| Svenska     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Turkiska     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* språk stöd finns i för hands version

\*\* [med namnet entitets igenkänning](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-ner) och [enhets länkning](how-tos/text-analytics-how-to-entity-linking.md#entity-linking) är båda tillgängliga för det här språket.  

\** * tillgängligt i [Attitydanalys v3 offentlig för hands version](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-v3-public-preview)

## <a name="see-also"></a>Se också

[Cognitive Services dokumentations sida](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktsida om Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
