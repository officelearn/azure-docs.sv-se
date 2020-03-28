---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900584"
---
I den här snabbstarten använder du tjänsten [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) och LUIS (Language Understanding) för att känna igen avsikter från ljuddata som hämtas från en mikrofon. Specifikt ska du använda Tal-SDK för att fånga tal och en fördefinierad domän från LUIS för att identifiera avsikter för hemautomatisering, som att slå på och stänga av en lampa. 

Efter att ha uppfyllt några förutsättningar, erkänner tal och identifiera avsikter från en mikrofon tar bara några steg:

> [!div class="checklist"]
>
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa `IntentRecognizer` ett objekt `SpeechConfig` med objektet ovanifrån.
> * Starta `IntentRecognizer` igenkänningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera `IntentRecognitionResult` de returnerade.
