---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 79dc1fe9fffd8c44c97cf68a12bb90655b672577
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577981"
---
<!-- N.B. no header, language-agnostic -->

Den [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) gör det möjligt att identifiera **avsikter från tal**, drivs med Speech-tjänsten och [tjänst för Språkförståelse (LUIS)](https://www.luis.ai/home).

1. Skapa en tal-factory tillhandahåller en prenumerationsnyckel för Language Understanding-tjänsten och [region](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition). Prenumerationsnyckel för Language Understanding-tjänsten kallas **slutpunktsnyckeln** i tjänstdokumentationen för. Du kan inte använda tjänst för Språkförståelse redigering av nyckeln. Se även de **Obs** nedan.

1. Få en avsiktlig Igenkännande från tal fabriken. En identifierare kan använda din enhets standard mikrofon, en ljudström eller ljud från en fil.

1. Få förståelse språkmodellen baserat på din AppId och Lägg till avsikter som du behöver. 

1. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten (inklusive avsikter). Annars kan får ditt program en slutlig avskrift resultatet.

1. Starta taligenkänning. Inleveransen erkännande som kommando eller en fråga erkännande, använda `RecognizeAsync()`, vilket returnerar den första uttryck som känns igen. Igenkänning av tidskrävande använda `StartContinuousRecognitionAsync()` och foga samman på händelser för asynkron igenkänningsresultat.

Se kodfragmenten nedan för taligenkänning scenarier med hjälp av tal-SDK. Ersätt dina egna Språkförståelse prenumerationsnyckel (slutpunktsnyckeln), den [regionen för din prenumeration](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition), och AppId för din avsikt modell i lämpliga platser i exemplen.

> [!NOTE]
> Till skillnad från andra tjänster som stöds av SDK: N för tal, kräver taligenkänning en viss prenumerationsnyckel (slutpunktsnyckeln för Språkförståelse). [Här](https://www.luis.ai) du hittar mer information om teknik för taligenkänning. Hur du skaffar den **slutpunktsnyckeln** beskrivs [här](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-azure-subscription#create-luis-endpoint-key).
