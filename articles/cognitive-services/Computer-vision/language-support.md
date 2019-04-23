---
title: Stöd för språk - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: En lista med naturligt språk som stöds av visuellt funktioner.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012657"
---
# <a name="language-support-for-computer-vision"></a>Språkstöd för visuellt innehåll

Vissa funktioner i visuellt stöd för flera språk. alla funktioner som inte anges här har endast stöd för engelska.

## <a name="text-recognition"></a>Textigenkänning

Visuellt innehåll kan identifiera text i många andra språk. Mer specifikt den [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API stöder en mängd olika språk, medan den [Läs](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API och [identifiera Text](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API har endast stöd för engelska. Se [känner igen utskrivna och handskriven text](concept-recognizing-text.md) för mer information om den här funktionen och fördelarna med varje API.

OCR identifierar automatiskt språket i det inkommande materialet, så du behöver inte ange en språkkod i API-anrop. Dock språkkoder returneras alltid som värde för den `"language"` nod i JSON-svar.

|Språk| Språkkod | OCR-API |
|:-----|:----:|:-----:|
|Arabiska | `ar`|✔ |
|Förenklad kinesiska | `zh-Hans`|✔ |
|Traditionell kinesiska | `zh-Hant`|✔ |
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
|Koreanska | `ko` |✔ |
|Norska | `nb` |✔ |
|Polska | `pl` |✔ |
|Portugisiska | `pt` |✔ |
|Rumänska | `ro` |✔ |
|Ryska | `ru` |✔ |
|Serbiska (kyrillisk) | `sr-Cyrl` |✔ |
|Serbiska (latinsk) | `sr-Latn` |✔ |
|Slovakiska | `sk` |✔ |
|Spanska | `es` |✔ |
|Svenska | `sw` |✔ |
|Turkiska | `tr` |✔ |

## <a name="image-analysis"></a>Bildanalys

Vissa åtgärder på den [analysera – bild](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API kan returnera resultat på andra språk som anges med den `language` frågeparameter. Andra åtgärder oavsett vilket språk har angetts returnerar resultat i engelska och andra utlöser ett undantag för språk som inte stöds. Åtgärder som har angetts med den `visualFeatures` och `details` frågeparametrar, se den [översikt](home.md) en lista över de åtgärder som du kan göra med bildanalys.

|Språk | Språkkod | Categories | Taggar | Beskrivning | Vuxet | Varumärken | Färg | Ansikten | Bildtyp | Objekt | Kändisar | Landmärken |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Kinesiska | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Svenska | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japanska | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugisiska | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spanska | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Nästa steg

Kom igång med funktionerna för visuellt innehåll som nämns i den här guiden.

* [Analysera en lokal avbildning (REST)](./quickstarts/csharp-analyze.md)
* [Extrahera utskrivna text (REST)](./quickstarts/csharp-print-text.md)