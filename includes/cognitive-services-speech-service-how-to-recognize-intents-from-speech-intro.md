---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1b55576581ebddc90c0af6b99a04dbe66d2e3b87
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330911"
---
<!-- N.B. no header, language-agnostic -->

Den [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) gör det möjligt att identifiera avsikter från tal, drivs av Speech-tjänsten och [tjänst för Språkförståelse (LUIS)](https://luis.ai).

1. Skapa en tal-factory tillhandahåller en prenumerationsnyckel för Language Understanding-tjänsten och [region](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition).


1. Få en avsiktlig Igenkännande från tal fabriken.
   En identifierare kan använda din enhets standard mikrofon, en ljudström eller ljud från en fil.

1. Koppla in händelser för asynkron åtgärd om du vill.
   Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten.
   Annars kan får ditt program en slutlig avskrift resultatet.

1. Starta taligenkänning.
   Inleveransen erkännande som kommando eller en fråga erkännande, använda `RecognizeAsync()`, vilket returnerar den första uttryck som känns igen.
   Igenkänning av tidskrävande, som taltranskription, använda `StartContinuousRecognitionAsync()` och foga samman på händelser för asynkron igenkänningsresultat.

Nedan beskrivs flera kodfragment för taligenkänning scenarier med hjälp av tal-SDK.

> [!NOTE]
> Skaffa en prenumerationsnyckel först.
> Till skillnad från andra tjänster som stöds av SDK: N för tal, kräver taligenkänning en viss prenumeration-nyckel.
> [Här](https://www.luis.ai) du hittar mer information om teknik för taligenkänning, samt information om hur du skaffa en prenumerationsnyckel.
> Ersätt dina egna prenumerationsnyckel för Language Understanding den [regionen för din prenumeration](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition), och AppId för din avsikt modell i lämpliga platser i exemplen.
