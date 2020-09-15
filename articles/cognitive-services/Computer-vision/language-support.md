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
ms.openlocfilehash: 844ef8c09f10e1df888ef4990db22be8705d5c01
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530207"
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

Vissa åtgärder för API: et [analys-image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) kan returnera resultat på andra språk, som anges med `language` Frågeparametern. Andra åtgärder returnerar resultat på engelska oavsett vilket språk som anges, och andra genererar ett undantag för språk som inte stöds. Åtgärder anges med `visualFeatures` `details` parametrarna och, se en lista över alla [Overview](overview.md) åtgärder som du kan utföra med bild analys.

|Språk | Språkkod | Kategorier | Taggar | Description | Vuxen | Varumärken | Color (Färg) | Ansikten | ImageType | Objekt | Kändisar | Landmärken |
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
