---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ee50a104a75d3cd5ff958bd49a1ff7010c5d5083
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144566"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) ger det enklaste sättet att använda **talöversättning** i ditt program.
SDK innehåller alla funktioner i tjänsten. Den grundläggande processen för att utföra talöversättning omfattar följande steg:

1. Skapa en datafabrik med tal och ge en prenumerationsnyckel för tal-tjänst eller en autentiseringstoken och en [region](~/articles/cognitive-services/speech-service/regions.md) som parametrar.
   
1. Skapa en översättning identifierare från tal fabriken. Du kan konfigurera språk för käll- och översättning, samt ange om du vill text eller tal utdata. Det finns olika varianter av översättning identifierare baserat på ljudkälla som du använder.

1. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten, samt en syntes händelse för valfritt ljuduppspelning. Annars kan får ditt program endast en slutlig avskrift resultatet.

1. Starta taligenkänning. Inleveransen translation använder den `RecognizeAsync()` metod som returnerar den första identifierade uttryck. För långvariga översättningar använder den `StartContinuousRecognitionAsync()` metoden och koppla upp händelser för asynkron igenkänningsresultat.

Se följande kodstycken för speech translation scenarier som använder SDK: N för tal.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
