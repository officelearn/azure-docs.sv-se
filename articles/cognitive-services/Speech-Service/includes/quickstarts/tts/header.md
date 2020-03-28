---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961379"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text för att syntetisera tal. Text-till-tal-tjänsten ger många alternativ för syntetiserade röster, under [stöd för text-till-tal-språk](../../../language-support.md#text-to-speech). Efter att ha uppfyllt några förutsättningar tar renderingssyntiserat tal till standardhögtalarna bara fyra steg:
> [!div class="checklist"]
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa `SpeechSynthesizer` ett objekt `SpeechConfig` med objektet ovanifrån.
> * Använda `SpeechSynthesizer` objektet för att läsa upp texten.
> * Kontrollera `SpeechSynthesisResult` att de returnerade har fel.
