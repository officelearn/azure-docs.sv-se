---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 74b9da1b3b81f9f35f231ef5caef8eafedc9aefc
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343040"
---
<!-- N.B. no header, language-agnostic -->

Den [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) ger det enklaste sättet att använda **talöversättning** i ditt program.
SDK innehåller alla funktioner i tjänsten. Den grundläggande processen för att utföra talöversättning omfattar följande steg:

1. Skapa en tal-factory tillhandahåller en prenumerationsnyckel för tal-tjänst eller en autentiseringstoken, och en [region](~/articles/cognitive-services/speech-service/regions.md).
   
1. Skapa en översättning identifierare från tal fabriken. Du kan konfigurera språk för käll- och översättning, samt att ange om du vill att text eller tal utdata. Det finns olika varianter av översättning identifierare baserat på ljudkälla som du använder.

1. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten, samt en syntes händelse för valfritt ljuduppspelning. Annars kan får ditt program en slutlig avskrift resultatet.

1. Starta taligenkänning. För inleveransen translation RecognizeAsync() som returnerar den första uttryck som känns igen. För långvariga översättningar använder StartContinuousRecognitionAsync() och foga samman på händelser för asynkron igenkänningsresultat.

Se kodfragmenten nedan för speech translation-scenarier med hjälp av tal-SDK.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
