---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961568"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text för att syntetisera tal i en ljudfil. Text-till-tal-tjänsten ger många alternativ för syntetiserade röster, under [stöd för text-till-tal-språk](../../../language-support.md#text-to-speech). Efter att ha uppfyllt några förutsättningar tar syntetisera tal i en fil bara fem steg:
> [!div class="checklist"]
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa ett ljudkonfigurationsobjekt som anger . WAV-filnamn.
> * Skapa `SpeechSynthesizer` ett objekt med hjälp av konfigurationsobjekten ovanifrån.
> * Med `SpeechSynthesizer` hjälp av objektet konverterar du texten till syntetiserat tal och sparar den i den angivna ljudfilen.
> * Kontrollera `SpeechSynthesizer` att den returnerade för fel.
