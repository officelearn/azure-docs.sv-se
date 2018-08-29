---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: a4accb0846ec9cf5efc5ca5aa00c731edd41d4d4
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143927"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) gör det möjligt att identifiera **avsikter från tal** och stöds av Cognitive Services [tjänst för Språkförståelse (LUIS)](https://www.luis.ai/home).

1. Skapa en tal-fabrik med en LUIS-prenumerationsnyckel och [region](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition) som parametrar. LUIS prenumerationsnyckeln kallas **slutpunktsnyckeln** i tjänstdokumentationen för. Du kan inte använda LUIS redigering av nyckeln. (Se anmärkning senare i det här avsnittet.)

1. Få en avsiktlig Igenkännande från tal fabriken. En identifierare kan använda din enhets standard mikrofon, en ljudström eller ljud från en fil.

1. Hämta så här fungerar språkmodell som baseras på dina **AppId**. Lägg till avsikter som du behöver. 

1. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten (inklusive avsikter). Om du inte sysselsätta händelserna får program bara en slutlig avskrift resultatet.

1. Starta taligenkänning. Inleveransen erkännande, t ex kommando eller en fråga, Använd den `RecognizeAsync()` metoden. Den här metoden returnerar den första identifierade uttryck. Igenkänning av tidskrävande, använda den `StartContinuousRecognitionAsync()` metoden. Koppla in händelser för asynkron igenkänningsresultat.

Se följande kodstycken för taligenkänning scenarier som använder SDK: N för tal. Ersätt värdena i exemplet med din egen LUIS prenumerationsnyckel (slutpunktsnyckeln), den [regionen för din prenumeration](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition), och **AppId** av avsikt modellen.

> [!NOTE]
> Till skillnad från andra tjänster som stöds av SDK: N för tal, kräver taligenkänning en viss prenumerationsnyckel (LUIS slutpunktsnyckeln). Läs om hur teknik för taligenkänning i [LUIS webbplats](https://www.luis.ai). Information om hur du skaffar den **slutpunktsnyckeln**, se [skapa en LUIS-slutpunktsnyckeln](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription#create-luis-endpoint-key).
