---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: b46eba502462e0cdbcb222082c5c885f84a8d1e5
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090682"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Cognitive Services [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) ger det enklaste sättet att använda **tal till Text** i ditt program med full funktionalitet.

1. Skapa en tal-fabrik och ge en prenumerationsnyckel för tal-tjänst (eller en autentiseringstoken) och en [region](~/articles/cognitive-services/speech-service/regions.md) som parametrar. Du kan också ange en anpassad slutpunkt för att ange en tjänstslutpunkt som inte är standard.

1. Få en taligenkänningens från tal fabriken. Du kan konfigurera språket och utdataformat. En identifierare kan använda din enhets standard mikrofon, en ljudström eller ljud från en fil.

1. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten. Annars kan får ditt program endast en slutlig avskrift resultatet.

1. Starta taligenkänning. Inleveransen erkännande, t ex kommando eller en fråga, Använd den `RecognizeAsync()` metoden. Den här metoden returnerar den första identifierade uttryck. Långvariga erkännande som taltranskription, använda den `StartContinuousRecognitionAsync()` metoden. Koppla in händelser för asynkron igenkänningsresultat.

Se följande kodstycken för tal för scenarier som använder SDK: N för tal.

[!include[Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
