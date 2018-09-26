---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: ab4795325f733d15403b53f027daa4fb8c824cc6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47185158"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) ger det enklaste sättet att använda **talöversättning** i ditt program.
SDK innehåller alla funktioner i tjänsten. Den grundläggande processen för att utföra talöversättning omfattar följande steg:

1. Skapa en speech translation-konfiguration och ge en prenumerationsnyckel för tal-tjänst (eller en autentiseringstoken) och en [region](~/articles/cognitive-services/speech-service/regions.md) som parametrar. Ändra konfigurationen efter behov. Exempel: du kan konfigurera språk för käll- och översättning, samt ange om du vill text eller tal utdata.

1. Skapa en översättning identifierare från speech translation-konfigurationen. Ange en ljud-konfiguration om du vill att känna igen från en annan källa än mikrofonen standard (till exempel ljudström eller ljudfil).

1. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten, samt en syntes händelse för valfritt ljuduppspelning. Annars kan får ditt program endast en slutlig avskrift resultatet.

1. Starta taligenkänning. Inleveransen translation använder den `RecognizeOnceAsync()` metod som returnerar den första identifierade uttryck. För långvariga översättningar använder den `StartContinuousRecognitionAsync()` metoden och koppla upp händelser för asynkron igenkänningsresultat.

Se följande kodstycken för speech translation scenarier som använder SDK: N för tal.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
