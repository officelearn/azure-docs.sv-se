---
title: Stöd för nationella inställningar och språk i Custom Speech Service på Azure | Microsoft Docs
description: Översikt över språk som stöds av Custom Speech Service i Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: nitinme
ms.openlocfilehash: c40cf951e59094470443d7244052e6214aaeba82
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864272"
---
# <a name="supported-locales-in-custom-speech-service"></a>Nationella inställningar som stöds i Custom Speech Service

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Custom Speech Service stöder för närvarande anpassning av modeller i följande språk:

| Modelltypen | Stöd för språk |
|----|-----|
| Akustiska modeller | Amerikansk engelska (en-US) |
| Språkmodeller | Amerikansk engelska (en-US), kinesiska (zh-CN) |

Även om anpassning av akustisk modell stöds endast på engelska (USA), har importera kinesiska akustiska data stöd för offline testningen av anpassade modeller för kinesiska språk.

Du måste välja rätt nationella inställningar innan du vidtar åtgärder. De aktuella nationella inställningarna anges i tabellrubriken på alla sidor för data, modeller och distribution. Om du vill ändra de nationella inställningarna, klicka på ”Ändra språket” finns under tabellens rubrik. Detta tar dig till en bekräftelsesida för nationella inställningar. Klicka på ”OK” för att gå tillbaka till tabellen.

Du bör följa upp med nästa steg
* Lär dig [så här skapar du en anpassad akustisk modell](cognitive-services-custom-speech-create-acoustic-model.md) att förbättra noggrannheten
* Lär dig [så här skapar du en anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md) att förbättra din taligenkänningen
* Följ den [avskrift riktlinjer](cognitive-services-custom-speech-transcription-guidelines.md) för att förbereda dina data
