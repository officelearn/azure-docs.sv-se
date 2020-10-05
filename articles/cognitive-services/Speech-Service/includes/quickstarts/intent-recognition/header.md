---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "81421738"
---
I den här snabb starten använder du tjänsten [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) och language Understanding (Luis) för att identifiera avsikter från ljuddata som fångats från en mikrofon. Mer specifikt använder du talet SDK för att avbilda tal och en fördefinierad domän från LUIS för att identifiera avsikter för start automatisering, som att aktivera och inaktivera ett ljus. 

Efter att ha uppfyllt några krav, tar det bara några steg att känna igen tal och identifiera syften från en mikrofon:

> [!div class="checklist"]
>
> * Skapa ett `SpeechConfig` objekt från din prenumerations nyckel och region.
> * Skapa ett `IntentRecognizer` objekt med hjälp av `SpeechConfig` objektet ovan.
> * `IntentRecognizer`Starta igenkännings processen för en enda uttryck med hjälp av objektet.
> * Granska den `IntentRecognitionResult` returnerade.
