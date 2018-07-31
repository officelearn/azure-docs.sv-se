---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: d7d73d9c5e85ac550b24c73797d536dc4d0fc6ed
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346274"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Den [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) ger det enklaste sättet att använda **tal till Text** i ditt program med full funktionalitet.

1. Skapa en tal-factory tillhandahåller en prenumerationsnyckel för tal-tjänst eller en autentiseringstoken, och en [region](~/articles/cognitive-services/speech-service/regions.md). Du kan också ange en anpassad slutpunkt för att ange en tjänstslutpunkt som inte är standard.

1. Få en taligenkänningens från tal fabriken. Du kan konfigurera språket som utdataformat.  En identifierare kan använda din enhets standard mikrofon, en ljudström eller ljud från en fil.

1. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten.  Annars kan får ditt program en slutlig avskrift resultatet.

1. Starta taligenkänning. Inleveransen erkännande som kommando eller en fråga erkännande, använda `RecognizeAsync()`, vilket returnerar den första uttryck som känns igen. Igenkänning av tidskrävande, som taltranskription, använda `StartContinuousRecognitionAsync()` och foga samman på händelser för asynkron igenkänningsresultat.

Se kodfragmenten nedan för tal för scenarier med hjälp av tal-SDK.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
