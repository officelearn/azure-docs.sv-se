---
title: Språk stöd – API för textanalys
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk som stöds av API för textanalys. I den här artikeln förklaras vilka språk som stöds för varje åtgärd.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: aahi
ms.openlocfilehash: b3cec783b43d0169a3d0d56ca040e1397701102e
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986176"
---
# <a name="text-analytics-api-v3-language-support"></a>Språk stöd för API för textanalys v3 

> [!IMPORTANT]
> Version 3. x av API för textanalys är för närvarande inte tillgänglig i följande regioner: centrala Indien, Förenade Arabemiraten Nord, Kina, norra 2, Kina, östra.


#### <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment-analysis)

| Språk              | Språkkod | v2-stöd | v3-stöd | Startar v3-modell version: |              Anteckningar |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Kinesiska – Förenklad    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh`även godkänd |
| Kinesiska (traditionell)   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Danska               |     `da`      |     ✓      |            |                            |                    |
| Nederländska                 |     `nl`      |     ✓      |            |                            |                    |
| Engelska               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finska               |     `fi`      |     ✓      |            |                            |                    |
| Franska                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Tyska                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Grekiska                 |     `el`      |     ✓      |            |                            |                    |
| Italienska               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japanska              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Koreanska                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norska (bokmål)   |     `no`      |     ✓      |            |                            |                    |
| Polska                |     `pl`      |     ✓      |            |                            |                    |
| Portugisiska (Portugal) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt`även godkänd |
| Ryska               |     `ru`      |     ✓      |            |                            |                    |
| Spanska               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Svenska               |     `sv`      |     ✓      |            |                            |                    |
| Turkiska               |     `tr`      |     ✓      |            |                            |                    |

### <a name="opinion-mining-v31-preview-only"></a>Utåsikts utvinning (v 3.1 – endast för hands version)

| Språk              | Språkkod | Från och med v3 modell version: |              Anteckningar |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Engelska               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Igenkänning av namngivna enheter (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * NER v3 stöder för närvarande bara engelska och spanska språk. Om du anropar NER v3 med ett annat språk returneras v 2.1-API: et, förutsatt att språket stöds i version 2,1.
> * v 2.1 returnerar bara den fullständiga uppsättningen tillgängliga entiteter för de engelska, kinesiska, enkla, franska, tyska och spanska språken.  Entiteterna "person", "plats" och "organisation" returneras för de andra språk som stöds.

| Språk               | Språkkod | v 2.1-stöd | v3-stöd | Från och med v3 modell version: |       Anteckningar        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arabiska                |     `ar`      |     ✓      |            |                                 |                    |
| Tjeckiska                 |     `cs`      |     ✓      |            |                                 |                    |
| Kinesiska – Förenklad     |   `zh-hans`   |     ✓      |            |                                 | `zh`även godkänd |
| Kinesiska (traditionell)   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Danska                |     `da`      |     ✓      |            |                                 |                    |
| Nederländska                 |     `nl`      |     ✓      |            |                                 |                    |
| Engelska                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Finska               |     `fi`      |     ✓      |            |                                 |                    |
| Franska                 |     `fr`      |     ✓      |            |                                 |                    |
| Tyska                 |     `de`      |     ✓      |            |                                 |                    |
| Hebreiska                |     `he`      |     ✓      |            |                                 |                    |
| Ungerska             |     `hu`      |     ✓      |            |                                 |                    |
| Italienska               |     `it`      |     ✓      |            |                                 |                    |
| Japanska              |     `ja`      |     ✓      |            |                                 |                    |
| Koreanska                |     `ko`      |     ✓      |            |                                 |                    |
| Norska (bokmål)   |     `no`      |     ✓      |            |                                 | `nb`även godkänd |
| Polska                |     `pl`      |     ✓      |            |                                 |                    |
| Portugisiska (Portugal) |    `pt-PT`    |     ✓      |            |                                 | `pt`även godkänd |
| Portugisiska (Brasilien)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Ryska              |     `ru`      |     ✓      |            |                                 |                    |
| Spanska               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Svenska               |     `sv`      |     ✓      |            |                                 |                    |
| Turkiska               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Extrahering av nyckel fraser](#tab/key-phrase-extraction)

| Språk              | Språkkod | v2-stöd | v3-stöd | Tillgängligt från och med v3-modell version: |       Anteckningar        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Nederländska                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Engelska               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finska               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Franska                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Tyska                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italienska               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japanska              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Koreanska                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norska (bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb`även godkänd |
| Polska                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugisiska (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt`även godkänd |
| Portugisiska (Brasilien)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Ryska               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Spanska               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Svenska               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Länkning av entitet](#tab/entity-linking)

| Språk | Språkkod | v2-stöd | v3-stöd | Tillgängligt från och med v3-modell version: | Anteckningar |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Engelska  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Spanska  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Språkidentifiering](#tab/language-detection)

API för textanalys kan identifiera en mängd olika språk, varianter, dialekter och vissa regionala/kulturella språk.  Språkidentifiering returnerar "skript" för ett språk. För frasen "Jag har en hund" kommer den att returneras `en` i stället för `en-US` . Det enda särskilda fallet är kinesiska, där språk identifierings funktionen kommer att returnera `zh_CHS` eller `zh_CHT` om den kan fastställa ett skript som har fått den angivna texten. I situationer där det inte går att identifiera ett särskilt skript för ett kinesiskt dokument kommer det bara att returnera `zh` .

Vi publicerar inte den exakta listan över språk för den här funktionen, men den kan identifiera en mängd olika språk, varianter, dialekter och vissa regionala/kulturella språk. 

Om du har innehåll som uttrycks på ett mindre vanligt språk kan du prova Språkidentifiering för att se om det returnerar en kod. Svaret för språk som inte kan identifieras är `unknown` .

---

## <a name="see-also"></a>Se även

* [Vad är API för textanalys?](overview.md)   
