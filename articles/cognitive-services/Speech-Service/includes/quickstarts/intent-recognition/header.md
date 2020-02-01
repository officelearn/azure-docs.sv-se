---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900584"
---
I den här snabb starten använder du tjänsten [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) och language Understanding (Luis) för att identifiera avsikter från ljuddata som fångats från en mikrofon. Mer specifikt använder du talet SDK för att avbilda tal och en fördefinierad domän från LUIS för att identifiera avsikter för start automatisering, som att aktivera och inaktivera ett ljus. 

Efter att ha uppfyllt några krav, tar det bara några steg att känna igen tal och identifiera syften från en mikrofon:

> [!div class="checklist"]
>
> * Skapa ett `SpeechConfig`-objekt från din prenumerations nyckel och region.
> * Skapa ett `IntentRecognizer`-objekt med hjälp av `SpeechConfig`-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av `IntentRecognizer`-objektet.
> * Granska `IntentRecognitionResult` som returneras.
