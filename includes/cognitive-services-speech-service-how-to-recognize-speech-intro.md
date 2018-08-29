---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1103e5a217ca4972cc1c983a7ad0d07b0797e9e9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122646"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Cognitive Services [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) ger det enklaste sättet att använda **tal till Text** i ditt program med full funktionalitet.

1. Skapa en tal-fabrik och ge en prenumerationsnyckel för tal-tjänst (eller en autentiseringstoken) och en [region](~/articles/cognitive-services/speech-service/regions.md) som parametrar. Du kan också ange en anpassad slutpunkt för att ange en tjänstslutpunkt som inte är standard.

1. Få en taligenkänningens från tal fabriken. Du kan konfigurera språket och utdataformat. En identifierare kan använda din enhets standard mikrofon, en ljudström eller ljud från en fil.

1. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten. Annars kan får ditt program endast en slutlig avskrift resultatet.

1. Starta taligenkänning. Inleveransen erkännande, t ex kommando eller en fråga, Använd den `RecognizeAsync()` metoden. Den här metoden returnerar den första identifierade uttryck. Långvariga erkännande som taltranskription, använda den `StartContinuousRecognitionAsync()` metoden. Koppla in händelser för asynkron igenkänningsresultat.

Se följande kodstycken för tal för scenarier som använder SDK: N för tal.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
