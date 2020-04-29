---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400782"
---
I den här snabb starten använder du [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att identifiera tal i ett inaktivt tal från mikrofonen och få text avskriften från insamlade ljud. Det är enkelt att integrera den här funktionen i dina appar eller enheter för vanliga igenkännings aktiviteter, till exempel att skriva över konversationer. Den kan också användas för mer komplexa integreringar, t. ex. med hjälp av bot Framework med tal-SDK för att bygga röst assistenter.

Efter att ha uppfyllt några krav, tar igenkänning av tal från en mikrofon bara fyra steg:

> [!div class="checklist"]
> * Skapa ett `SpeechConfig` objekt från din prenumerations nyckel och region.
> * Skapa ett `SpeechRecognizer` objekt med hjälp `SpeechConfig` av objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av `SpeechRecognizer` objektet.
> * Granska den `SpeechRecognitionResult` returnerade.
