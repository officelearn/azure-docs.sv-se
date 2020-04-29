---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400392"
---
I den här snabb starten ska du använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text till syntetiskt tal. Tjänsten för text till tal innehåller flera alternativ för syntetiserade röster under [stöd för text till tal-språk](../../../language-support.md#text-to-speech). Efter att ha uppfyllt några krav, tar åter givningen av syntetiskt tal till standard högtalarna bara fyra steg:
> [!div class="checklist"]
> * Skapa ett `SpeechConfig` objekt från din prenumerations nyckel och region.
> * Skapa ett `SpeechSynthesizer` objekt med hjälp `SpeechConfig` av objektet ovan.
> * Använder `SpeechSynthesizer` objektet för att tala texten.
> * Kontrol lera `SpeechSynthesisResult` felen som returnerades.
