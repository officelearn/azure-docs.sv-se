---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3775690802c89805ccf9df1ee6d6717a8818213f
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658941"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt känna igen tal från en mikrofoningång och hämta textutskriften från tagna ljud. Det är enkelt att integrera den här funktionen i dina appar eller enheter för vanliga igenkänningsuppgifter, till exempel att överföra konversationer. Den kan också användas för mer komplexa integrationer, som att använda Bot Framework med Tal SDK för att bygga röstassistenter.

Efter att ha uppfyllt några förutsättningar, erkänner tal från en mikrofon tar bara fyra steg:

> [!div class="checklist"]
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa `SpeechRecognizer` ett objekt `SpeechConfig` med objektet ovanifrån.
> * Starta `SpeechRecognizer` igenkänningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera `SpeechRecognitionResult` de returnerade.
