---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961379"
---
I den här snabb starten ska du använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text till syntetiskt tal. Tjänsten för text till tal innehåller flera alternativ för syntetiserade röster under [stöd för text till tal-språk](../../../language-support.md#text-to-speech). Efter att ha uppfyllt några krav, tar åter givningen av syntetiskt tal till standard högtalarna bara fyra steg:
> [!div class="checklist"]
> * Skapa ett `SpeechConfig`-objekt från din prenumerations nyckel och region.
> * Skapa ett `SpeechSynthesizer`-objekt med hjälp av `SpeechConfig`-objektet ovan.
> * Använd `SpeechSynthesizer`-objektet för att tala texten.
> * Kontrol lera `SpeechSynthesisResult` returnerade för fel.
