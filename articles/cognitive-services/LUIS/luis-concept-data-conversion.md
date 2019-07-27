---
title: Data konvertering – LUIS
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: bdce1a49ce6c6531ce344de5aa157717fe72c609
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560802"
---
# <a name="convert-data-format-of-utterances"></a>Konvertera dataformatet för yttranden
LUIS använder Cognitive Services tal-tjänsten för att konvertera yttranden från talat yttranden till text yttranden innan förutsägelse. 

## <a name="speech-to-intent-conversion-concepts"></a>Tal till avsikt konvertering begrepp
Konvertera tal till text i LUIS kan du skicka talat yttranden till en slutpunkt och ta emot svar LUIS förutsägelse. Processen är en integrering av den [tal](https://docs.microsoft.com/azure/cognitive-services/Speech) tjänst med LUIS. Lär dig mer om tal till avsikt med en [själv studie kurs](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Viktiga krav
Du behöver inte skapa en **taligenkänning för Bing** nyckel för den här integreringen. En **Språkförståelse** nyckeln som skapats i Azure portal som fungerar för den här integreringen. Använd inte start nyckeln LUIS.

### <a name="pricing-tier"></a>Prisnivå
Den här integrationen använder en annan [pris](luis-boundaries.md#key-limits) modell än vanliga language Understanding pris nivåer. 

### <a name="quota-usage"></a>Kvotanvändning
Se [viktiga begränsningar](luis-boundaries.md#key-limits) information. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extraherar data](luis-concept-data-extraction.md)

