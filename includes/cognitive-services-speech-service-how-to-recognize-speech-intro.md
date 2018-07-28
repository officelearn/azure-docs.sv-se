---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ca342a812f6a8de19c732b5df4fab14a825f6c51
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330929"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Den [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) ger det enklaste sättet att använda **tal till Text** i ditt program med full funktionalitet.

1. Skapa en tal-factory tillhandahåller en prenumerationsnyckel för tal-tjänst eller en autentiseringstoken, och en [region](~/articles/cognitive-services/speech-service/regions.md).
   Du kan även konfigurera alternativ, till exempel språket eller en anpassad slutpunkt för dina egna talmodeller för taligenkänning.

1. Få en taligenkänningens från tal fabriken.
   En identifierare kan använda din enhets standard mikrofon, en ljudström eller ljud från en fil.

1. Koppla in händelser för asynkron åtgärd om du vill.
   Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten.
   Annars kan får ditt program en slutlig avskrift resultatet.

1. Starta taligenkänning.
   Inleveransen erkännande som kommando eller en fråga erkännande, använda `RecognizeAsync()`, vilket returnerar den första uttryck som känns igen.
   Igenkänning av tidskrävande, som taltranskription, använda `StartContinuousRecognitionAsync()` och foga samman på händelser för asynkron igenkänningsresultat.

Nedan beskrivs flera kodfragment för tal för scenarier med hjälp av tal-SDK.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
