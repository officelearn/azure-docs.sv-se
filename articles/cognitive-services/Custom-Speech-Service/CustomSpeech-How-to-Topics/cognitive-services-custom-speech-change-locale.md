---
title: Nationella inställningar och språk som stöds i anpassade tal Service i Azure | Microsoft Docs
description: Översikt över språk som stöds av anpassade tal Service i kognitiva tjänster.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: c378fd951f9cd04884f44fbec5accb5d9a886bfe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351405"
---
# <a name="supported-locales-in-custom-speech-service"></a>Språk i anpassade tal Service
Anpassad tal tjänsten stöder för närvarande anpassning av modeller i följande språk:

| Modelltypen | Språkstöd |
|----|-----|
| Ljud modeller | Engelska (en-US) |
| Språk modeller | Engelska (en-US), kinesiska (zh-CN) |

Även om ljud modellen anpassning stöds endast i amerikansk engelska, kinesiska ljud dataimport stöds i syfte att offline testning av anpassade kinesiska språk-modeller.

Lämpliga språk måste väljas innan du vidtar någon åtgärd. Språket anges i tabellen titeln på alla data, modell och distribution av sidor. Klicka på knappen ”Ändra språk” som finns under rubriken i tabell om du vill ändra språk. Detta leder till en bekräftelsesida för språk. Klicka på ”OK” för att gå tillbaka till tabellen.

Du bör följa upp med nästa steg
* Läs [hur du skapar en anpassad ljud modell](cognitive-services-custom-speech-create-acoustic-model.md) att förbättra taligenkänning
* Läs [hur du skapar en anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md) att förbättra din taligenkänningen
* Följ den [skrivfel riktlinjer](cognitive-services-custom-speech-transcription-guidelines.md) för att förbereda dina data
