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
ms.openlocfilehash: b065b36103b69f0601daa1388b45865856543d2b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540526"
---
# <a name="language-support-for-computer-vision"></a>Språk stöd för Visuellt innehåll

Vissa funktioner i Visuellt innehåll stöd för flera språk. de funktioner som inte nämns här stöder bara engelska.

## <a name="optical-character-recognition-ocr"></a>Optisk teckenläsning (OCR)

Visuellt innehålls OCR-API: er stöder flera språk. De kräver inte att du anger en språkkod. Mer information finns i [OCR (optisk tecken läsning)](concept-recognizing-text.md) .

|Språk| Språkkod | OCR-API | Läsa 3,0 och 3,1 | Läs v 3.2 – för hands version. 1 |
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
|Japanska | `ja` |✔ | |✔ |
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

## <a name="image-analysis"></a>Bildanalys

Vissa åtgärder för API: et [analys-image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) kan returnera resultat på andra språk, som anges med `language` Frågeparametern. Andra åtgärder returnerar resultat på engelska oavsett vilket språk som anges, och andra genererar ett undantag för språk som inte stöds. Åtgärder anges med `visualFeatures` `details` parametrarna och, se en lista över alla [Overview](overview.md) åtgärder som du kan utföra med bild analys.

|Språk | Språkkod | Kategorier | Taggar | Beskrivning | Vuxen | Varumärken | Color (Färg) | Ansikten | ImageType | Objekt | Kändisar | Landmärken |
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
