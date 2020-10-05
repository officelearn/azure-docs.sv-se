---
title: 'Kognitiva service SKU: er och priser'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: cb066ecc1dc11985c0ccb0fa687a15d6b038b9fa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321610"
---
Se listan över SKU: er och pris information nedan. 

#### <a name="multi-service"></a>Multi-service

| Tjänst                    | Typ                      |
|----------------------------|---------------------------|
| Flera tjänster. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/) .            | `CognitiveServices`     |


#### <a name="vision"></a>Visuellt innehåll

| Tjänst                    | Typ                      |
|----------------------------|---------------------------|
| Visuellt innehåll            | `ComputerVision`          |
| Custom Vision förutsägelse | `CustomVision.Prediction` |
| Custom Vision-utbildning   | `CustomVision.Training`   |
| Ansikte                       | `Face`                    |
| Formigenkänning            | `FormRecognizer`          |
| Handskriftsigenkänning             | `InkRecognizer`           |

#### <a name="search"></a>Search

| Tjänst            | Typ                  |
|--------------------|-----------------------|
| Automatiska förslag i Bing   | `Bing.Autosuggest.v7` |
| Anpassad sökning i Bing | `Bing.CustomSearch`   |
| Entitetssökning i Bing | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Stavningskontroll i Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Tal

| Tjänst            | Typ                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Taligenkänning | `SpeakerRecognition` |

#### <a name="language"></a>Språk

| Tjänst            | Typ                |
|--------------------|---------------------|
| Formulär förståelse | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Textanalys     | `TextAnalytics`     |
| Textöversättning   | `TextTranslation`   |

#### <a name="decision"></a>Beslut

| Tjänst           | Typ               |
|-------------------|--------------------|
| Avvikelseidentifiering  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personanpassning      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Pris nivåer och fakturering

Pris nivåerna (och den mängd du debiteras) baseras på antalet transaktioner som du skickar med hjälp av autentiseringsinformationen. Varje pris nivå anger:
* maximalt antal tillåtna transaktioner per sekund (TPS).
* tjänst funktioner som Aktiver ATS inom pris nivån.
* kostnad för ett fördefinierat antal transaktioner. Om du fortsätter över det här numret kommer det att leda till en extra avgift som anges i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för din tjänst.

> [!NOTE]
> Många av de Cognitive Services har en kostnads fri nivå som du kan använda för att testa tjänsten. Använd den kostnads fria nivån `F0` som SKU för din resurs.