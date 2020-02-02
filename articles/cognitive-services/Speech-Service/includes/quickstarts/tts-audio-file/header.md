---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961568"
---
I den här snabb starten ska du använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text till syntetiskt tal i en ljudfil. Tjänsten för text till tal innehåller flera alternativ för syntetiserade röster under [stöd för text till tal-språk](../../../language-support.md#text-to-speech). Efter att ha uppfyllt några krav, tar det bara att syntetisera tal i en fil fem steg:
> [!div class="checklist"]
> * Skapa ett `SpeechConfig`-objekt från din prenumerations nyckel och region.
> * Skapa ett ljud konfigurations objekt som anger. WAV-filnamn.
> * Skapa ett `SpeechSynthesizer`-objekt med hjälp av konfigurations objekt från ovan.
> * Använd `SpeechSynthesizer`-objektet, konvertera texten till syntetiskt tal och spara den i den angivna ljud filen.
> * Granska `SpeechSynthesizer` som returnerats för fel.
