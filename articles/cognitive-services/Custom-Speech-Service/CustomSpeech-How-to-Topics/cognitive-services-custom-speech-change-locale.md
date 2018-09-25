---
title: Stöd för nationella inställningar och språk i Custom Speech Service på Azure | Microsoft Docs
description: Översikt över språk som stöds av Custom Speech Service i Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 1f186681c7e46d2e47ed7eee55c8f61290c48fcb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987534"
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
