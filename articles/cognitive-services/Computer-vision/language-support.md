---
title: Språkstöd - Datorseende
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller en lista över naturliga språk som stöds av funktioner för datorseende. OCR, Känn igen text och läsa.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220142"
---
# <a name="language-support-for-computer-vision"></a>Språkstöd för datorseende

Vissa funktioner i Datorseende stöder flera språk. alla funktioner som inte nämns här stöder bara engelska.

## <a name="text-recognition"></a>Textigenkänning

Computer Vision kan känna igen text på många språk. Ocr-API:et stöder en mängd olika språk, medan [Läs-API:et](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) och [API:et för inkänns](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) endast engelska. [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) Se [Känna igen utskriven och handskriven text](concept-recognizing-text.md) för mer information om den här funktionen och fördelarna med varje API.

OCR identifierar automatiskt språket i indatamaterialet, så det finns ingen anledning att ange en språkkod i API-anropet. Språkkoder returneras dock alltid som värdet `"language"` för noden i JSON-svaret.

|Språk| Språkkod | OCR-API |
|:-----|:----:|:-----:|
|Arabiska | `ar`|✔ |
|Kinesiska (förenklad) | `zh-Hans`|✔ |
|Kinesiska (traditionell) | `zh-Hant`|✔ |
|Tjeckiska | `cs` |✔ |
|Danska | `da` |✔ |
|Nederländska | `nl` |✔ |
|Svenska | `en` |✔ |
|Finska | `fi` |✔ |
|Franska | `fr` |✔ |
|Tyska | `de` |✔ |
|Grekiska | `el` |✔ |
|Ungerska | `hu` |✔ |
|Italienska | `it` |✔ |
|Japanska | `ja` |✔ |
|Koreansk | `ko` |✔ |
|Norska | `nb` |✔ |
|Polska | `pl` |✔ |
|Portugisiska | `pt` |✔ |
|Rumänska | `ro` |✔ |
|Ryska | `ru` |✔ |
|Serbiska (kyrillisk) | `sr-Cyrl` |✔ |
|Serbiska (latinsk) (Serbien) | `sr-Latn` |✔ |
|Slovakiska | `sk` |✔ |
|Spanska | `es` |✔ |
|Svenska | `sw` |✔ |
|Turkiska | `tr` |✔ |

## <a name="image-analysis"></a>Bildanalys

Vissa åtgärder i [Api för analys - avbildning](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) kan `language` returnera resultat på andra språk som anges med frågeparametern. Andra åtgärder returnerar resultat på engelska oavsett vilket språk som anges, och andra genererar ett undantag för språk som inte stöds. Åtgärder anges med `visualFeatures` parametrarna och `details` fråga. se [Översikten](home.md) för en lista över alla åtgärder du kan göra med bildanalys.

|Språk | Språkkod | Kategorier | Taggar | Beskrivning | Vuxen | Varumärken | Color | Ansikten | Bildtyp | Objekt | Kändisar | Landmärken |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Kinesiska | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Svenska | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japanska | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugisiska | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spanska | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Nästa steg

Kom igång med de datorseendefunktioner som nämns i den här guiden.

* [Analysera en lokal bild (REST)](./quickstarts/csharp-analyze.md)
* [Extrahera utskriven text (REST)](./quickstarts/csharp-print-text.md)