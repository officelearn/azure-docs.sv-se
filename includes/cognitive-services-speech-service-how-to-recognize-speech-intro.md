---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 3394fc2f6395799a252b645635d982b4573296c6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47000727"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Cognitive Services [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) ger det enklaste sättet att använda **tal till Text** i ditt program med full funktionalitet.

1. Skapa en tal-konfiguration och ge en prenumerationsnyckel för tal-tjänst (eller en autentiseringstoken) och en [region](~/articles/cognitive-services/speech-service/regions.md) som parametrar. Ändra konfigurationen efter behov. Till exempel ange en anpassad slutpunkt för att ange en tjänstslutpunkt som inte är standard, eller välj talat språk eller utdataformat.

1. Skapa en taligenkänningens från tal-konfigurationen. Ange en ljud-konfiguration om du vill att känna igen från en annan källa än mikrofonen standard (till exempel ljudström eller ljudfil).

1. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten. Annars kan får ditt program endast en slutlig avskrift resultatet.

1. Starta taligenkänning. Inleveransen erkännande, t ex kommando eller en fråga, Använd den `RecognizeOnceAsync()` metoden. Den här metoden returnerar den första identifierade uttryck. Långvariga erkännande som taltranskription, använda den `StartContinuousRecognitionAsync()` metoden. Koppla in händelser för asynkron igenkänningsresultat.

Se följande kodstycken för tal för scenarier som använder SDK: N för tal.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
