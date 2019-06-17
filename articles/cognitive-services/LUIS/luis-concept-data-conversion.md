---
title: Datakonvertering
titleSuffix: Language Understanding - Azure Cognitive Services
description: Lär dig hur du kan ändra yttranden innan förutsägelser i Språkförståelse (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: a148c849d0935978f049e01dd254c4c18800ee3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66496986"
---
# <a name="convert-data-format-of-utterances"></a>Konvertera dataformatet för yttranden
LUIS använder Cognitive Services tal-tjänsten för att konvertera yttranden från talat yttranden till text yttranden innan förutsägelse. 

## <a name="speech-to-intent-conversion-concepts"></a>Tal till avsikt konvertering begrepp
Konvertera tal till text i LUIS kan du skicka talat yttranden till en slutpunkt och ta emot svar LUIS förutsägelse. Processen är en integrering av den [tal](https://docs.microsoft.com/azure/cognitive-services/Speech) tjänst med LUIS. Läs mer om tal till avsikt med en [självstudien](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Viktiga krav
Du behöver inte skapa en **taligenkänning för Bing** nyckel för den här integreringen. En **Språkförståelse** nyckeln som skapats i Azure portal som fungerar för den här integreringen. Använd inte nyckeln LUIS starter.

### <a name="pricing-tier"></a>Prisnivå
Den här integreringen använder en annan [priser](luis-boundaries.md#key-limits) modeller än vanligt Språkförståelse som prisnivåer. 

### <a name="quota-usage"></a>Kvotanvändning
Se [viktiga begränsningar](luis-boundaries.md#key-limits) information. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera data](luis-concept-data-extraction.md)

