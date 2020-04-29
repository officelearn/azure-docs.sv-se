---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400279"
---
I den här snabb starten ska du använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text till syntetiskt tal i en ljudfil. Tjänsten för text till tal innehåller flera alternativ för syntetiserade röster under [stöd för text till tal-språk](../../../language-support.md#text-to-speech). Efter att ha uppfyllt några krav, tar det bara att syntetisera tal i en fil fem steg:
> [!div class="checklist"]
> * Skapa ett `SpeechConfig` objekt från din prenumerations nyckel och region.
> * Skapa ett ljud konfigurations objekt som anger. WAV-filnamn.
> * Skapa ett `SpeechSynthesizer` objekt med hjälp av konfigurations objekt från ovan.
> * Konvertera texten `SpeechSynthesizer` till syntetiskt tal med hjälp av objektet och spara den i den angivna ljud filen.
> * Undersök felen `SpeechSynthesizer` som returneras.
