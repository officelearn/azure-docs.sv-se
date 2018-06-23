---
title: Språk som stöds i Text Analytics API - kognitiva Azure-tjänster | Microsoft Docs
description: 'Lista över allmänt tillgänglig och förhandsgranska språk stöd för Text Analytics-API: et. Gäller för sentiment analys, viktiga frasen extrahering och identifiera språk.'
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 2d341cfaf261bea6367bb55dd5d322f419e22d34
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355185"
---
# <a name="supported-languages-in-the-text-analytics-api"></a>Språk som stöds i Text Analytics-API

Den här artikeln beskriver vilka språk som stöds för varje åtgärd: sentiment analys, viktiga frasen extrahering och identifiera språk.

## <a name="language-detection"></a>Språkidentifiering

Text Analytics API kan identifiera upp till 120 olika språk. Identifiera språk returnerar ”skript” för ett språk. Till exempel för frasen ”jag har en hund” returneras `en` i stället för `en-US`. Endast särskilda fall är kinesiska, där kapaciteten för identifiering av språk returnerar `zh_CHS` eller `zh_CHT` om den kan fastställa skriptet anges den text som angetts. I situationer där ett visst skript inte kan identifieras för ett kinesiskt dokument, returneras bara `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>Sentiment analys, viktiga frasen extrahering och länka entitet

Sentiment analys, viktiga frasen extrahering och entiteten länka, är lista över språk som stöds mer selektiv eftersom analyzers förfinade för språkliga reglerna för ytterligare språk.

## <a name="language-list-and-status"></a>Lista över språk och status

Språkstöd distribuerat ursprungligen i preview examen till allmänt tillgänglig (GA) status, oberoende av varandra och av övergripande Text Analytics-tjänsten. Det är möjligt för språk finns kvar i preview även Text Analytics API övergår till allmänt tillgänglig.

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
| Portugisiska (Portugal) | `pt-PT`| ✔        |  ✔          |       |`pt` också godkänns|
| Portugisiska (Brasilien)   | `pt-BR`|          |  ✔   |         |     |
| Ryska     | `ru`          | ✔ \*     | ✔           |             |     |
| Spanska     | `es`          | ✔        | ✔           |     |     |
| Svenska     | `sv`          | ✔ \*     | ✔           |             |     |
| Turkiska     | `tr`          | ✔ \*     |             |             |     |

\* Anger språkstöd i preview

## <a name="see-also"></a>Se också

[Kognitiva dokumentationssidan för tjänster](https://docs.microsoft.com/azure/cognitive-services/)   
[Kognitiva produktsidan för tjänster](https://azure.microsoft.com/services/cognitive-services/)
