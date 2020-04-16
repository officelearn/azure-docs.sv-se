---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400279"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text för att syntetisera tal i en ljudfil. Text-till-tal-tjänsten ger många alternativ för syntetiserade röster, under [stöd för text-till-tal-språk](../../../language-support.md#text-to-speech). Efter att ha uppfyllt några förutsättningar tar syntetisera tal i en fil bara fem steg:
> [!div class="checklist"]
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa ett ljudkonfigurationsobjekt som anger . WAV-filnamn.
> * Skapa `SpeechSynthesizer` ett objekt med hjälp av konfigurationsobjekten ovanifrån.
> * Med `SpeechSynthesizer` hjälp av objektet konverterar du texten till syntetiserat tal och sparar den i den angivna ljudfilen.
> * Kontrollera `SpeechSynthesizer` att den returnerade för fel.
