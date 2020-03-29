---
title: Datakonvertering - LUIS
titleSuffix: Azure Cognitive Services
description: Läs om hur yttranden kan ändras före förutsägelser i Språkförståelse (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: aeb9094db83b14af988f70485788934a7854200c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68619801"
---
# <a name="convert-data-format-of-utterances"></a>Konvertera dataformat för yttranden
LUIS tillhandahåller följande konverteringar av ett användarutseende före förutsägelse"

* Tal till text med hjälp av [cognitive services-taltjänsten.](../Speech-Service/overview.md) 

## <a name="speech-to-text"></a>Tal till text

Tal till text tillhandahålls som en integrering med LUIS. 

### <a name="intent-conversion-concepts"></a>Begrepp för avsiktskonvertering
Konvertering av tal till text i LUIS gör att du kan skicka talade yttranden till en slutpunkt och få ett LUIS-förutsägelsesvar. Processen är en integrering av [taltjänsten](https://docs.microsoft.com/azure/cognitive-services/Speech) med LUIS. Läs mer om Tal till avsikt med en [självstudiekurs](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Viktiga krav
Du behöver inte skapa en **API-nyckel för Bing-tal** för den här integreringen. En **språk understanding-nyckel** som skapats i Azure-portalen fungerar för den här integrationen. Använd inte STARTknappen LUIS.

### <a name="pricing-tier"></a>Prisnivå
Den här integreringen använder en annan [prismodell](luis-boundaries.md#key-limits) än de vanliga prisnivåerna för språkbeståe. 

### <a name="quota-usage"></a>Kvotanvändning
Se [Nyckelgränser](luis-boundaries.md#key-limits) för information. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera data](luis-concept-data-extraction.md)

