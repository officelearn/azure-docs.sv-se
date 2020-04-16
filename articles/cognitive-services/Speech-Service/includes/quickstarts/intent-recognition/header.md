---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421738"
---
I den här snabbstarten använder du tjänsten [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) och LUIS (Language Understanding) för att känna igen avsikter från ljuddata som hämtas från en mikrofon. Specifikt ska du använda Tal-SDK för att fånga tal och en fördefinierad domän från LUIS för att identifiera avsikter för hemautomatisering, som att slå på och stänga av en lampa. 

Efter att ha uppfyllt några förutsättningar, erkänner tal och identifiera avsikter från en mikrofon tar bara några steg:

> [!div class="checklist"]
>
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa `IntentRecognizer` ett objekt `SpeechConfig` med objektet ovanifrån.
> * Starta `IntentRecognizer` igenkänningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera `IntentRecognitionResult` de returnerade.
