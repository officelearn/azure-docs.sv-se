---
title: Nationella inställningar som stöds och språk – Custom Speech Service
titlesuffix: Azure Cognitive Services
description: Översikt över språk som stöds av Custom Speech Service i Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 34af6673689244364ab3a1fe3f2a6ab056ea8598
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223963"
---
# <a name="supported-locales-in-custom-speech-service"></a>Nationella inställningar som stöds i Custom Speech Service
Custom Speech Service stöder för närvarande anpassning av modeller i följande språk:

| Modelltypen | Språkstöd |
|----|-----|
| Akustiska modeller | Amerikansk engelska (en-US) |
| Språkmodeller | Amerikansk engelska (en-US), kinesiska (zh-CN) |

Även om anpassning av akustisk modell stöds endast på engelska (USA), har importera kinesiska akustiska data stöd för offline testningen av anpassade modeller för kinesiska språk.

Du måste välja rätt nationella inställningar innan du vidtar åtgärder. De aktuella nationella inställningarna anges i tabellrubriken på alla sidor för data, modeller och distribution. Om du vill ändra de nationella inställningarna, klicka på ”Ändra språket” finns under tabellens rubrik. Detta tar dig till en bekräftelsesida för nationella inställningar. Klicka på ”OK” för att gå tillbaka till tabellen.

Du bör följa upp med nästa steg
* Lär dig [så här skapar du en anpassad akustisk modell](cognitive-services-custom-speech-create-acoustic-model.md) att förbättra noggrannheten
* Lär dig [så här skapar du en anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md) att förbättra din taligenkänningen
* Följ den [avskrift riktlinjer](cognitive-services-custom-speech-transcription-guidelines.md) för att förbereda dina data
