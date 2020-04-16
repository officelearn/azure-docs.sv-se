---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400392"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text för att syntetisera tal. Text-till-tal-tjänsten ger många alternativ för syntetiserade röster, under [stöd för text-till-tal-språk](../../../language-support.md#text-to-speech). Efter att ha uppfyllt några förutsättningar tar renderingssyntiserat tal till standardhögtalarna bara fyra steg:
> [!div class="checklist"]
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa `SpeechSynthesizer` ett objekt `SpeechConfig` med objektet ovanifrån.
> * Använda `SpeechSynthesizer` objektet för att läsa upp texten.
> * Kontrollera `SpeechSynthesisResult` att de returnerade har fel.
