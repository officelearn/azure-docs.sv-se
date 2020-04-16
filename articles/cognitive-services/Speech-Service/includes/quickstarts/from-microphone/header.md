---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400782"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt känna igen tal från en mikrofoningång och hämta textutskriften från tagna ljud. Det är enkelt att integrera den här funktionen i dina appar eller enheter för vanliga igenkänningsuppgifter, till exempel att överföra konversationer. Den kan också användas för mer komplexa integrationer, som att använda Bot Framework med Tal SDK för att bygga röstassistenter.

Efter att ha uppfyllt några förutsättningar, erkänner tal från en mikrofon tar bara fyra steg:

> [!div class="checklist"]
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa `SpeechRecognizer` ett objekt `SpeechConfig` med objektet ovanifrån.
> * Starta `SpeechRecognizer` igenkänningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera `SpeechRecognitionResult` de returnerade.
