---
title: Språk stöd – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller en lista över naturliga språk som stöds av Visuellt innehåll funktioner; OCR, bild analys.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 649f1e6cf79aa17d053cc547debf5232120bb4fe
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760642"
---
# <a name="language-support-for-computer-vision"></a>Språk stöd för Visuellt innehåll

Vissa funktioner i Visuellt innehåll stöd för flera språk. de funktioner som inte nämns här stöder bara engelska.

## <a name="optical-character-recognition-ocr"></a>Optisk teckenläsning (OCR)

Visuellt innehålls OCR-API: er stöder flera språk. De kräver inte att du anger en språkkod. Mer information finns i [OCR (optisk tecken läsning)](concept-recognizing-text.md) .

|Språk| Språkkod | OCR-API | Läsa v 3.0 | Läs v 3.1 offentlig för hands version |
|:-----|:----:|:-----:|:---:|:---:|
|Arabiska | `ar`|✔ | | |
|Kinesiska (förenklad) | `zh-Hans`|✔ | |✔ |
|Kinesiska (traditionell) | `zh-Hant`|✔ | | |
|Tjeckiska | `cs` |✔ | | |
|Danska | `da` |✔ | | |
|Nederländska | `nl` |✔ |✔ |✔ |
|Engelska | `en` |✔ |✔ |✔ |
|Finska | `fi` |✔ | | |
|Franska | `fr` |✔ |✔ |✔ |
|Tyska | `de` |✔ |✔ |✔ |
|Grekiska | `el` |✔ | | |
|Ungerska | `hu` |✔ | | |
|Italienska | `it` |✔ |✔ |✔ |
|Japanska | `ja` |✔ | | |
|Koreanska | `ko` |✔ | | |
|Norska | `nb` |✔ | | |
|Polska | `pl` |✔ | | |
|Portugisiska | `pt` |✔ |✔ |✔ |
|Rumänska | `ro` |✔ | | |
|Ryska | `ru` |✔ | | |
|Serbiska (kyrillisk) | `sr-Cyrl` |✔ | | |
|Serbiska (latinsk) (Serbien) | `sr-Latn` |✔ | | |
|Slovakiska | `sk` |✔ | | |
|Spanska | `es` |✔ |✔ |✔ |
|Svenska | `sw` |✔ | | |
|Turkiska | `tr` |✔ | | |

## <a name="image-analysis"></a>Bild analys

Vissa åtgärder för API: et [analys-image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) kan returnera resultat på andra språk, som anges med `language` Frågeparametern. Andra åtgärder returnerar resultat på engelska oavsett vilket språk som anges, och andra genererar ett undantag för språk som inte stöds. Åtgärder anges med `visualFeatures` `details` parametrarna och, se en lista över alla [Overview](home.md) åtgärder som du kan utföra med bild analys.

|Språk | Språkkod | Kategorier | Taggar | Beskrivning | Vuxen | Varumärken | Färg | Ansikten | ImageType | Objekt | Kändisar | Landmärken |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Kinesiska | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Engelska | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japanska | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugisiska | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spanska | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Nästa steg

Kom igång med Visuellt innehåll funktionerna som nämns i den här hand boken.

* [Analysera en lokal avbildning (REST)](./quickstarts/csharp-analyze.md)
* [Extrahera utskriven text (REST)](./quickstarts/csharp-print-text.md)
